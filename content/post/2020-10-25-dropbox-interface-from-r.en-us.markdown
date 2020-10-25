---
title: Dropbox 与 R 的交互
author: ZERO
date: '2020-10-25'
slug: dropbox-interface-from-r.en-us
categories:
  - R
tags:
  - "\U0001F4E6"
keywords:
  - tech
thumbnailImage: https://tva1.sinaimg.cn/large/0081Kckwgy1gk19sogooaj307705e0sm.jpg
thumbnailImagePosition: left
# metaAlignment: center
output:
  blogdown::html_page:
    toc: true
    toc_depth: 1
    number_sections: true
    fig_width: 8
    # css: "/css/my-style.css"
#bibliography: ./../../references.bib
---
<!--more-->







`rdrop2`个包提供了从 r 到 Dropbox的应用接口。

功能包括任何文件处理的命令(就像在使用自己的本地文件夹一样)以及 Dropbox 帐户上传和下载文件的能力。


## 安装

```r
# Current CRAN version (0.8.1)
install.packages('rdrop2')

# or the development version (0.8.1.9999)
devtools::install_github("karthik/rdrop2")
```

## 认证

+ `drop_auth()`名利将启动您的浏览器并请求访问您的Dropbox帐户。如果您尚未登录，将提示您登录。
+ 完成后，关闭浏览器窗口并返回R以完成身份验证。
+ 凭据将自动缓存（可以防止这种情况），以备将来使用。


```r
library(tidyverse)
library(rdrop2)
drop_auth()

#如果你希望保存凭据作为本地使用, 可以执行以下命令
token <- drop_auth()
saveRDS(token, file = "~/Desktop/token.rds")
# Then in any drop_* function, pass `dtoken = token
# Tokens are valid until revoked.
drop_acc(dtoken = token)
```
好了设置完成, 现在可以开始使用了

## 开始使用

### 检索 Dropbox 帐户信息

```r
library(dplyr)
drop_acc() %>% data.frame()
```
### 目录列表


```r
drop_dir()
```

```
## # A tibble: 5 x 5
##   .tag   name            path_lower      path_display    id                     
##   <chr>  <chr>           <chr>           <chr>           <chr>                  
## 1 folder photo           /photo          /photo          id:ltB8YJnPuIAAAAAAAAB…
## 2 folder IFTTT           /ifttt          /IFTTT          id:ltB8YJnPuIAAAAAAAAB…
## 3 folder _2_book         /_2_book        /_2_book        id:ltB8YJnPuIAAAAAAAAB…
## 4 folder Library.papers3 /library.paper… /Library.paper… id:ltB8YJnPuIAAAAAAAAB…
## 5 folder 应用            /应用           /应用           id:ltB8YJnPuIAAAAAAAAB…
```

```r
# If your account is not empty, it returns the following fields
#  [1] ".tag"            "name"            "path_lower"     
#  [4] "path_display"    "id"              "client_modified"
#  [7] "server_modified" "rev"             "size"           
# [10] "content_hash"   

# or specify a path
drop_dir('/_2_book')
```

```
## # A tibble: 90 x 11
##    .tag  name  path_lower path_display id    client_modified server_modified
##    <chr> <chr> <chr>      <chr>        <chr> <chr>           <chr>          
##  1 fold… 2017… /_2_book/… /_2_book/20… id:l… <NA>            <NA>           
##  2 fold… _rea… /_2_book/… /_2_book/_r… id:l… <NA>            <NA>           
##  3 fold… _圣经 /_2_book/… /_2_book/_圣… id:l… <NA>            <NA>           
##  4 fold… Hand… /_2_book/… /_2_book/Ha… id:l… <NA>            <NA>           
##  5 fold… pyth… /_2_book/… /_2_book/py… id:l… <NA>            <NA>           
##  6 fold… shiny /_2_book/… /_2_book/sh… id:l… <NA>            <NA>           
##  7 file  一些关于… /_2_book/… /_2_book/一些… id:l… 2017-02-07T07:… 2018-06-14T05:…
##  8 file  赤裸裸的… /_2_book/… /_2_book/赤裸… id:l… 2016-09-30T15:… 2018-08-14T16:…
##  9 file  a-li… /_2_book/… /_2_book/a-… id:l… 2017-10-10T03:… 2018-08-20T03:…
## 10 file  Appl… /_2_book/… /_2_book/Ap… id:l… 2018-02-11T14:… 2018-08-20T03:…
## # … with 80 more rows, and 4 more variables: rev <chr>, size <int>,
## #   is_downloadable <lgl>, content_hash <chr>
```
### 按照类型筛选

```r
drop_dir('/_2_book') %>%
  filter(.tag == "folder")
