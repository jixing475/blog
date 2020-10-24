---
title: 实时分享你的代码
author: ZERO
date: '2020-10-24'
slug: how-to-share-your-code.en-us
categories:
  - tips
tags:
  - rstudio
keywords:
  - tech
thumbnailImage: https://tva1.sinaimg.cn/large/0081Kckwgy1gk09sjxya9j30u00u0te1.jpg
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

## 工具准备

  * [RStudio](https://rstudio.com/)
  * `livecode`: [https://github.com/rundel/livecode](https://github.com/rundel/livecode)
  * [ngrok账户](https://ngrok.com/) 和 `ngrok`客户端



### `livecode`


```r
remotes::install_github("rundel/livecode")
```

### `ngrok`

1. 首先我们登陆[ngrok.com](https://ngok.com/)，点击“注册”获得账号。可以使用 Google 或 github 或者邮箱进行注册.
![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk0a0z9akgj30ks0bg414.jpg)
2. 下载客户端

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk0a6f3f9nj30h50bg755.jpg)

3. 授权

![](https://tva1.sinaimg.cn/large/0081Kckwly1gk0aal7yxnj30d304j0sx.jpg)
![](https://tva1.sinaimg.cn/large/0081Kckwly1gk0abjwtotj319q02odhw.jpg)
## 开始使用 livecode

在 Rstudio新建文件并保存

```r
s <- livecode::serve_file()
```
![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk0affq9y3j30pc082myb.jpg)

但是这只是在本地局域网可以访问, 那么如何上传到因特网?

### 使用 ngrok 在线分享我们的代码

```bash
ngrok http http://192.168.31.140:15149/
```


![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk0an6w75lj31d40u045l.jpg)

好了, 现在可以开始访问我的本地代码, 当然这个办法可以分享任何本地网址.

## 参考

[Code live from RStudio, and share it with the World in real time - Bits & Bricks](https://bitsandbricks.github.io/post/code-live-from-rstudio-and-share-it-with-the-world-in-real-time/)
