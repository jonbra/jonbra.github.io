---
title: "R for Epidemiology"
permalink: /resources/R/epidemiology
sidebar:
  nav: "resources"
tags:
  - R
  - descriptive_analysis
  - factors
  - summarise
  - group_by
  - count
  - percentage
  - median
  - mean
  - mode
  - index
  - dispersion
  - range
  - variance
  - standard_deviation
---

## Contents
- [Descriptive analysis](#descriptive-analysis)
  * [Categorical data](#categorical-data)
    + [Factors in R](#factors-in-r)
    + [Height and Weight Data](#create-height-and-weight-data)  
    + [Calculating frequencies](#calculating-frequencies)
    + [Calculating percentages](#calculating-percentages)
    + [Missing data](#missing-data)
  * [Numerical variables](#numerical-variables)
    + [Comparison](#comparison)
    + [Range](#range)
    + [Variance](#variance)
    + [Standard Deviation](#standard-deviation)
    + [Comparing distributions](#comparing-distributions)

---------------------
These are my notes from reading parts of the book [R for Epidemiology](https://www.r4epi.com/).  

# Descriptive analysis

Descriptive analysis is sometimes called **Exploratory analysis**.

Descriptive statistics is often a good **starting point**.

There are at least three reasons why we want to start with a descriptive analysis:
1. We can use descriptive analysis to uncover errors in our data.
2. It helps us understand the distribution of values in our variables.
3. Descriptive analysis serve as a starting point for understanding relationships between our variables.  

Variables can be **numerical** or **categorical**.
- Numeric variables can be **continuous** or **discrete**.
- Categorical variables can be **ordinal** (oridinalnivå in Norwegian) or **nominal** (nominalnivå). Ordinal variables can be **sorted/ordered** _and_ **classified** (e.g. low, medium, high), while nominal variables can _only_ be classified (e.g. man or woman).

All types of variables can be analysed with either numerical or graphical methods.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/self_study/numeric_categorical_variables_flowchart_full.png" alt="">
  <figcaption>From the book R for Epidemiology (https://www.r4epi.com/)</figcaption>

# Categorical data  

## Factors in R

**Factors** are useful when operating on categorical data. Often you don't want to have categories as characters in R. E.g. because R summarizes characters alphabetically by default. It's easier to determine the order of factors. With factors it's also possible to specify cases that are not in our data (not observed).

### Coercing variables into factors
The code below shows one method for coercing a numeric vector into a factor.

```r
# Load dplyr for tibble(), pipe and mutate()
library(dplyr)

demo <- tibble(
  id       = c("001", "002", "003", "004"),
  age      = c(30, 67, 52, 56),
  edu      = c(3, 1, 4, 2),
  edu_char = c(
    "Some college", "Less than high school", "College graduate",
    "High school graduate"
  )
)
```

Using `mutate()` to add a factor column.
```r
demo <- demo %>%
  mutate(
    edu_f = factor(
      x      = edu,
      levels = 1:4,
      labels = c(
        "Less than high school", "High school graduate", "Some college",
        "College graduate"
      )
    )
  )
```

```r
demo
# A tibble: 4 × 5
  id      age   edu edu_char              edu_f                
  <chr> <dbl> <dbl> <chr>                 <fct>                
1 001      30     3 Some college          Some college         
2 002      67     1 Less than high school Less than high school
3 003      52     4 College graduate      College graduate     
4 004      56     2 High school graduate  High school graduate
```

Here we create a new column with the factors instead of overwriting the original `edu` column. That is often wise in order to keep the numeric version of the data for reference. Here they also use `_f` as a naming convention for factors.

To create a vector of factors we use the `factor()` function. And we pass the numerial vector `edu` to the `x` argument of `factor()`. So we kind of map the different factors to elements in the numeric vector (but these are mapped via `levels` I think...). And then we also need to specify the `levels`. `levels` tells R the unique values that the new factor variable can take. `labels` are descriptive text assigned to each value in the `levels` argument. _The order of the labels in the character vector we pass to the labels argument should match the order of the values passed to the levels argument._ For example, the ordering of levels and labels above tells R that 1 should be labeled with “Less than high school,” 2 should be labeled with “High school graduate,” etc.  

Even though the factors look just like the character vector when they are printed, they are actually integers under the hood.

```r
as.numeric(demo$edu_char)
## Warning: NAs introduced by coercion
## [1] NA NA NA NA

as.numeric(demo$edu_f)
## [1] 3 1 4 2
```

Factors allows us to have _unobserved_ values in our data:
```r
demo <- demo %>%
  mutate(
    edu_5cat_f = factor(
      x      = edu,
      levels = 1:5,
      labels = c(
        "Less than high school", "High school graduate", "Some college",
        "College graduate", "Graduate school"
      )
    )
  )

table(demo$edu_5cat_f)

Less than high school  High school graduate          Some college
                    1                     1                     1
     College graduate       Graduate school
                    1                     0
```

We can also coerce a character vector into factors:
```r
demo <- demo %>%
  mutate(
    edu_f_from_char = factor(
      x      = edu_char,
      levels = c(
        "Less than high school", "High school graduate", "Some college",
        "College graduate", "Graduate school"
      )
    )
  )
```
Here, because the levels already _are_ character strings, we don't need to add any `labels` argument. _Keep in mind, though, that the order of the values passed to the levels argument matters. It will be the order that the factor levels will be displayed in your analyses._

[Top](#contents)

## Create Height and Weight Data

```r
library(dplyr)
# Simulate some data
height_and_weight_20 <- tibble(
  id = c(
    "001", "002", "003", "004", "005", "006", "007", "008", "009", "010", "011",
    "012", "013", "014", "015", "016", "017", "018", "019", "020"
  ),
  sex = c(1, 1, 2, 2, 1, 1, 2, 1, 2, 1, 1, 2, 2, 2, 1, 2, 2, 2, 2, 2),
  sex_f = factor(sex, 1:2, c("Male", "Female")),
  ht_in = c(
    71, 69, 64, 65, 73, 69, 68, 73, 71, 66, 71, 69, 66, 68, 75, 69, 66, 65, 65,
    65
  ),
  wt_lbs = c(
    190, 176, 130, 154, 173, 182, 140, 185, 157, 155, 213, 151, 147, 196, 212,
    190, 194, 176, 176, 102
  )
)

```

[Top](#contents)  

## Calculating frequencies
The first thing we want to do is to generate some summary statistics. The `summarise()` function is useful for this. We can use another function, `n()`, inside `summarise()` to count rows. By default, it will count all the rows in the data frame (there are 20 rows):
```r
height_and_weight_20 %>%
  summarise(n())

## # A tibble: 1 × 1
##   `n()`
##   <int>
## 1    20
```
The output is a new data frame.

Now we want to count how many rows have the value Female and Male in the `sex_f` column. Then first group the data using `group_by()`:
```r
height_and_weight_20 %>%
  group_by(sex_f) %>%
  summarise(n())

## # A tibble: 2 × 2
##   sex_f  `n()`
##   <fct>  <int>
## 1 Male       8
## 2 Female    12
```

Using the `n()` function along will create a column with the counts called `n()`. Name the count column like this:
```r
height_and_weight_20 %>%
  group_by(sex_f) %>%
  summarise(n = n())
```

Actually, grouping and counting can be done in a single step using the `count()` function (the `add_count()` function will add the counts as a new column of the original data frame instead of creating a new data frame):
```r
height_and_weight_20 %>%
  count(sex_f)

## # A tibble: 2 × 2
##   sex_f      n
##   <fct>  <int>
## 1 Male       8
## 2 Female    12
```

[Top](#contents)  

## Calculating percentages
Because the `count()` function produces a data frame (or a tibble) we can pipe the output into other functions, like `mutate()`:
```r
height_and_weight_20 %>%
  count(sex_f) %>%
  mutate(prop = n / sum(n))

## # A tibble: 2 × 3
##   sex_f      n  prop
##   <fct>  <int> <dbl>
## 1 Male       8   0.4
## 2 Female    12   0.6
```r
The `count()` function produces a column named `n`. And here we create a new column with n divided by the sum of all numbers in the n column.

To calculate the percentage:
```r
height_and_weight_20 %>%
  count(sex_f) %>%
  mutate(percent = n / sum(n) * 100)

## # A tibble: 2 × 3
##   sex_f      n percent
##   <fct>  <int>   <dbl>
## 1 Male       8      40
## 2 Female    12      60
```

[Top](#contents)  

## Missing data
Let's replace some values in `sex_f` with `NA`:
```r
height_and_weight_20 <- height_and_weight_20 %>%
  mutate(sex_f = replace(sex, c(2, 9), NA)) %>%
  print()
## # A tibble: 20 × 5
##    id      sex sex_f ht_in wt_lbs
##    <chr> <dbl> <dbl> <dbl>  <dbl>
##  1 001       1     1    71    190
##  2 002       1    NA    69    176
##  3 003       2     2    64    130
##  4 004       2     2    65    154
##  5 005       1     1    73    173
##  6 006       1     1    69    182
##  7 007       2     2    68    140
##  8 008       1     1    73    185
##  9 009       2    NA    71    157
## 10 010       1     1    66    155
## 11 011       1     1    71    213
## 12 012       2     2    69    151
## 13 013       2     2    66    147
## 14 014       2     2    68    196
## 15 015       1     1    75    212
## 16 016       2     2    69    190
## 17 017       2     2    66    194
## 18 018       2     2    65    176
## 19 019       2     2    65    176
## 20 020       2     2    65    102
```

If we summarise and calculate the frequencies and precentages like above, the missing values are treated as a category of `sex_f`. Sometimes that is ok, and it's a nice way of displaying the data.
```r
height_and_weight_20 %>%
  count(sex_f) %>%
  mutate(percent = n / sum(n) * 100)

## # A tibble: 3 × 3
##   sex_f     n percent
##   <dbl> <int>   <dbl>
## 1     1     7      35
## 2     2    11      55
## 3    NA     2      10
```

If we want to do the calculations on only non-missing data (also known as **complete case analysis**) we can for example filter out the rows with missing values for `sex_f` first (note that many R functions often have the argument `na.rm` that can be used to drop NAs):
```r
height_and_weight_20 %>%
  filter(!is.na(sex_f)) %>%
  count(sex_f) %>%
  mutate(percent = n / sum(n) * 100)

## # A tibble: 2 × 3
##   sex_f     n percent
##   <dbl> <int>   <dbl>
## 1     1     7    38.9
## 2     2    11    61.1
```
[Top](#contents)  

# Numerical variables  

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

[Top](#contents)  

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

[Top](#contents)    

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

[Top](#contents)    

## Variance
Variance measures the _variability from the average or the mean_. You take the difference of each observation in the data set from the mean, squaring the differences (to get positive values), and dividing the sum of these differences to the mean by the number of observations in the data set. In this way, the variance takes into account the spread of all the data points in the data set.

The variance of a sample is often denoted <img src="https://render.githubusercontent.com/render/math?math=s^2">  
[](https://gist.github.com/a-rodin/fef3f543412d6e1ec5b6cf55bf197d7b)  
In R, the function `var()` can calculate the variance.  

[Top](#contents)  

## Standard Deviation  
The square root of the variance is the standard deviation (SD). The sample standard deviation is denoted <img src="https://render.githubusercontent.com/render/math?math=s">, while the population standard deviation is denoted <img src="https://render.githubusercontent.com/render/math?math={\sigma}">.  

Standard deviation of a sample:
<img src="https://render.githubusercontent.com/render/math?math=\sqrt{s^2}">  

So in this cases, the variance is 120 and the standard deviation is <img src="https://render.githubusercontent.com/render/math?math=\sqrt{120}"> which is 10.95 inches (notice the original units).   

What is the standard deviation? The standard deviation is similar to the variance, however as the variance is in squared units it isn't that intuitive. Because standard deviation takes the root of the variance we get the original units. Hence, the standard deviation is more easy to compare against the original numbers, and to other samples. A low standard deviation indicates that the values of the samples are close to the mean, while a high standard deviation indicates a more spread distribution.   

If the values in our sample are normally distributed, then the percentage of values within each standard deviation from the mean follow these rules:

| Standard deviations  | Percent of values |
| ------------- | ------------- |
| 1  | 68%  |
| 2  | 95%  |
| 3  | 99%  |  

In other words, when we know that the mean of our sample is 68.4 inches, and the standard deviation is 10.95 inches we know that 68% of all the students are between 57.45-79.35 inches (1 SD lower and higher than the mean).  

In R we can generate normally distributed data with a given mean and a given standard deviation using the `rnorm` function (this creates random numbers with a given mean and SD):  
```r
rnorm(n=20, mean=68.4, sd=10.05))

 [1] 63.69113 68.89975 84.73759 56.37946 67.44818 63.19714
 [7] 80.50921 58.63412 47.03020 72.54318 74.93411 91.41247
[13] 60.29886 63.09570 54.25223 83.97762 69.61411 74.98939
[19] 65.16676 83.72266
```  

[Top](#contents)  

## Comparing distributions
