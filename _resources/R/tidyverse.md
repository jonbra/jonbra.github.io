---
title: "Some snippets on Tidyverse usage"
permalink: /resources/R/tidyverse
excerpt: "A random collection of useful tidyverse commands"
sidebar:
  nav: "resources"
tags:
  - R
  - Tidyverse
---

{% include base_path %}

## Tibbles and strings

Combining `mutate()` with `str_replace()` and `str_remove()`

```
some_tibble %>%
  mutate(New_col = str_replace(Old_col, "pattern", "replacement"))
```

```
some_tibble %>%
  mutate(New_col = str_remove_all(Old_col, "pattern to remove"))
```

## Move every other row into two columns
<script src="https://gist.github.com/jonbra/f8936352bb21717e02ef4dbf9e486920.js"></script>

## Some snippets on using map() to read files  

```
# Read a list of input files into a list
tmp <- list.files(full.names = FALSE, pattern = ".tsv") %>%
  # Keep the file names as the names of the list elements
  set_names() %>% 
  map(read_tsv, col_names = FALSE)

# Reduce the list to a single dataframe. Keep the filenames (list element names) in column 1
# The column name will be "sampleName"
df <- bind_rows(tmp, .id = "sampleName")
```

## Arrange rows in custom order   
Use the `match` function to create a vector of indexes (row numbers) of the rows matching the strings "Honda Civic" and "Camaro Z28" in the column "car". Put this vector into `arrange()` to arrange the rows accordingly.  
```
library(tidyverse)
rownames_to_column(mtcars, var = "car") %>% 
  arrange(match(car, c("Honda Civic", "Camaro Z28")))

#              car  mpg cyl  disp  hp drat    wt  qsec vs am gear carb
# 1    Honda Civic 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
# 2     Camaro Z28 13.3   8 350.0 245 3.73 3.840 15.41  0  0    3    4
# 3      Mazda RX4 21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
# 4  Mazda RX4 Wag 21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
# 5     Datsun 710 22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
# 6 Hornet 4 Drive 21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1

```
