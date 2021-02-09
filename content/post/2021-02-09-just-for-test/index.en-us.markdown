---
title: Use R to draw data frame missing values
author: ZERO
date: '2021-02-09'
slug: index.en-us
categories:
  - python
tags:
  - misc
keywords:
  - tech
thumbnailImagePosition: left
thumbnailImage: https://i.loli.net/2018/08/13/5b70bd4dc9763.jpg
metaAlignment: center
coverMeta: out
editor_options: 
  markdown: 
    wrap: 72
---



## load data


```r
library(visdat)
library(naniar)
library(simputation)
library(tidyverse)
air_quality_tbl <- airquality %>% as_tibble()

air_quality_tbl
```

```
# A tibble: 153 x 6
   Ozone Solar.R  Wind  Temp Month   Day
   <int>   <int> <dbl> <int> <int> <int>
 1    41     190   7.4    67     5     1
 2    36     118   8      72     5     2
 3    12     149  12.6    74     5     3
 4    18     313  11.5    62     5     4
 5    NA      NA  14.3    56     5     5
 6    28      NA  14.9    66     5     6
 7    23     299   8.6    65     5     7
 8    19      99  13.8    59     5     8
 9     8      19  20.1    61     5     9
10    NA     194   8.6    69     5    10
# … with 143 more rows
```

## vis missing data in R


```r
# 1.0 MISSING DATA VISUALIZATIONS ---- - Visualization, Covered in DS4B 101-R,
# Week 4

# * vis_dat() -----

air_quality_tbl %>% vis_dat()
```

<img src="/post/2021-02-09-just-for-test/index.en-us_files/figure-html/unnamed-chunk-2-1.png" width="672" />

```r
# * vis_miss() ----

air_quality_tbl %>% vis_miss()
```

<img src="/post/2021-02-09-just-for-test/index.en-us_files/figure-html/unnamed-chunk-2-2.png" width="672" />

```r
# * gg_miss_upset() ----

air_quality_tbl %>% gg_miss_upset()
```

<img src="/post/2021-02-09-just-for-test/index.en-us_files/figure-html/unnamed-chunk-2-3.png" width="672" />

```r
# * geom_miss_point() ----

air_quality_tbl %>% ggplot(aes(x = Solar.R, y = Ozone)) + geom_miss_point()
```

<img src="/post/2021-02-09-just-for-test/index.en-us_files/figure-html/unnamed-chunk-2-4.png" width="672" />

```r
# 2.0 IMPUTATION ----

# * Linear Imputation - impute_lm() ---- - Data Wrangling - Covered in DS4B
# 101-R, Week 2&3

air_quality_tbl %>% 
# Label if Ozone is missing
add_label_missings(Ozone) %>% 
# Imputation - Linear Regression
mutate(Ozone = as.double(Ozone)) %>% impute_lm(Ozone ~ Temp + Wind) %>% 
# Visualize
ggplot(aes(Solar.R, Ozone, color = any_missing)) + geom_point()
```

<img src="/post/2021-02-09-just-for-test/index.en-us_files/figure-html/unnamed-chunk-2-5.png" width="672" />

```r
# * Random Forest - impute_rf() ----

air_quality_tbl %>% 
# Label if Ozone is missing
add_label_missings(Ozone) %>% 
# Imputation - Ozone
mutate(Ozone = as.double(Ozone)) %>% impute_rf(Ozone ~ Temp + Wind) %>% 
# Imputation - Solar.R
mutate(Solar.R = as.double(Solar.R)) %>% impute_rf(Solar.R ~ Temp + Wind) %>% 
# Visualize
ggplot(aes(Solar.R, Ozone, color = any_missing)) + geom_point()
```

<img src="/post/2021-02-09-just-for-test/index.en-us_files/figure-html/unnamed-chunk-2-6.png" width="672" />


```r
df <- air_quality_tbl %>% select(c("Ozone", "Solar.R"))
```

![](images/paste-5B08AE54.png)
