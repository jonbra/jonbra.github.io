---
title: Arrange rows in custom order
layout: single
excerpt: "Arrange rows in custom order"
tags:
  - R
  - Tidyverse
---

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