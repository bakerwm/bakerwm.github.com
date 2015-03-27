---
layout: post
title: "ggplot2 and parallel"
categories:
- R
tags:
- ggplot2
- parallel

comments: true
---
> ## Store the ggplot2 figs to a `list` object  
> **Q:** How to create lists of ggplot?    
> **A:** Find solutions in [stackOverflow](http://stackoverflow.com/questions/22817386/how-to-create-lists-of-ggplot)  

## 1. Condition-1  

I'm trying to create functions that return a list of `ggplot`.

the default class of `ggplot` are: `gg` and `ggplot`


```r
library(ggplot2)
p1 <- c(qplot(1:10, rnorm(10)))
p2 <- qplot(1:10, rnorm(10))

attributes(p1)
```

```
## $names
## [1] "data"        "layers"      "scales"      "mapping"     "theme"      
## [6] "coordinates" "facet"       "plot_env"    "labels"
```

```r
attributes(p2)
```

```
## $names
## [1] "data"        "layers"      "scales"      "mapping"     "theme"      
## [6] "coordinates" "facet"       "plot_env"    "labels"     
## 
## $class
## [1] "gg"     "ggplot"
```

`p[[1]]` (same as `p[['data']])`) is supposed to be a `dataframe`. It usually holds the data for the plot.

`p1` is a `list` because you used the `c` function.

`p2` is a `ggplot` because that's what `qplot` returns.

**So, we can store many `ggplot` objects using `list`.**


```r
FinalPlots <- list(p1, p2)
```

Or we can save all the `ggplot` objects in the global env using the following function:


```r
AllPlots <- Filter(function(x) is(x, "ggplot"), mget(ls()))
```

## 2. Create >1000 figures using ggplot2 faster.
I will store the `ggplot` in a `list`, and using `parallel` perform parallel computing. At last, write all the `ggplot` from the `list` to a PDF file.



```r
library(foreach)
library(doParallel)
```

```
## Loading required package: iterators
## Loading required package: parallel
```

```r
library(ggplot2)

cl <- makeCluster(2)
registerDoParallel(cl, cores = 2)
getDoParName()
```

```
## [1] "doParallelSNOW"
```

```r
AllFig <- list()

system.time(
  #for(i in 1:100) {
  AllFig <- foreach(i = 1:100, .packages = c("ggplot2")) %dopar% {
    fig <- ggplot(mtcars, aes(x = mpg, y = disp)) + geom_point()
    fig
  }
)
```

```
##    user  system elapsed 
##    3.36    0.15    6.51
```

```r
stopCluster(cl)

pdf("test.pdf", width = 6, height = 4)
for(i in 1:length(AllFig)) {
  print(AllFig[[i]])  
}
dev.off()
```

```
## png 
##   2
```

## Parallel computing in R (foreach + doParallel)

URL: [https://nycdatascience.com/slides/parallel_R/examples_general.html](https://nycdatascience.com/slides/parallel_R/examples_general.html)

We need packages: `foreach`, `doParallel`, or `doSNOW`

1. Parallel 1: Two cores, two chunks  

```r
library(doSNOW)
```

```
## Loading required package: snow
## 
## Attaching package: 'snow'
## 
## The following objects are masked from 'package:parallel':
## 
##     clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
##     clusterExport, clusterMap, clusterSplit, makeCluster,
##     parApply, parCapply, parLapply, parRapply, parSapply,
##     splitIndices, stopCluster
```

```r
library(foreach)
library(randomForest)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
cores <- 2
cl <- makeCluster(cores, type = "SOCK")
registerDoSNOW(cl)

## main code, portable
total.tree <- 2000  # B
num.chunk <- 2
avg.tree <- ceiling(total.tree/num.chunk)

## foreach statement ntree: iterator .combine: speicify the way to collect
## results .packages: load the packages for workers

system.time({
  rf_fit <- foreach(ntree = rep(avg.tree, num.chunk), .combine = combine, 
                    .packages = c("randomForest")) %dopar% {
                      randomForest(x = iris[, -5], y = iris[, 5], ntree = ntree)
                    }
})
```

```
##    user  system elapsed 
##    0.44    0.01    1.56
```

2. Parallel 2: Two cores, four chunk


```r
cores <- 2
cl <- makeCluster(cores, type = "SOCK")
registerDoSNOW(cl)


total.tree <- 2000
num.chunk <- 4
avg.tree <- ceiling(total.tree/num.chunk)

system.time({
  rf_fit <- foreach(ntree = rep(avg.tree, num.chunk), .combine = combine, 
                    .packages = c("randomForest")) %dopar% {
                      randomForest(x = iris[, -5], y = iris[, 5], ntree = ntree)
                    }
})
```

```
## Warning: 关闭不再使用的链结6(<-activate.adobe.com:11647)
```

```
## Warning: 关闭不再使用的链结5(<-activate.adobe.com:11647)
```

```
##    user  system elapsed 
##    0.81    0.03    1.91
```



```r
stopCluster(cl)
```


**Usefull resources:**    
1. vignettes of foreach. [Site](http://cran.r-project.org/web/packages/foreach/vignettes/foreach.pdf)  
2. vignettes of Parallel. [Site](http://cran.r-project.org/web/packages/doParallel/vignettes/gettingstartedParallel.pdf)  
3. Simple example of doSNOW and parallel: [site](http://www.r-bloggers.com/simple-examplehow-to-use-foreach-and-dosnow-packages-for-parallel-computation/)  
4. Example of Parallel computing using R. [Site](https://nycdatascience.com/slides/parallel_R/examples_general.html)  
5. Example of ggplot2 graph. [Site](http://www.r-bloggers.com/my-commonly-done-ggplot2-graphs/)  