```

```
## # A tibble: 6 x 11
##   .tag  name  path_lower path_display id    client_modified server_modified
##   <chr> <chr> <chr>      <chr>        <chr> <chr>           <chr>          
## 1 fold… 2017… /_2_book/… /_2_book/20… id:l… <NA>            <NA>           
## 2 fold… _rea… /_2_book/… /_2_book/_r… id:l… <NA>            <NA>           
## 3 fold… _圣经 /_2_book/… /_2_book/_圣… id:l… <NA>            <NA>           
## 4 fold… Hand… /_2_book/… /_2_book/Ha… id:l… <NA>            <NA>           
## 5 fold… pyth… /_2_book/… /_2_book/py… id:l… <NA>            <NA>           
## 6 fold… shiny /_2_book/… /_2_book/sh… id:l… <NA>            <NA>           
## # … with 4 more variables: rev <chr>, size <int>, is_downloadable <lgl>,
## #   content_hash <chr>
```
### 创建文件夹

```r
drop_create('test/rdrop2_test')
```

```
## Folder /test/rdrop2_test created successfully
```

### 上传文件

```r
write.csv(mtcars, '~/Desktop/mtcars.csv')
# or upload to a specific folder
drop_upload(file = '~/Desktop/mtcars.csv', 
            path = "test/rdrop2_test")
```

```
## File ~/Desktop/mtcars.csv uploaded as /test/rdrop2_test/mtcars.csv successfully at 2020-10-25T00:52:58Z
```

### 下载文件

```r
drop_download(path = "test/rdrop2_test/mtcars.csv", 
              local_path = "~/Desktop",
              overwrite = TRUE)
```

```
## [1] TRUE
```
### 删除文件

```r
drop_create("test/new_folder")
```

```
## Folder /test/new_folder created successfully
```

```r
drop_delete(path = "test/new_folder")
```
### 移动文件

```r
drop_create("test/new_folder")
```

```
## Folder /test/new_folder created successfully
```

```r
drop_move(from_path = "test/rdrop2_test/mtcars.csv", 
          to_path = "test/new_folder/mtcars.csv")
```

```
## /test/rdrop2_test/mtcars.csv moved to /test/new_folder/mtcars.csv
```
### 复制文件


```r
drop_create("test/new_folder2")
```

```
## Folder /test/new_folder2 created successfully
```

```r
drop_copy(from_path = "test/new_folder/mtcars.csv", 
          to_path = "test/new_folder2/mtcars.csv")
```

```
## /test/new_folder/mtcars.csv copied to /test/new_folder2/mtcars.csv
```

### 搜索和下载文件

这个对于一些常用文件的调用非常方便

例如, 调取`matrix.gif`文件

```r
drop_upload("~/Desktop/matrix.gif", "test")
```

```
## File ~/Desktop/matrix.gif uploaded as /test/matrix.gif successfully at 2020-10-25T00:53:23Z
```

```r
x <- drop_search("matrix")
x$matches[[1]]$metadata$path_lower
```

```
## [1] "/test/matrix.gif"
```

```r
drop_download(x$matches[[1]]$metadata$path_lower, 
              local_path = '~/Desktop/matrix_from_dropbox.gif',
              overwrite = TRUE)
```

```
## [1] TRUE
```

### 直接从 Dropbox 读取 csv 文件

如果希望通过 `Shiny` 或远程集群、 EC2、 Digital Ocean 等访问 Dropbox 账户，可以将缓存的 `oauth` 文件保留在同一个目录中，或者显式地传递令牌以删除 auth。您还可以将 `drop_auth` 的输出保存到`r` 对象中，将其接收到磁盘，并将其作为标记传递。

如果在 `Travis` 或类似的网站上使用，你应该考虑加密 `oauth` 缓存文件，以防止未经授权访问你的 `Dropbox` 账户。

如果您有多个令牌和帐户，也可以覆盖环境令牌，并为每个 `drop_fun`
显式传递一个特定令牌。


```r
# Note that there is a quiet download happening to your temp dir
new_mtcars <- drop_read_csv("test/new_folder/mtcars.csv")
```
### 在 Shiny 和远程服务器上访问 Dropbox


```r
token <- drop_auth()
saveRDS(token, "~/Desktop/droptoken.rds")
# Upload droptoken to your server
# ******** WARNING ********
# Losing this file will give anyone 
# complete control of your Dropbox account
# You can then revoke the rdrop2 app from your
# dropbox account and start over.
# ******** WARNING ********
# read it back with readRDS
token <- readRDS("~/Desktop/droptoken.rds")
# Then pass the token to each drop_ function
drop_acc(dtoken = token)
```



## links

[karthik/rdrop2: Dropbox Interface from R](https://github.com/karthik/rdrop2)
