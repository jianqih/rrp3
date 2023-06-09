# 宗族文化 {#culture}



对于宗族文化的研究近些年一直是较为火热的研究热点[@Cao2022],[@Hanetsu2019a],[@ZhangShinYi2021a],[@ChenAkiraKai2018],[@ZhangKawagawa2017],[@ZHANG2020100]和[@FAN2023457]。但对于宗族文化的测度方法又各具差异，比如 @ZHANG2020100, @Cao2022 和 @FAN2023457 都是使用上海古籍出版社的县地方族谱数据来测量宗族文化，@ZhangKawagawa2017 使用的是CFPS的数据来测量；@ZhangShinYi2021a 使用的是地方的前三姓氏来作为度量，数据来源是2005年的1%人口抽样调查数据。数据质量上，直观感受是上海古籍出版社的数据会优于其他几个。



## 数据载入

如何在R中没有任何资源的前提下进行关于宗族文化的测度，我先试了做法最为简便的，城市的前三姓氏我翻遍了所有变量都没找到姓氏的变量；后面又看了下上海古籍出版社数据，数据量太大，估计需要爬虫等黑科技，遂又放弃，之后只有选择CFPS，之后也并不很顺利。


```r
library(haven)
```


![](image/q1.png)



```r
cfps2010comm <- read_dta("/Users/a182501/rproject/cfps/data/社区数据cfps/cfps2010comm_201906.dta")
cfps2010comm%>%
  select(cid,provcd,countyid,cyear,cmonth,ca3_s_6,ca3_s_7)->df1
head(df1)
```

```
## # A tibble: 6 × 7
##   cid       provcd        countyid  cyear     cmonth ca3_s_6            ca3_s_7 
##   <dbl+lbl> <dbl+lbl>     <dbl+lbl> <dbl+lbl>  <dbl> <dbl+lbl>          <dbl+lb>
## 1 13200     12 [天津市]    79       2010          10 -8 [不适用]      … -8 [不…
## 2 13190     12 [天津市]    79       2010          10  9 [老年活动场所/… 13 [村/…
## 3 12780     14 [山西省]    69       2010          10  8 [教堂/清真寺] … 10 [敬…
## 4 21340     44 [广东省]   116       2010          10  9 [老年活动场所/… 13 [村/…
## 5 12260     23 [黑龙江省]  56       2010           9 -8 [不适用]      … -8 [不…
## 6 21640     44 [广东省]   123       2010          10  7 [家族祠堂]    … 11 [体…
```

```r
dim(df1)
```

```
## [1] 635   7
```



```r
cfps2010comm$ca3_s_6[which(df1$ca3_s_6==-8)]=0
cfps2010comm$ca3_s_7[which(df1$ca3_s_7==-8)]=0
table(cfps2010comm$ca3_s_7)
```

```
## 
##   0   2   7   8   9  10  11  12  13  14  15 
## 262   2  14   6  41  26  66  26 106  68  18
```

```r
table(cfps2010comm$ca3_s_6)
```

```
## 
##   0   2   3   5   6   7   8   9  10  11  12  13  14  15 
## 172   1   1   1  47  23  23  92  24  58  21  94  72   6
```

```r
na.omit(df1)
```

```
## # A tibble: 635 × 7
##    cid       provcd        countyid  cyear     cmonth ca3_s_6           ca3_s_7 
##    <dbl+lbl> <dbl+lbl>     <dbl+lbl> <dbl+lbl>  <dbl> <dbl+lbl>         <dbl+lb>
##  1 13200     12 [天津市]    79       2010          10 -8 [不适用]     … -8 [不…
##  2 13190     12 [天津市]    79       2010          10  9 [老年活动场所… 13 [村/…
##  3 12780     14 [山西省]    69       2010          10  8 [教堂/清真寺]… 10 [敬…
##  4 21340     44 [广东省]   116       2010          10  9 [老年活动场所… 13 [村/…
##  5 12260     23 [黑龙江省]  56       2010           9 -8 [不适用]     … -8 [不…
##  6 21640     44 [广东省]   123       2010          10  7 [家族祠堂]   … 11 [体…
##  7 21730     44 [广东省]   126       2010          10 -8 [不适用]     … -8 [不…
##  8 22523     62 [甘肃省]   145       2010           9 -8 [不适用]     … -8 [不…
##  9 10930     52 [贵州省]    24       2010          10 12 [儿童游乐场所… 13 [村/…
## 10 10100     34 [安徽省]     3       2010          10  6 [庙宇/道观]  … 10 [敬…
## # … with 625 more rows
```

