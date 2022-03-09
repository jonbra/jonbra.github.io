---
title: "Descriptive analysis 3"
permalink: /resources/R/descriptive_analysis_3
sidebar:
  nav: "resources"
tags:
  - R
  - descriptive_analysis
  - dispersion
  - range
  - variance
  - standard_deviation
---

## Contents
- [Notes](#notes)
- [Range](#range)
- [Variance](#variance)
- [Standard Deviation](#standard-deviation)


---------------------
From the book [R for Epidemiology](https://www.r4epi.com/)

We're interested in describing what is "typical", or "average" in our data. [Last time](/resources/R/descriptive_analysis_2) we looked at measures like "mean", "median" and "mode". Now we want to look into things like, “how much like the average person, are the other people in our data set”. Are everyone average? Is average the middle value of everyone else's? If we look at the heights in this dataset:

```r
library(tidyverse)

# Simulate some data
height_and_weight_20 <- tribble(
  ~id,   ~sex,     ~ht_in, ~wt_lbs,
  "001", "Male",   71,     190,
  "002", "Male",   69,     177,
  "003", "Female", 64,     130,
  "004", "Female", 65,     153,
  "005", NA,       73,     173,
  "006", "Male",   69,     182,
  "007", "Female", 68,     186,
  "008", NA,       73,     185,
  "009", "Female", 71,     157,
  "010", "Male",   66,     155,
  "011", "Male",   71,     213,
  "012", "Female", 69,     151,
  "013", "Female", 66,     147,
  "014", "Female", 68,     196,
  "015", "Male",   75,     212,
  "016", "Female", 69,     19000,
  "017", "Female", 66,     194,
  "018", "Female", 65,     176,
  "019", "Female", 65,     176,
  "020", "Female", 65,     102
)

# Recreate our mode function
mode_val <- function(x) {
  value_counts <- table(x)
  result <- names(value_counts)[value_counts == max(value_counts)]
  if (length(value_counts) == length(result)) {
    result <- NA
  }
  result
}

height_and_weight_20 %>%
  summarise(
    min_height    = min(ht_in),
    mean_height   = mean(ht_in),
    median_height = median(ht_in),
    mode_height   = mode_val(ht_in) %>% paste(collapse = " , "),
    max_height    = max(ht_in)
  )
```

```r
## # A tibble: 1 × 5
##   min_height mean_height median_height mode_height max_height
##        <dbl>       <dbl>         <dbl> <chr>            <dbl>
## 1         64        68.4          68.5 65 , 69             75
```

Remember that the mean or the median does not have to be an actual value, or observation, in the data. So therefore, we might want to investigate things like, _how much like the average person are the other people in the dataset? Are all persons the same height? Is the average the middle value, or is the distribution skewed?_

This is called to investigate the **dispersion** (spredning tenker jeg?), and there are three common measures of dispersion:
- **Range**
- **Variance**
- **Standard Deviation**

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/self_study/dispersion.png" alt="">
  <figcaption>From the book R for Epidemiology (https://www.r4epi.com/)</figcaption>

## Range
The range is the difference between the minimum and maximum value.

In our dataset the range is 11.

```r
height_and_weight_20 %>%
  summarise(
    min_height  = min(ht_in),
    mean_height = mean(ht_in),
    max_height  = max(ht_in),
    range       = max_height - min_height
  )
## # A tibble: 1 × 4
##   min_height mean_height max_height range
##        <dbl>       <dbl>      <dbl> <dbl>
## 1         64        68.4         75    11
```

The range is often useful, but it doesn't tell anything about the distribution. Are most people located on either end of the range, or in the middle, or evenly distributed?

## Variance
Variance measures the _variability from the average or the mean_. You take the difference of each observation in the data set from the mean, squaring the differences (to get positive values), and dividing the sum of these differences to the mean by the number of observations in the data set. In this way, the variance takes into account the spread of all the data points in the data set.

The variance is often denoted `s^2`

In R, the function `var()` can calculate the variance.

## Standard Deviation
