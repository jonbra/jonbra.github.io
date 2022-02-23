---
title: "Descriptive analysis 2"
permalink: /resources/R/descriptive_analysis_2
sidebar:
  nav: "resources"
tags:
  - R
  - descriptive_analysis
  - median
  - mean
  - mode
  - index
  - summarise
---

## Contents
- [Notes](#notes)
- [Comparison](#comparison)


---------------------
From the book [R for Epidemiology](https://www.r4epi.com/)

## Introduction
We're working with **numerical** variables this time.

**Central tendency** - trying to describe what is _typical_. We're looking at three measures of central tendency here:
- The mean
- The median
- The mode

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/self_study/central_tendency_01.png" alt="">
  <figcaption>From the book R for Epidemiology (https://www.r4epi.com/)</figcaption>

**The mean**
Arithmetic mean is the sum of a collection of numbers/values divided by the number of values. The arithmetic mean is what we often refer to when we talk about __the mean__.

The arithmetic mean is vulnerable to outliers and extreme values, and also when the distributions are skewed.
Also remember that the arithmetic mean does not necessarily have to be a number that is actually part of the data.  

**The Median**  
If you have an odd number of observations, the median is the same as the middle number when they are ordered. However, when there's and even number then the median is the mean of the two middle numbers. Hence, the median is not always observed in the data. Just like for the mean.

**The Mode**
The mode is the value most often observed in our data. This means there can be more than one mode, or there can be no mode at all if all values are equally frequent. And the mode is, by definition, always observed in our data.

To calculate the mean and median in R we can just use the built-in `mean()` and `median()`. However, there's no predefined _mode_ function (the `mode()` function in base R does something else, it shows what kind of object we have). This is one example of calculating the mode:

```r
library(dplyr)

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
```

```r
mode_val <- function(x) {

  # Count the number of occurrences for each value of x
  value_counts <- table(x)

  # Get the maximum number of times any value is observed
  max_count <- max(value_counts)

  # Create an index vector that identifies the positions that correspond to
  # count values that are the same as the maximum count value: TRUE if so
  # and false otherwise
  index <- value_counts == max_count
  # Looks something like this:
  #   64    65    66    68    69    71    73    75
  # FALSE  TRUE FALSE FALSE  TRUE FALSE FALSE FALSE

  # Use the index vector to get all values that are observed the same number
  # of times as the maximum number of times that any value is observed
  unique_values <- names(value_counts)
  result <- unique_values[index]
  # This subsets the index vector and retrieves the names of the vector that are TRUE

  # If result is the same length as value counts that means that every value
  # occured the same number of times. If every value occurred the same number
  # of times, then there is no mode
  no_mode <- length(value_counts) == length(result)
  # I guess we could have also used unique_values instead of value_counts

  # If there is no mode then change the value of result to NA
  if (no_mode) {
    result <- NA
  }
  # The if statement is only executed if no_mode is TRUE. So the object no_mode can exists, but the if statement will not be run if the object is FALSE.

  # Return result
  result
}
```

```r
mode_val(height_and_weight_20$ht_in)
## [1] "65" "69"
```

## Comparison
Here's a nice example of using the `summarise()` function to create new columns with different descriptive statistics. Notice how several new columns can be created in a single summarise function. These will all be present in the new tibble.

This is also a nice way to quickly inspect the data. To see the minimum and maximum values. And the comparison of the mean and median, in relation to the min and max values gives an indication that there could be outliers in the data.

```r
height_and_weight_20 %>%
  summarise(
    min_weight    = min(wt_lbs),
    mean_weight   = mean(wt_lbs),
    median_weight = median(wt_lbs),
    mode_weight   = mode_val(wt_lbs) %>% as.double(),
    max_weight    = max(wt_lbs)
  )
```

```r
## # A tibble: 1 × 5
##   min_weight mean_weight median_weight mode_weight max_weight
##        <dbl>       <dbl>         <dbl>       <dbl>      <dbl>
## 1        102       1113.          176.         176      19000
```
