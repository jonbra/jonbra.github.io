---
title: "Plotting coverage from bamfiles"
permalink: /resources/visualization/coverage_plot/
excerpt: "Plotting the mapping of reads from bamfiles with samtools depth and R"
sidebar:
  nav: "resources"
categories:
  - R
---

{% include base_path %}

*Under development.*

Using `samtools depth` and `R` to plot the coverage of mapping from bamfiles. 
`-aa` option to include all nucleotides in reference, and `-d` to increase the maximum coverage depth.

```
# Using samtools v.1.3.1
# Setting max depth to 1000000 with -d (8000 default).
samtools depth -aa -d 1000000 input.bam | grep "contig_youwant_to_count" | gzip > coverage.txt.gz

# Enter R
library(tidyverse)
# Load data
cov <- as.tbl(read.table(file = "coverage.txt.gz")) %>% 
	rename("Position" = V2) %>% rename("Coverage" = V3)
# Plot
cov %>% select(Position, Coverage) %>% 
	ggplot(aes(Position, Coverage)) + 
	geom_line()
```