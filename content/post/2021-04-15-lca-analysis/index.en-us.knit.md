---
title: LCA analysis
author: ZERO
date: '2021-04-15'
slug: index.en-us
categories:
  - data science
tags:
  - Clinical
thumbnailImagePosition: left
thumbnailImage: https://i.loli.net/2018/08/13/5b70bd4dc9763.jpg
metaAlignment: center
coverMeta: out
---

# depMixS4

Some general resources:    
[http://www.ats.ucla.edu/stat/mplus/seminars/lca](http://www.ats.ucla.edu/stat/mplus/seminars/lca)     
[http://www.john-uebersax.com/stat/faq.htm](http://www.john-uebersax.com/stat/faq.htm)
An excellent chapter explaining LCA (categorical indicators) and LPA (continuous indicators): [http://daob.nl/wp-content/uploads/2015/06/oberski-LCA.pdf](http://daob.nl/wp-content/uploads/2015/06/oberski-LCA.pdf)          
[http://stats.stackexchange.com/questions/122213/latent-class-analysis-vs-cluster-analysis-differences-in-inferences](http://stats.stackexchange.com/questions/122213/latent-class-analysis-vs-cluster-analysis-differences-in-inferences)     


Includes an example (in mplus) of a model with both continuous and categorical indicators:
[http://www.ats.ucla.edu/stat/mplus/seminars/introMplus_part2/lca.htm](http://www.ats.ucla.edu/stat/mplus/seminars/introMplus_part2/lca.htm)     
...translating it to r:
[http://stats.stackexchange.com/questions/47426/latent-class-model-with-both-continuous-and-categorical-indicators-in-r](http://stats.stackexchange.com/questions/47426/latent-class-model-with-both-continuous-and-categorical-indicators-in-r)     
The r package being used:
[https://cran.r-project.org/web/packages/depmixS4/vignettes/depmixS4.pdf](https://cran.r-project.org/web/packages/depmixS4/vignettes/depmixS4.pdf)     

Some packages I'm not using:


```r
library(mclust)
library(poLCA) # only categorical indicators
library(nlsem)
```


From [IDRE (example 2 is most relevant)](http://www.ats.ucla.edu/stat/mplus/seminars/introMplus_part2/lca.htm):     
The examples on this page use a dataset with information on high school students' academic histories.  In the first example below, a 2 class model is estimated using four dichotomous variables as indicators (category 1 = no, category 2 = yes). The variables are whether the student had taken honors math (hm), honors English (he), or vocational classes (voc); and whether the student reported they were unlikely to go to college (nocol). The expected classes are academically oriented students (i.e. students who took honors classes, did not take vocational classes and reported they were likely to go to college), and students who are less academically oriented. The dataset for this example is [lca.dat](http://www.ats.ucla.edu/stat/mplus/seminars/introMplus_part2/lca.dat).   
...    
Above we estimated a specific case of a mixture model, a latent class analysis, in which all of the indicators are categorical, in this example the model contains both categorical and continuous indicators. In addition to the four categorical variables used in the example above, this model includes four continuous variables, the students score on a measure of academic achievement for each of the four years of high school (ach09-ach12). The achievement variables have been centered so that each has a mean of zero.


```r
library(depmixS4)
```

```
## Warning: package 'depmixS4' was built under R version 4.0.2
```

```
## Loading required package: nnet
```

```
## Loading required package: MASS
```

```
## Loading required package: Rsolnp
```

```
## Warning: package 'Rsolnp' was built under R version 4.0.2
```

```
## Loading required package: nlme
```



```r
data(sp500)

# fit some models
msp <- depmix(logret~1,nstates=2,data=sp500)
set.seed(1)
fmsp <- fit(msp)	
```

```
## converged at iteration 85 with logLik: 1334.635
```

```r
# plot posterior state sequence for the 2-state model
plot(ts(posterior(fmsp)[,2], start=c(1950,2),deltat=1/12),ylab="probability",
main="Posterior probability of state 1 (volatile, negative markets).",
frame=FALSE)
```

<img src="index.en-us_files/figure-html/unnamed-chunk-2-1.png" width="672" />

```r
## Not run: 

# this creates data with a single change point with Poisson data
set.seed(3)
y1 <- rpois(50,1)
y2 <- rpois(50,2)
ydf <- data.frame(y=c(y1,y2))

# fit models with 1 to 3 states
m1 <- depmix(y~1,ns=1,family=poisson(),data=ydf)
set.seed(1)
fm1 <- fit(m1)
```

```
## converged at iteration 1 with logLik: -159.3054
```

```r
m2 <- depmix(y~1,ns=2,family=poisson(),data=ydf)
set.seed(1)
fm2 <- fit(m2)
```

```
## converged at iteration 27 with logLik: -145.528
```

```r
m3 <- depmix(y~1,ns=3,family=poisson(),data=ydf)
set.seed(1)
fm3 <- fit(m3,em=em.control(maxit=500))

# plot the BICs to select the proper model
plot(1:3,c(BIC(fm1),BIC(fm2),BIC(fm3)),ty="b")
```

<img src="index.en-us_files/figure-html/unnamed-chunk-2-2.png" width="672" />

```r
## End(Not run)

## Not run: 
# similar to the binomial model, data may also be entered in 
# multi-column format where the n for each row can be different
dt <- data.frame(y1=c(0,1,1,2,4,5),y2=c(1,0,1,0,1,0),y3=c(4,4,3,2,1,1))
# specify a mixture model ...
m2 <- mix(cbind(y1,y2,y3)~1,data=dt,ns=2,family=multinomial("identity"))
set.seed(1)
fm2 <- fit(m2)
```

```
## converged at iteration 14 with logLik: -15.52289
```

```r
# ... or dependent mixture model
dm2 <- depmix(cbind(y1,y2,y3)~1,data=dt,ns=2,family=multinomial("identity"))
set.seed(1)
fdm2 <- fit(dm2)
```

```
## converged at iteration 8 with logLik: -13.54739
```

```r
## End(Not run)
```



























