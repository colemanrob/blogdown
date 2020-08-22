---
title: Tidyverse tricks
author: ''
date: '2020-06-07'
slug: dplyr-tricks
categories:
tags: [coding, tidyverse]
subtitle: ''
summary: ''
authors: []
lastmod: '2020-06-07T16:11:23-04:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

The following is a list of functions that are super useful in the tidyverse that help solve a wide range of problems:

* [add_count](#add_count)
* [crossing](#crossing)
* [across](#across)





```r
# Get data from tidytues

library(tidyverse)
cocktails <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-26/cocktails.csv')
```


# `add_count`

adds a count based on the variable supplied.  Equivalent to group_by, summarize, ungroup.


```r
cocktails %>% 
  add_count(drink) %>% 
  filter(n >= 12) %>% 
  knitr::kable()
```



| row_id|drink            |date_modified       | id_drink|alcoholic |category         |drink_thumb                                                          |glass         |iba |video | ingredient_number|ingredient      |measure               |  n|
|------:|:----------------|:-------------------|--------:|:---------|:----------------|:--------------------------------------------------------------------|:-------------|:---|:-----|-----------------:|:---------------|:---------------------|--:|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 1|Angelica root   |3 tblsp chopped       | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                10|Water           |1/4 cup               | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                11|Food coloring   |1 drop yellow         | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                12|Food coloring   |1 drop green          | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 2|Almond          |1 tblsp chopped       | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 3|Allspice        |1 cracked             | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 4|Cinnamon        |1 one-inch            | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 5|Anise           |3-6 crushed           | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 6|Coriander       |1/8 tsp powdered      | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 7|Marjoram leaves |1 tblsp fresh chopped | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 8|Vodka           |1.5 cup               | 12|
|     88|Angelica Liqueur |2016-08-31 19:21:12 |    12794|Alcoholic |Homemade Liqueur |http://www.thecocktaildb.com/images/media/drink/yuurps1472667672.jpg |Collins Glass |NA  |NA    |                 9|Sugar           |1/2 cup granulated    | 12|

# `crossing`

generates all possible combinations of variables (like expand.grid, but returns a dataframe)


```r
crossing(
  a = 1:2,
  b = c("a", "b"),
  c = c("x", "y")
) %>% 
  knitr::kable()
```



|  a|b  |c  |
|--:|:--|:--|
|  1|a  |x  |
|  1|a  |y  |
|  1|b  |x  |
|  1|b  |y  |
|  2|a  |x  |
|  2|a  |y  |
|  2|b  |x  |
|  2|b  |y  |

# `across`


```r
character_visualization <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-06-30/character_visualization.csv')
```

```
## Parsed with column specification:
## cols(
##   issue = col_double(),
##   costume = col_character(),
##   character = col_character(),
##   speech = col_double(),
##   thought = col_double(),
##   narrative = col_double(),
##   depicted = col_double()
## )
```

```r
top_speech <- character_visualization %>% 
  filter(!(character %in% c("Editor narration", "Omnipresent narration"))) %>% 
  group_by(character) %>% 
  summarize(across(speech:depicted, sum)) %>% 
  slice_max(order_by = speech, n = 15) %>% 
  separate(character, into = c("hero", "real_name"), sep = " = ") %>% 
  mutate(pct_speech = speech/sum(speech)) %>% 
  arrange(desc(pct_speech)) %>% 
  select(-real_name) %>% 
  select(hero, pct_speech, everything()) %>% 
  mutate(pct_speech = round(pct_speech, 3))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

