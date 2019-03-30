---
title: "Useful R commands"
permalink: /resources/r_commands
excerpt: "A random, and growing, collection of useful R commands"
sidebar:
  nav: "resources"
tags:
  - R
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