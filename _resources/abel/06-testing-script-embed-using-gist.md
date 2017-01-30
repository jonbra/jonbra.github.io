---
title: "Testing Gist and embedding scripts"
permalink: /resources/abel/testing/
excerpt: "How to use a Gist to embed code"
tags:
  - unix
  - mapping
categories:
  - abel
---

<script src="https://gist.github.com/jonbra/e5b116e51ff565a65b839357eeca0ca0.js"></script>

<p>
  <script src="https://gist.github.com/<gist_id>.js"> </script>
  <noscript>
    <pre>
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
    </pre>
  </noscript>
</p>