```r
dim(df1)
```

```
## [1] 635   7
```



根据社区问卷手册，我们可以指导


```r
library(dplyr)
cfps2010comm%>%
  group_by(provcd)%>%
  dplyr::summarise(x1=sum(ca3_s_6),x2=sum(ca3_s_7))->df2
df2
```

```
## # A tibble: 25 × 3
##    provcd           x1    x2
##    <dbl+lbl>     <dbl> <dbl>
##  1 11 [北京市]      27    14
##  2 12 [天津市]       9    13
##  3 13 [河北省]     216   178
##  4 14 [山西省]     207   220
##  5 21 [辽宁省]     525   511
##  6 22 [吉林省]      97   112
##  7 23 [黑龙江省]   159   155
##  8 31 [上海市]     495   386
##  9 32 [江苏省]     127   117
## 10 33 [浙江省]     111   126
## # … with 15 more rows
```


## 可视化



```r
library(ggplot2)
ggplot(df2)+
  geom_point(aes(x=x1,y=x2))+
  geom_smooth(method = 'lm',aes(x=x1,y=x2))
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

<div class="figure" style="text-align: center">
<img src="03-clan_files/figure-epub3/as-1.png" alt="祠堂与族谱"  />
<p class="caption">(\#fig:as)祠堂与族谱</p>
</div>


## 地图

将论文图表绘制在图上。


```r
d <- attributes(df2$provcd)$labels
d <- as.data.frame(d)
d2 <- rownames(d)
d3 <- cbind(d,d2)
colnames(d3) <- c("provcd","label")
d4 <- merge(df2,d3,by = "provcd")
d4
```

```
##    provcd  x1  x2          label
## 1      11  27  14         北京市
## 2      12   9  13         天津市
## 3      13 216 178         河北省
## 4      14 207 220         山西省
## 5      21 525 511         辽宁省
## 6      22  97 112         吉林省
## 7      23 159 155       黑龙江省
## 8      31 495 386         上海市
## 9      32 127 117         江苏省
## 10     33 111 126         浙江省
## 11     34 123 113         安徽省
## 12     35  47  63         福建省
## 13     36 102 107         江西省
## 14     37 220 125         山东省
## 15     41 538 453         河南省
## 16     42  76  90         湖北省
## 17     43 190 147         湖南省
## 18     44 493 566         广东省
## 19     45 107  35 广西壮族自治区
## 20     50  66  59         重庆市
## 21     51 155 149         四川省
## 22     52  88  71         贵州省
## 23     53 147 141         云南省
## 24     61  73  58         陕西省
## 25     62 517 408         甘肃省
```




json数据来源于[阿里 DataV 数据可视化平台](http://datav.aliyun.com/portal/school/atlas/area_selector)，能够在多个行政层级绘制中国地图。



```r
library(echarts4r.maps)
library(echarts4r)
colnames(d4) <- c("provcd","value1","value2","region")
china_map <- jsonlite::read_json("rep.json")
d4 %>%
  e_charts(region)%>%
  e_map_register("China2", china_map) %>%
  e_map(value1, map = "China2") %>%
  e_visual_map(value1)
```

![](03-clan_files/figure-epub3/unnamed-chunk-6-1.png)<!-- -->





```r
d4 %>%
  e_charts(region)%>%
  e_map_register("China2", china_map) %>%
  e_map(value2, map = "China2") %>%
  e_visual_map(value2)
```

![](03-clan_files/figure-epub3/unnamed-chunk-7-1.png)<!-- -->


上面的数据还是挺让人吃惊的，一般会认为宗族文化会在南方更为发达，包括修建祠堂上，我们通过图 \@ref(fig:as) 中知道祠堂与家谱是基本上在省层面是正相关的，但地域上呈现了较大的差异。可能是与抽样方法有关，需要进一步的处理。


## 其他数据源

目前学界用的较为广泛的是通过上海家族族谱来测算宗族文化，也就是看一个地方的族谱的密度来作为宗族文化的代理变量，代表性学者有浙大的[张川川老师](https://scholar.google.com/citations?user=_YWE1C4AAAAJ&hl=en&oi=ao)，他目前发表的关于宗族文化的论文有[@Cao2022], [@ZHANG2020100], [@ZhangKawagawa2017]。
很巧，他和合作者[Yiqin Xu](https://yiqingxu.org/)和博士生曹家瑞在JDE刊发的论文有[replicate file](https://yiqingxu.org/papers/english/2022_famine/replication.zip)(可直接下载)


但图中的图是使用ArcGIS来实现的，这里试图通过R来进行复刻。



```r
clan_gbook <- read_dta("/Users/a182501/stata-replicat/replication/datafiles/gbooks_byyear.dta")
head(clan_gbook)
```

```
## # A tibble: 6 × 2
##    year year_imp
##   <dbl>    <dbl>
## 1   970      970
## 2  1430     1430
## 3  1800     1800
## 4  1880     1880
## 5  1890     1890
## 6  1900     1900
```

```r
p <- clan_gbook%>%
  dplyr::filter(year<=2010&year>=1400)%>%
  ggplot()+
    geom_histogram(aes(year_imp),binwidth = 1)

