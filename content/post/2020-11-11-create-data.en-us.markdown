---
title: 根据平均值和标准差生成正态分布数据
author: ZERO
date: '2020-11-11'
slug: create-data.en-us
categories:
  - R
tags:
  - academic
keywords:
  - tech
thumbnailImagePosition: left
thumbnailImage: https://i.loli.net/2018/08/13/5b70bd4dc9763.jpg
metaAlignment: center
coverMeta: out
---



<!--more-->

## 定义函数

```r
my_tableone <- function(data_tidy, group, ...) {
    formula <- stringr::str_glue("{group} ~ . ") %>% as.formula()
    res_tab <-
        data_tidy %>%
        compareGroups::compareGroups(formula,
                                     data = .,
                                     method = 1) %>%
        #1 - forces analysis as "normal-distributed";
        #2 - forces analysis as "continuous non-normal";
        #3 - forces analysis as "categorical";
        #4 - NA, which performs a Shapiro-Wilks test to decide between normal or non-normal
        compareGroups::createTable(
            .,
            #digits = c(p14 = 0, hormo = 1),
            # show.p.mul = TRUE, show.p.overall = FALSE, # 显示过个 P value, 两两之间的比较
            #extra.labels = c("Mean (SD)","Median (IQR)","",""), # 给变量添上额外标签
            digits.p = 3, # 小数点位数
            sd.type = 2, # 是1 () 还是 2 ±
            type = 2,
            show.all = TRUE,
            hide.no = "no",
            show.n = FALSE
        )
    return(res_tab)
}
```


## 需求


根据以下结果表生成数据:

+ A组样本 30 个, B组样本 31 个
+ 且为离散型正态分布


```r
meta <- tibble::tribble(
  ~var, ~mean_A,  ~sd_A, ~mean_B,  ~sd_B,    ~p,
  "D1",   52.86,   "45",  128.33,   "40", "sig",
  "D2",   36.34,   "20",   87.33,   "10", "sig",
  "D3",   89.29,   "80",  215.67,   "50", "sig",
  "D4",     2.2,  "1.1",     2.7,  "1.3", "sig",
  "D5",     0.8,    "1",     1.1,    "1",    NA,
  "D6",   78.93,   "25",   80.67,   "30",    NA,
  "D7",  113.44,   "10",  108.53,   "15",    NA,
  "D8",     1.6,   "<3",     1.8,   "<3",    NA,
  "D9",     3.8, "2--5",     3.5, "2--5",    NA
  )
```


## 数据表


```r
set.seed(123)
data <- data.frame(group = rep(c("A", "B"), each = 30) %>% c(., "B"),
           D1 = c(rnorm_perfect(30, 52.86, 20), rnorm_perfect(31, 128.33, 40)),
           D2 = c(rnorm_perfect(30, 36.34, 19), rnorm_perfect(31, 87.33, 10)),
           D3 = c(rnorm_perfect(30, 89.29, 18), rnorm_perfect(31, 215.67, 50)),
           #
           D4 = c(rnorm_perfect(30, 2.2, 0.7), rnorm_perfect(31, 2.7, 0.3)),
           D5 = c(rnorm_perfect(30, 1.2, 0.613), rnorm_perfect(31, 1.4, 0.964)),
           D6 = c(rnorm_perfect(30, 78.93, 25), rnorm_perfect(31, 80.67, 30)),
           #
           D7 = c(rnorm_perfect(30, 113.44, 10), rnorm_perfect(31, 108.53, 15)),
           D8 = c(rnorm_perfect(30, 1.6, 0.7), rnorm_perfect(31, 1.8, 0.8)),
           D9 = c(rnorm_perfect(30, 3.8, 1.5), rnorm_perfect(31, 3.5, 1.4))) 
```


```r
colnames(data)
```

```
##  [1] "group" "D1"    "D2"    "D3"    "D4"    "D5"    "D6"    "D7"    "D8"   
## [10] "D9"
```

```r
df <- 
data %>% 
  mutate_at(c("D4", "D5", "D8", "D9"), round, digits = 0) %>% 
  mutate_at(c("D1", "D2","D3", "D6", "D7"), ~ `%/%`(.x, 5) * 5) %>% 
  mutate_at(c("D1", "D2","D3", "D6", "D7"), ~ if_else(.x==0, 5, .x)) %>% 
  mutate_at(c("D4"), ~ if_else(.x==0, 1, .x)) %>% 
  mutate(D3 = D1 + D2) %>% 
  relocate(D3, .before = "D4")
```





## 统计


```r
df %>% 
  my_tableone(., "group") 
```

```
## 
## --------Summary descriptives table by 'group'---------
## 
## __________________________________________ 
##      [ALL]       A         B     p.overall 
##      N=61      N=30      N=31              
## ¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯ 
## D1 88.9±47.5 50.7±18.1 126±36.4   <0.001   
## D2 60.0±29.1 34.3±17.7 84.8±9.62  <0.001   
## D3 149±75.4  85.0±35.6 211±46.0   <0.001   
## D4 2.48±0.62 2.20±0.66 2.74±0.44  <0.001   
## D5 1.31±0.79 1.20±0.61 1.42±0.92   0.277   
## D6 77.8±25.2 76.7±22.8 78.9±27.8   0.735   
## D7 108±11.9  111±9.01  106±13.8    0.120   
## D8 1.72±0.76 1.63±0.72 1.81±0.79   0.375   
## D9 3.66±1.34 3.80±1.37 3.52±1.31   0.413   
## ¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯
```




The quick-and-dirty approach is to use the 68-95-99.7 rule.

In a normal distribution, 99.7% of values fall within 3 standard deviations of the mean. So, if you set your mean to the middle of your desired minimum value and maximum value, and set your standard deviation to 1/3 of your mean, you get (mostly) values that fall within the desired interval. Then you can just clean up the rest.


```r
minVal <- 0
maxVal <- 100
mn <- (maxVal - minVal)/2
# Generate numbers (mostly) from min to max
x <- rnorm(count, mean = mn, sd = mn/3)
# Do something about the out-of-bounds generated values
x <- pmax(minVal, x)
x <- pmin(maxVal, x)
```


[r - Generate random numbers following a distribution within an interval - Cross Validated](https://stats.stackexchange.com/questions/113230/generate-random-numbers-following-a-distribution-within-an-interval)




