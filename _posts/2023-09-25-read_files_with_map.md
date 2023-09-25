---
title: Using map() to read files
layout: single
excerpt: "Some snippets on using map() to read files"
tags:
  - R
---

## Some snippets on using map() to read files  

```
library(tidyverse)

# Read a list of input files into a list
tmp <- list.files(full.names = FALSE, pattern = ".tsv") %>%
  # Keep the file names as the names of the list elements
  set_names() %>% 
  map(read_tsv, col_names = FALSE)

# Reduce the list to a single dataframe. Keep the filenames (list element names) in column 1
# The column name will be "sampleName"
df <- bind_rows(tmp, .id = "sampleName")
```