p+geom_vline(aes(xintercept=1950), colour="#BB0000",size = 0.2)+
  geom_vline(aes(xintercept=1980), colour="#BB0000",size = 0.2)+xlab("Year")+ylab("Frequency")
```

```
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.
```

![](03-clan_files/figure-epub3/unnamed-chunk-8-1.png)<!-- -->

在统计上的大小与原作者给出的频率有一定的差异，不过形状是相同的。




```r
library(mapchina)
library(sysfonts)
library(showtextdb)
library(showtext)
library(sf)
```

```
## Linking to GEOS 3.11.0, GDAL 3.5.3, PROJ 9.1.0; sf_use_s2() is TRUE
```

```r
library(haven)
clan_disr <- read_dta("/Users/a182501/stata-replicat/replication/datafiles/clan_distr.dta")
arrange(clan_disr,provcd)
```

```
## # A tibble: 1,145 × 3
##    provcd lnzupunum50 countycode
##     <dbl>       <dbl>      <dbl>
##  1     13      0.0421          1
##  2     13      0               3
##  3     13      0               9
##  4     13      0              18
##  5     13      0.105          23
##  6     13      0.203          26
##  7     13      0.310          42
##  8     13      0.160          44
##  9     13      0.0281         48
## 10     13      0              70
## # … with 1,135 more rows
```



```r
head(china)
```

```
## Simple feature collection with 6 features and 13 fields
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: 115.4248 ymin: 39.44473 xmax: 116.8805 ymax: 41.05936
## Geodetic CRS:  WGS 84
## # A tibble: 6 × 14
##   Code_…¹ Code_…² Code_…³ Name_…⁴ Name_…⁵ Name_…⁶ Pinyin Pop_2…⁷ Pop_2…⁸ Pop_2…⁹
##   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>    <dbl>   <dbl>   <dbl>
## 1 110101  1101    11      北京市  <NA>    东城区  Dōngc…  881763  919253      NA
## 2 110102  1101    11      北京市  <NA>    西城区  Xīché… 1232823 1243315      NA
## 3 110114  1101    11      北京市  <NA>    昌平区  Chāng…  614821 1660501      NA
## 4 110115  1101    11      北京市  <NA>    大兴区  Dàxīn…  671444 1365112      NA
## 5 110111  1101    11      北京市  <NA>    房山区  Fángs…  814367  944832      NA
## 6 110116  1101    11      北京市  <NA>    怀柔区  Huáir…  296002  372887      NA
## # … with 4 more variables: Pop_2018 <dbl>, Area <dbl>, Density <dbl>,
## #   geometry <MULTIPOLYGON [°]>, and abbreviated variable names ¹​Code_County,
## #   ²​Code_Perfecture, ³​Code_Province, ⁴​Name_Province, ⁵​Name_Perfecture,
## #   ⁶​Name_County, ⁷​Pop_2000, ⁸​Pop_2010, ⁹​Pop_2017
```



## 重新再利用

尽管我们没有关于县级层面的宏观经济等控制变量，但我们可以将获得的数据反向匹配给到个人，看宗族祠堂对于个人的影响是如何呈现的。

我们这里选择使用cfps2014年的数据，处理方法基本上与2010年一致。


```r
cfps2014comm <- read_dta("/Users/a182501/rproject/cfps/data/cfps/2014/cfps2014comm_201906.dta")
cfps2014comm$ca3_s_6[which(cfps2014comm$ca3_s_6==-8)] <- 0
cfps2014comm$ca3_s_7[which(cfps2014comm$ca3_s_7==-8)] <- 0
#View(cfps2014comm)随时观察变量
cfps2014comm%>%
  select(cid14,cid10,ca3_s_6,ca3_s_7)->comm14
