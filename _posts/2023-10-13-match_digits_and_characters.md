---
title: Use stringr to extract only digits or only characters
layout: single
excerpt: "Use R regex and the stringr package to match only digits or only characters"
tags:
  - R
---

`\\d+` will match one or more digits. `\\D` will match all non-digit characters. Example:  

```
library(tidyverse)

# Sample text string
text <- "Hello 123 World 456"

# Remove numbers using str_replace_all
str_replace_all(text, "\\d+", "")
[1] "Hello  World "

str_replace_all(text, "\\D", "")
[1] "123456"
```
