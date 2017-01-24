---
title: "Setting up a project on Abel"
permalink: /resources/setting-up-project/
excerpt: "How to set up a bioinformatics project on Abel at UiO"
tags:
  - unix
categories:
  - abel
---

A lot of this is taken from the book: [_Bioinformatics Data Skills_](http://shop.oreilly.com/product/0636920030157.do) by Vince Buffalo 2015 (This book is recommended when you get more into bioinformatics).

### What is a project?
It can sometimes be a little hard to know when a new project starts and when you are just continuing an old one. Usually I think of a master thesis or a publication as one single project.

### Organizing a project
First create a directory for the entire project, e.g. `mkdir MyProject`.  
Inside the directory I usually have the following top level directories: `SequenceData`, `Scripts` and `Analyses`. In addition it is good to have a README file (usually written in Markdown format. Read more about Markdown [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet). On a Mac the [MacDown](http://macdown.uranusjr.com/) editor is great).  

Inside the `SequenceData` directory I have at least two directories, `RawSeqs` which contains the unprocessed sequences data as they come straight from the sequencing facility. This data should never be edited or changed in any way (read only). In addition I make one directory for processed sequences called `TrimmedSeqs` where I put the output of quality trimming. Intermediate files and output of the different analyses should go under `Analyses`. The files in this directory should be easily re-created by running the different scripts, so it is not crucial to keep everything here. Make sure to organize the `Analyses` folder well with many sub-directories as this will easily get crowded. 

In the end you might have a directory structure like this:

```
MyProject
│   README.md  
│
│───SequenceData
│   │
│   ├───RawSeqs
│   │   │   file1.fq.gz
│   │   │   file2.fq.gz
│   │   │   ...
│   │
│   ├───TrimmedSeqs
│   │   │   Trimmed_file1.fq.gz
│   │   │   Trimmed_file2.fq.gz
│   │   │   ...
│
│───Scripts
│
└───Analyses
    │
    ├───FastQC
    │   │   file1.fastqc
    │   │   file2.fastqc
    │   │   ...
    │
    ├───Mapping
    │   │
    │   ├───TopHat_out
    │   │   │   ...
    │
```

The first thing you should do with your sequence data is to check the quality of the reads and the presence of sequencing adapters, poly-A tails, pcr primers etc. 
