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
