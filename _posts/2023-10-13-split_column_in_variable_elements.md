---
title: Split column with variable number of elements
layout: single
excerpt: "Use str_split() to split a column when the number of elements is variable"
tags:
  - R
---

Based on this [StackOverflow thread](https://stackoverflow.com/questions/33288695/how-to-use-tidyrseparate-when-the-number-of-needed-variables-is-unknown)  

With Tidyverse, the `separate()` function can be used to split different elements in a column into individual columns. 
But this is not straightforward when the number of elements is variable in each row. 
One solution is to use `str_split()` instead to create lists of elements in each row. And then `unnest()` the lists, which will give you a table in long format.
This can be spread to wide format later with `pivot_wider()` if you need to.



```
library(tidyverse)

# Create example dataset
df <- rownames_to_column(mtcars, var = "car") %>% as_tibble()

# Split the car column on space
df %>% 
  mutate(new_col = str_split(car, " ")) %>% 
  select(car, new_col)

# A tibble: 32 × 2
   car               new_col  
   <chr>             <list>   
 1 Mazda RX4         <chr [2]>
 2 Mazda RX4 Wag     <chr [3]>
 3 Datsun 710        <chr [2]>
 4 Hornet 4 Drive    <chr [3]>
 5 Hornet Sportabout <chr [2]>
 6 Valiant           <chr [1]>
 7 Duster 360        <chr [2]>
 8 Merc 240D         <chr [2]>
 9 Merc 230          <chr [2]>
10 Merc 280          <chr [2]>
# ℹ 22 more rows
# ℹ Use `print(n = ...)` to see more rows


# Each element from the car column is a list in new_col
# Use unnest() to separate the different elements into a long format
df %>% 
  mutate(new_col = str_split(car, " ")) %>% 
  select(car, new_col) %>% 
  unnest(new_col)

# A tibble: 66 × 2
   car            new_col
   <chr>          <chr>  
 1 Mazda RX4      Mazda  
 2 Mazda RX4      RX4    
 3 Mazda RX4 Wag  Mazda  
 4 Mazda RX4 Wag  RX4    
 5 Mazda RX4 Wag  Wag    
 6 Datsun 710     Datsun 
 7 Datsun 710     710    
 8 Hornet 4 Drive Hornet 
 9 Hornet 4 Drive 4      
10 Hornet 4 Drive Drive  
# ℹ 56 more rows
# ℹ Use `print(n = ...)` to see more rows
```