head(comm14)
```

```
## # A tibble: 6 × 4
##   cid14     cid10     ca3_s_6    ca3_s_7               
##   <dbl+lbl> <dbl+lbl> <dbl+lbl>  <dbl+lbl>             
## 1 118100    11810     0           0                    
## 2 118200    11820     0           0                    
## 3 212300    21230     7 [通公路] 12 [实施村/居直接选举]
## 4 209100    20910     0           0                    
## 5 118300    11830     0           0                    
## 6 118400    11840     0           0
```


调用家户数据


```r
famconf14 <- read_dta("/Users/a182501/rproject/cfps/data/cfps/2014/cfps2014famconf_170630.dta")
head(famconf14)
```

```
## # A tibble: 6 × 307
##   fid14     fid12           fid10         provcd14 count…¹ cid14  urban14 pid   
##   <dbl+lbl> <dbl+lbl>       <dbl+lbl>     <dbl+lb> <dbl+l> <dbl+> <dbl+l> <dbl+>
## 1 100051        -8 [不适用]     -8 [不适… 11 [北…  45     624942 1 [城… 1.00e8
## 2 100051        -8 [不适用]     -8 [不适… 11 [北…  45     624942 1 [城… 1.00e8
## 3 100051    110043          110043      … 11 [北…  45     624942 1 [城… 1.10e8
## 4 100125    110147          110147      … 11 [北… 170     564346 1 [城… 1.10e8
## 5 100160    120009          120009      … 12 [天…  79     131700 1 [城… 1.20e8
## 6 100286    130005          130005      … 13 [河… 237     161210 1 [城… 1.30e8
## # … with 299 more variables: code_a_p <dbl+lbl>, tb2_a_p <dbl+lbl>,
## #   tb1y_a_p <dbl+lbl>, tb1m_a_p <dbl+lbl>, tb1a_a_p <dbl+lbl>,
## #   tb3_a14_p <dbl+lbl>, tb4_a14_p <dbl+lbl>, alive_a14_p <dbl+lbl>,
## #   ta4y_a14_p <dbl+lbl>, ta4m_a14_p <dbl+lbl>, ta401_a14_p <chr>,
## #   qa301_a14_p <dbl+lbl>, qa302_a14_p <dbl+lbl>, tb6_a14_p <dbl+lbl>,
## #   tb601_a14_p <dbl+lbl>, co_a14_p <dbl+lbl>, outpers_where14_p <dbl+lbl>,
## #   tb602acode_a14_p <dbl+lbl>, cfps2014_interv_p <dbl+lbl>, …
```

使用左连接`left_join`以保留我们的家户信息，用村居样本代码`cid14`来进行匹配。


```r
library(visdat)

famcon14_clan <- left_join(famconf14,comm14,by="cid14")
dim(famcon14_clan)
```

```
## [1] 57734   310
```

```r
dim(famconf14)
```

```
## [1] 57734   307
```

```r
#View(famcon14_clan)
famcon14_clan%>%
  select(cid14,cid10,ca3_s_6,ca3_s_7,tb2_a_p,tb1y_a_p,cfps2014_interv_p,tb4_a14_p,urban14,tb4_a14_f)%>%
  dplyr::filter(urban14==0)%>%
  dplyr::filter(tb4_a14_p!=-8)%>%
  dplyr::filter(tb4_a14_p!=-9)%>%
  dplyr::filter(!is.na(cid10))%>%
  mutate(age=2014-tb1y_a_p)%>%
  
  mutate(eduyear = case_when(
    tb4_a14_p==8 ~ 23,
    tb4_a14_p==7 ~ 19,
    tb4_a14_p==6 ~ 16,
    tb4_a14_p==5 ~ 15,
    tb4_a14_p==4 ~ 12,
    tb4_a14_p==3 ~ 9,
    tb4_a14_p==2 ~ 6,
    tb4_a14_p==1 ~ 0
  ))%>%
  mutate(temple_dummy = case_when(
    ca3_s_6==0~0,
    ca3_s_6>0~1
  ))%>%
  mutate(genealogy_dummy = case_when(
    ca3_s_7 == 0~0,
    ca3_s_7 > 0~1
  ))%>%
  mutate(eduyear_fa = case_when(
    tb4_a14_f==8 ~ 23,
    tb4_a14_f==7 ~ 19,
    tb4_a14_f==6 ~ 16,
    tb4_a14_f==5 ~ 15,
    tb4_a14_f==4 ~ 12,
    tb4_a14_f==3 ~ 9,
    tb4_a14_f==2 ~ 6,
    tb4_a14_f==1 ~ 0
  ))-> facon14_clan_clean
