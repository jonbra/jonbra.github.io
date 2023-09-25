---
title: Combine mutate() with string manipulation
layout: single
excerpt: "Create a new column with mutate() by manipulating strings in another column"
tags:
  - R
  - Tidyverse
---

Combining `mutate()` with `str_replace()` and `str_remove()`

```
some_tibble %>%
  mutate(New_col = str_replace(Old_col, "pattern", "replacement"))
```

```
some_tibble %>%
  mutate(New_col = str_remove_all(Old_col, "pattern to remove"))
```