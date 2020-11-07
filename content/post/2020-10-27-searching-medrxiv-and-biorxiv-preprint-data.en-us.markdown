---
title: 使用 R 搜索medRxiv和bioRxiv
author: ZERO
date: '2020-10-27'
slug: searching-medrxiv-and-biorxiv-preprint-data.en-us
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

medRxiv，作为医学，临床和相关健康科学领域论文的预印库，成为与COVID-19大流行相关的新研究的主要来源。

但是, medRxiv网站上提供的本机搜索功能不适合用于系统评价. 

下面介绍R 软件包在这方面的补充[ropensci/medrxivr: Access and search medRxiv and bioRxiv preprint data](https://github.com/ropensci/medrxivr).

## medRxiv网站搜索功能的局限性

### 令人困惑的布尔运算

“(dementia OR alzheimer) AND (lipid)” 和  “(alzheimer OR dementia) AND lipids” 两个关键词的搜索结果竟然不一样


### 缺乏再现性

“(dementia OR alzheimer) AND lipids” 随着时间的推移, 结果会不一样

### 不能批量导出

不能批量导出你检索到的结果


```r
# Load the packages
library(medrxivr)
library(dplyr)
library(ggplot2)
```

## 例1


```r
# Import the data from medRxiv API endpoint
mx_data <- mx_api_content(from_date = "2020-01-01", 
                          to_date = "2020-01-07")

# Create the graph 
mx_data %>%
  group_by(category) %>%
  summarise(N = n(), .groups = "keep") %>%
  arrange(desc(N)) %>%
  ggplot(aes(y = reorder(category,N), x = N)) +
  geom_col(colour = "black") + 
  ylab("Category") +
  xlab("Number of preprints, 1st week of January 2020") +
  scale_x_continuous(expand = c(0, 0), limits = c(0,10)) +
  theme_classic() 
```

<img src="/post/2020-10-27-searching-medrxiv-and-biorxiv-preprint-data.en-us_files/figure-html/unnamed-chunk-2-1.png" width="672" />


## 例2


```r
# Use the maintained snapshot to quickly load today's copy of the medRxiv database
# Note - this `preprint_data` object could be saved as a CSV in your project 
#        repository to aid reproducibility
preprint_data <- mx_snapshot()
```


```r
# Build your search
topic1  <- c("dementia","vascular","alzheimer's")  # Combined with Boolean OR
topic2  <- c("lipids","statins","cholesterol")     # Combined with Boolean OR
myquery <- list(topic1, topic2)                    # Combined with Boolean AND

# Run your search
results <- mx_search(data = preprint_data,
                     query = myquery)
```


```r
# Let's have a look at the returned records
results
```

```
## # A tibble: 52 x 14
##       ID title abstract authors date       category doi   version
##    <dbl> <chr> <chr>    <chr>   <date>     <chr>    <chr>   <dbl>
##  1   184 Exce… "Object… De Lim… 2019-07-25 Cardiov… 10.1…       1
##  2   281 Peri… "Introd… Newton… 2019-08-14 Addicti… 10.1…       1
##  3   284 A co… "Object… Moriar… 2019-08-15 Cardiov… 10.1…       1
##  4   341 Apol… "Backgr… Richar… 2019-08-29 Cardiov… 10.1…       1
##  5   307 Comp… "Owing … Batty,… 2019-08-17 Epidemi… 10.1…       1
##  6   473 Self… "Backgr… Grover… 2019-09-23 Cardiov… 10.1…       1
##  7   644 Tren… "Object… Curtis… 2019-10-18 Cardiov… 10.1…       1
##  8   979 Does… "OBJECT… Harber… 2019-11-29 Endocri… 10.1…       1
##  9  1086 Asse… "Backgr… Finner… 2019-12-14 Cardiov… 10.1…       1
## 10  1334 Idea… "BACKGR… Mckenz… 2020-01-16 Epidemi… 10.1…       1
## # … with 42 more rows, and 6 more variables: author_corresponding <chr>,
## #   author_corresponding_institution <chr>, link_page <chr>, link_pdf <chr>,
## #   license <chr>, published <chr>
```





## links

[rOpenSci | Searching medRxiv and bioRxiv Preprint Data](https://ropensci.org/blog/2020/10/20/searching-medrxivr-and-biorxiv-preprint-data/)
