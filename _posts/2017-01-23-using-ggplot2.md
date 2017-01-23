---
layout: post
title: "Using ggplot2"
description: "Creating scatter plots, line graphs, bar graphs, histograms, and density plots using ggplot2"
category: R
tags: [ggplot2]
---
{% include JB/setup %}

Load library:

``` r
library(ggplot2)
```

Scatter plot:

``` r
data(iris)
ggplot(iris, aes(x=Sepal.Length, y=Sepal.Width)) +
  geom_point(aes(colour=Species))
```

<img src="unnamed-chunk-2-1.png" width="600" class="center-image">

Line graph:

``` r
data(EuStockMarkets)
df <- as.data.frame(EuStockMarkets)
df$x <- 1:nrow(df)
library(reshape2)
df_melt <- melt(df, id.vars = 'x')
ggplot(df_melt, aes(x=x, y=value)) +
  geom_line(aes(colour=variable)) +
  xlab('1991 - 1998')
```

<img src="unnamed-chunk-3-1.png" width="600" class="center-image">

Bar graph:

``` r
data(Orange)
library(dplyr)
```

    ##
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ##
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ##
    ##     intersect, setdiff, setequal, union

``` r
ggplot(group_by(Orange, Tree) %>% summarise(max = max(circumference)), aes(x=Tree, y=max)) + geom_bar(stat="identity")
```

<img src="unnamed-chunk-4-1.png" width="600" class="center-image">

Histogram:

``` r
data(randu)
ggplot(randu, aes(x=x)) + geom_histogram(bins = 50)
```

<img src="unnamed-chunk-5-1.png" width="600" class="center-image">

Density plot:

``` r
data(randu)
ggplot(randu, aes(x=x)) + geom_density(adjust=0.1)
```

<img src="unnamed-chunk-6-1.png" width="600" class="center-image">
