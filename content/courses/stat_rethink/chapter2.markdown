---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Chapter 2
menu:
  example:
    parent: Overview
    weight: 1
title: Chapter 1
toc: true
type: docs
weight: 1
---
## Building up Bayesian Inference

* _small_ world (the model) vs _large_ world (reality) - goal of science is to help us move between
* bayesian inference is counting and comparing possibilities, the garden of forking data
* globe tossing example.  Variables:
  * 9 tosses - n = 9
  * W water x 6 
  * L land x 3 
  * _p_ proportion of globe covered by water - parameters (unobserved variables) 

<p>

## Likelihood

 * the number of ways the data are possible after eliminating the ways that are inconsistent with out data (i.e. counting the garden)

The relative number of ways of getting 6 water, holding _p_ at 0.5, and _N = L+W_ at 9.


```r
dbinom(6, size = 9, prob = 0.5)
```

```
## [1] 0.1640625
```

## Prior

_p_ (the probability of sampling water) is the *parameter* of the model.  For every parameter in your model you must provide a distribution of _prior plausibility_.  Where do priors come from?  Engineering choice, domain knowledge etc.

## Posterior

Update the prior given the data - for every unique combination of data, likelihood, parameters and prior there is a unique _posterior distribution_ -- {{< hl >}} the relative plausibility of different parameter values conditional on the data {{< /hl >}}.

Bayesian Updating

3 different conditioning engines or techniques for computing posterior distributions:

* grid approximation
* quadratic approximation
* markov chain monte carlo (MCMC)

## Grid Approximation

Constrain the possible parameter values to only a finite grid of values.


```r
# define grid
p_grid <- seq(from = 0, to = 1, length.out = 20)

# define prior

prior <- rep(1, 20)

# compute likelihood at each value in grid

likelihood <- dbinom(6, size = 9, prob = p_grid)

# compute the product of likelihood and prior

unstd_posterior <- likelihood * prior

# standardize the posterior so it sums to 1

posterior <- unstd_posterior / sum(unstd_posterior)
```

plot the results

```r
plot(p_grid, posterior, type = "b",
     xlab = "probability of water", ylab = "posterior probability")
mtext("20 points")
```

<img src="/courses/stat_rethink/chapter2_files/figure-html/unnamed-chunk-3-1.png" width="672" />


## Quadratic Approximation
Find the peak/curve of the distribution

use the `quap` function from the rethinking package


```r
library(rethinking)
```

```
## Loading required package: rstan
```

```
## Loading required package: StanHeaders
```

```
## Loading required package: ggplot2
```

```
## rstan (Version 2.19.3, GitRev: 2e1f913d3ca3)
```

```
## For execution on a local, multicore CPU with excess RAM we recommend calling
## options(mc.cores = parallel::detectCores()).
## To avoid recompilation of unchanged Stan programs, we recommend calling
## rstan_options(auto_write = TRUE)
```

```
## Loading required package: parallel
```

```
## Loading required package: dagitty
```

```
## rethinking (Version 2.01)
```

```
## 
## Attaching package: 'rethinking'
```

```
## The following object is masked from 'package:stats':
## 
##     rstudent
```

```r
# specify
globe_qa <- quap(
  alist(
    W ~ dbinom(W+L, p), #binomial likelihood
    p ~ dunif(0,1) # uniform prior
  ),
  data = list(W=6, L=3)
)

# display the summary

precis(globe_qa)
```

```
##        mean        sd      5.5%     94.5%
## p 0.6666665 0.1571338 0.4155363 0.9177967
```

## MCMC

Instead of computing or approximating the posterior distribution, MCMC draws samples from the posterior - you end up with samples whose frequencies correspond to posterior plausibilities.



```r
n_samples <- 1000

p <- rep(NA, n_samples)

p[1] <- 0.5

W <- 6
L <- 3

for (i in 2:n_samples) {
  p_new <- rnorm(1, p[i-1], 0.1)
  if (p_new < 0 ) p_new <- abs(p_new)
  if (p_new > 1 ) p_new <- 2 - p_new
  q0 <- dbinom(W, W+L, p[i-1])
  q1 <- dbinom(W, W+L, p_new)
  p[i] <- ifelse(runif(1) < q1/q0, p_new, p[i-1])
}

dens(p, xlim=c(0,1))

curve(dbeta(x, W+1, L+1), lty = 2, add=TRUE)
```

<img src="/courses/stat_rethink/chapter2_files/figure-html/unnamed-chunk-5-1.png" width="672" />