```


```r
dim(facon14_clan_clean)
```

```
## [1] 29190    15
```

```r
sum(table(facon14_clan_clean$eduyear))
```

```
## [1] 27780
```

```r
table(facon14_clan_clean$eduyear_fa)
```

```
## 
##    0    6    9   12   15   16   19   23 
## 9139 6435 5176 1850  284  109    4    1
```

```r
table(facon14_clan_clean$temple_dummy)
```

```
## 
##     0     1 
## 28033  1157
```



```r
colnames(facon14_clan_clean)
```

```
##  [1] "cid14"             "cid10"             "ca3_s_6"          
##  [4] "ca3_s_7"           "tb2_a_p"           "tb1y_a_p"         
##  [7] "cfps2014_interv_p" "tb4_a14_p"         "urban14"          
## [10] "tb4_a14_f"         "age"               "eduyear"          
## [13] "temple_dummy"      "genealogy_dummy"   "eduyear_fa"
```

```r
reg_temple <- lm(data = facon14_clan_clean,eduyear~temple_dummy+age+tb2_a_p)# 控制母亲的受教育水平/父亲的受教育水平
summary(reg_temple)
```

```
## 
## Call:
## lm(formula = eduyear ~ temple_dummy + age + tb2_a_p, data = facon14_clan_clean)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.9472 -4.8059  0.3229  3.3196 14.2842 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   4.6291262  0.0459993 100.635   <2e-16 ***
## temple_dummy -0.2351321  0.1448925  -1.623    0.105    
## age           0.0033346  0.0003746   8.903   <2e-16 ***
## tb2_a_p       0.9879641  0.0534791  18.474   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.714 on 27776 degrees of freedom
##   (1410 observations deleted due to missingness)
## Multiple R-squared:  0.0123,	Adjusted R-squared:  0.0122 
## F-statistic: 115.3 on 3 and 27776 DF,  p-value: < 2.2e-16
```


```r
reg_genealogy <- lm(data = facon14_clan_clean,eduyear~genealogy_dummy+age+tb2_a_p)
summary(reg_genealogy)
```

```
## 
## Call:
## lm(formula = eduyear ~ genealogy_dummy + age + tb2_a_p, data = facon14_clan_clean)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.9464 -4.8052  0.3202  3.3202 14.2848 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      4.6285474  0.0459432 100.745   <2e-16 ***
## genealogy_dummy -0.2627547  0.1583737  -1.659   0.0971 .  
## age              0.0033325  0.0003746   8.897   <2e-16 ***
## tb2_a_p          0.9879593  0.0534790  18.474   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.714 on 27776 degrees of freedom
##   (1410 observations deleted due to missingness)
## Multiple R-squared:  0.01231,	Adjusted R-squared:  0.0122 
## F-statistic: 115.4 on 3 and 27776 DF,  p-value: < 2.2e-16
```


还是对族谱的回归会微弱显著，不过都是负的系数，和一些学者之前研究的结论有一些差别，但可能是在这里控制变量控制的不够，可能存在内生性问题，比如遗漏一些关键的控制变量，受访者的智力水平、家庭规模，父亲的政治背景、教育理念、文化资本等。


不过个人认为这里可以使用族谱的数量，并不需要将其转变为虚拟变量。



```r
reg_genealogy_cont <- lm(data = facon14_clan_clean,eduyear~ca3_s_7+age+tb2_a_p)
summary(reg_genealogy_cont)
```

```
## 
## Call:
## lm(formula = eduyear ~ ca3_s_7 + age + tb2_a_p, data = facon14_clan_clean)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.9476 -4.8062  0.3191  3.3191 14.2838 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  4.6294935  0.0459205 100.815   <2e-16 ***
## ca3_s_7     -0.0323995  0.0169295  -1.914   0.0557 .  
## age          0.0033338  0.0003746   8.901   <2e-16 ***
## tb2_a_p      0.9880811  0.0534782  18.476   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.713 on 27776 degrees of freedom
##   (1410 observations deleted due to missingness)
## Multiple R-squared:  0.01234,	Adjusted R-squared:  0.01223 
## F-statistic: 115.7 on 3 and 27776 DF,  p-value: < 2.2e-16
```






