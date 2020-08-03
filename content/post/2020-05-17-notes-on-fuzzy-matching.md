---
title: Notes on fuzzy matching
author: ''
date: '2019-10-05'
slug: notes-on-fuzzy-matching
categories: []
tags: [Other]
subtitle: ''
summary: ''
authors: []
lastmod: '2019-10-05'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
Recently had the pleasure of fuzzy matching two datasets that included first and last name and date of birth.  Here are some notes:

* [fuzzyjoin package](https://github.com/dgrtwo/fuzzyjoin) - great functions that do most of the heavy lifting
* [string metrics](http://finzi.psych.upenn.edu/library/stringdist/html/stringdist-metrics.html) - there are a lot of ways to measure the difference in strings, selecting a metric depends on the problem


### Toy Example

``` r
library(tidyverse)
library(fuzzyjoin)
```

Create our list of names and dobs.

``` r
inside_list <- tribble(
  ~first, ~last, ~dob,
  "frank", "johnson", "1980-01-01",
  "debbie", "willis", "1982-01-01"
) %>% mutate(dob = parse_date(dob, format = "%Y-%m-%d"))
```

Create a second list to be matched against

``` r
outside_list <- tribble(
  ~first, ~last, ~dob,
  "frank", "johnson", "1980-01-01",
  "debrah", "willis", "1982-01-02"
) %>% mutate(dob = parse_date(dob, format = "%Y-%m-%d"))
```

Use the function of your choice.
``` r
stringdist_join(outside_list, inside_list,
                by = c("first", "last", "dob"),
                ignore_case = TRUE,
                distance_col = "dist",
                mode = "left",
                method = "lv",
                max_dist = 20) %>% 
  mutate(total_distance = dob.dist + first.dist + last.dist) %>% 
  select(-ends_with(".dist")) %>% 
  knitr::kable()
```

| first.x | last.x  | dob.x      | first.y | last.y  | dob.y      | dist | total\_distance |
| :------ | :------ | :--------- | :------ | :------ | :--------- | :--- | --------------: |
| frank   | johnson | 1980-01-01 | frank   | johnson | 1980-01-01 | NA   |               0 |
| frank   | johnson | 1980-01-01 | debbie  | willis  | 1982-01-01 | NA   |              14 |
| debrah  | willis  | 1982-01-02 | frank   | johnson | 1980-01-01 | NA   |              14 |
| debrah  | willis  | 1982-01-02 | debbie  | willis  | 1982-01-01 | NA   |               4 |

You can see the difference between _debrah_ and _debbie_ is 4.

Setting the distance threshold is often a function of how exact you want the matches to be.

See the [fuzzyjoin](http://varianceexplained.org/fuzzyjoin/) reference for more.


