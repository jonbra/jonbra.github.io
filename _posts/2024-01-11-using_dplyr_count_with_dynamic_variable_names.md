---
title: Using dplyr::count() with variable column names
layout: single
excerpt: "Using dplyr::count() with variable column names"
tags:
  - R
---

Based on this [StackOverflow thread](https://stackoverflow.com/questions/61735329/r-dplyr-calling-count-with-variable-name-now-that-count-is-deprecated) and this [R-bloggers post](https://www.r-bloggers.com/2019/07/bang-bang-how-to-program-with-dplyr/)

If calling the `count()` function inside a custom function for example, using a variable name as input to match the column to count, the variable name will be a string. And because `dplyr` quotes the arguments, we need to unquote the argument.  

One option is to use `across()`:  
`df %>% count(across(colname))`  

Another is to use `!!as.name()`. 

My concrete use: 
```
count_drug <- function(drug) {
  colname <- (paste0("rut_", drug))
  df %>% 
    count(Major_geno, !!as.name(colname)) %>% 
    add_column("drug" = drug) %>% 
    rename(category = !!as.name(colname))
}
```
