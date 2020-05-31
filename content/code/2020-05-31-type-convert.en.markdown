---
title: Type Convert
author: Rob
date: '2020-05-31'
slug: type-convert
categories: []
tags: [Tidyverse]
summary: ''
authors: []
external_link: ''
image:
  caption: ''
  focal_point: ''
  preview_only: no
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''
slides: ''
---

Never noticed `type_convert` from `readr` that lets you wrangle data and then convert as appropriate.  Let's give it a spin.


```r
library(tidyverse)

typical_data <- tribble(~bad_col,
                        "3_apple",
                        "4_banana")

typical_data %>% 
  separate(bad_col, into = c("n", "fruit"), sep = "_") %>% 
  type_convert()
```

```
## # A tibble: 2 x 2
##       n fruit 
##   <dbl> <chr> 
## 1     3 apple 
## 2     4 banana
```

Perfect!  
