---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Chapter 3
menu:
  example:
    parent: Overview
    weight: 1
title: Chapter 3
toc: true
type: docs
weight: 1
editable: false
---

## Sampling from the imaginary

Now let's sample from the posterior


```r
p_grid <- seq( from=0, to=1, length.out = 1000)

prob_p <- rep(1, 1000)

prob_data <- dbinom(6, size=9, prob= p_grid)

posterior <- prob_data * prob_p


posterior <- posterior / sum(posterior)
```




```r
samples <- sample(p_grid, prob = posterior, size=1e4, replace = TRUE)
```




```r
plot(samples)
```

<img src="/courses/stat_rethink/chapter3_files/figure-html/unnamed-chunk-3-1.png" width="672" />




```r
library(rethinking)

dens(samples)
```

<img src="/courses/stat_rethink/chapter3_files/figure-html/unnamed-chunk-4-1.png" width="672" />


## Intervals of defined boundaries


```r
# add up posterior prob where p < 0.5

sum(posterior[p_grid < 0.5])
```

```
## [1] 0.1718746
```




```r
quantile(samples, 0.8)
```

```
##       80% 
## 0.7607608
```


```r
quantile(samples, c(0.1, 0.9))
```

```
##       10%       90% 
## 0.4473473 0.8128128
```


```r
PI(samples, prob = 0.5)
```

```
##       25%       75% 
## 0.5415415 0.7387387
```

## Sampling to simulate prediction



```r
dbinom(0:2, size=2, prob=0.7)
```

```
## [1] 0.09 0.42 0.49
```


```r
rbinom(10, size = 2, prob = 0.7)
```

```
##  [1] 2 1 2 2 2 1 0 2 2 2
```

```r
dummy_w <- rbinom(1e5, size = 9, prob = 0.7)

simplehist(dummy_w, xlab="dummy water count")
```

<img src="/courses/stat_rethink/chapter3_files/figure-html/unnamed-chunk-10-1.png" width="672" />





