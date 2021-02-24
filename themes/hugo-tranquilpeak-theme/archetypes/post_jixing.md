---
title: "{{ replace .TranslationBaseName "-" " " | title }}"
date: {{ .Date }}
categories:
- category
- subcategory
tags:
- tag1
- tag2
keywords:
- tech
thumbnailImagePosition: left
thumbnailImage: https://i.loli.net/2018/08/13/5b70bd4dc9763.jpg
metaAlignment: center
coverMeta: out
---


```{r setup, echo=FALSE, cache=FALSE, include=FALSE}
library(knitr)
library(tidyverse)
library(reticulate)
# reticulate::py_config()

# Global options
options(max.print="75")
opts_chunk$set(echo=TRUE,
	             cache=TRUE,
               prompt=FALSE,
               tidy=TRUE,
               comment=NA,
               message=FALSE,
               warning=FALSE)
opts_knit$set(width=75)
```

<!--more-->
