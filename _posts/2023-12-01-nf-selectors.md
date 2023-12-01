---
title: "Nextflow selectors"
excerpt: "Notes on selectors in Nextflow"
tags:
  - Nextflow
  - selectors
---

You can use the full name to a process in for example the `withName` selector to configure a process that is shared by many workflows but which should only apply to one workflow. E.g., `withName: NF_CORE_PIPELINE:SOME_SUBWORKFLOW:SAMTOOLS_SORT`. 
