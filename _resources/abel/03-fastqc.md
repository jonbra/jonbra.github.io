---
title: "Checking sequence quality using FastQC"
permalink: /resources/fastqc/
excerpt: "How to run FastQC on your sequence data"
tags:
  - unix
categories:
  - abel
---

### Quality check using FastQC
The first thing you should do when getting new sequence data, either DNA or RNA, is to run a tool such as [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) to check the quality of the reads, presence of sequencing adapters, GC-content etc. Fastqc is available on Abel via the command `module load fastqc`.  

The easiest way to run FastQC is simply `fastqc *.fastq.gz` inside the directory with the sequence data (given that your sequence files ends with `fastq.gz`). 

If you have a lot of sequence libraries it is wise to start FastQC as a slurm-job. Here is a script which loops over all the files ending with `.fastq.gz` and runs the program. Just paste this text into a file called `fastqc_loop.slurm` and run it by typing `sbatch fastqc_loop.slurm`:

```
#!/bin/sh
#SBATCH --job-name=fastqc
#SBATCH --account=uio
#SBATCH --time=5:00:00
#SBATCH --mem-per-cpu=8G
#SBATCH --output=slurm-%j.base

## Set up job environment:
source /cluster/bin/jobsetup
module purge   # clear any inherited modules
set -o errexit

module load fastqc

for i in ../SequenceData/RawSeqs/*.fq.gz
do
        fastqc -o ../Analyses/FastQC/ $i
done
```

Here I assume that you are in a directory (preferably named `Scripts`) which is on the same level as the `SequenceData` directory and that the raw sequences are in a directory named `RawSeqs` inside `SequenceData`. The directory `Analyses` (with `FastQC` inside) should be on the same level as `SequenceData` and `Scripts` (see [this](https://jonbra.github.io/resources/setting-up-project/) setup). 
