---
title: "Descriptive analysis 1"
permalink: /resources/R/descriptive_analysis_1
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
---

## Contents
- [Factors in R](#factors-in-r)
- [Height and Weight Data](#create-height-and-weight-data)  
- [Calculating frequencies](#calculating-frequencies)
- [Calculating percentages](#calculating-percentages)
- [Missing data](#missing-data)

---------------------
From the book [R for Epidemiology](https://www.r4epi.com/)

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

<figure style="width: 580px" class="align-center">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/self_study/numeric_categorical_variables_flowchart_full.png" alt="">
  <figcaption>From the book R for Epidemiology (https://www.r4epi.com/)</figcaption>
</figure>


## Factors in R

**Factors** are useful when operating on categorical data. Often you don't want to have categories as characters in R. E.g. because R summarizes characters alphabetically by default. It's easier to determine the order of factors. With factors it's also possible to specify cases that are not in our data (not observed).

### Coercing variables into factors
The code below shows one method for coercing a numeric vector into a factor.

```
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
```
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

```
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

```
as.numeric(demo$edu_char)
## Warning: NAs introduced by coercion
## [1] NA NA NA NA

as.numeric(demo$edu_f)
## [1] 3 1 4 2
```

Factors allows us to have _unobserved_ values in our data:
```
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
```
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


## Create Height and Weight Data

```
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

## Calculating frequencies
The first thing we want to do is to generate some summary statistics. The `summarise()` function is useful for this. We can use another function, `n()`, inside `summarise()` to count rows. By default, it will count all the rows in the data frame (there are 20 rows):
```
height_and_weight_20 %>%
  summarise(n())

## # A tibble: 1 × 1
##   `n()`
##   <int>
## 1    20
```
The output is a new data frame.

Now we want to count how many rows have the value Female and Male in the `sex_f` column. Then first group the data using `group_by()`:
```
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
```
height_and_weight_20 %>%
  group_by(sex_f) %>%
  summarise(n = n())
```

Actually, grouping and counting can be done in a single step using the `count()` function (the `add_count()` function will add the counts as a new column of the original data frame instead of creating a new data frame):
```
height_and_weight_20 %>%
  count(sex_f)

## # A tibble: 2 × 2
##   sex_f      n
##   <fct>  <int>
## 1 Male       8
## 2 Female    12
```

## Calculating percentages
Because the `count()` function produces a data frame (or a tibble) we can pipe the output into other functions, like `mutate()`:
```
height_and_weight_20 %>%
  count(sex_f) %>%
  mutate(prop = n / sum(n))

## # A tibble: 2 × 3
##   sex_f      n  prop
##   <fct>  <int> <dbl>
## 1 Male       8   0.4
## 2 Female    12   0.6
```
The `count()` function produces a column named `n`. And here we create a new column with n divided by the sum of all numbers in the n column.

To calculate the percentage:
```
height_and_weight_20 %>%
  count(sex_f) %>%
  mutate(percent = n / sum(n) * 100)

## # A tibble: 2 × 3
##   sex_f      n percent
##   <fct>  <int>   <dbl>
## 1 Male       8      40
## 2 Female    12      60
```

## Missing data
Let's replace some values in `sex_f` with `NA`:
```
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
```
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

If we want to do the calculations on only non-missing data (also known as **complete case analysis**) we can for example filter out the rows with missing values for `sex_f` first:
```
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
