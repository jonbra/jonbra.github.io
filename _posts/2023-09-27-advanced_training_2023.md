---
title: "Advanced Training - September 2023"
excerpt: "Notes from the advanced training in September 2023"
tags:
  - Nextflow
  - map
  - operators
---

[Youtube Link](https://www.youtube.com/watch?v=nPAH9owvKvI&t=2725s)

[Training material and info](https://training.nextflow.io/advanced/operators/)

Slack channel: #sept23-advanced-training

# Operators:

## The map operator

The map operator:  

```
workflow {
    Channel.of( 1, 2, 3, 4, 5 )
    | map { it * it }
    | view
}
```

Each element of the channel is being multiplied by itself. By default, each element of the list in the channel is named "it". You can use "->" to name the elements. 

```
workflow {
    Channel.of( 1, 2, 3, 4, 5 )
    | map { num -> num * num }
    | view
}
```  

Here each of the integers in the list will have the name "num" for each of the repeated actions performed on the elements. 

I also notice that they use "|" as a pipe symbol. Does it work also with "."? Seems like it, but have to use `view()` instead of simply `view`  

Maps in groovy are represented by key:value pairs inside square brackets. 

## The view operator  

View the outputs of a pipeline, or channel. 
It's possible to customize the output. E.g., : `view { "Found: $it"}`.  


## The splitCsv operator  

When using the `splitCsv()` operator, with `header: true`, the column names are mapped to the different elements, or values, of the columns/cells in the csv. 
With the header: true argument we can use the header names to access the csv elements. 

samplesheet.csv:  
```
id,repeat,type,fastq1,fastq2
sampleA,1,normal,data/reads/sampleA_rep1_normal_R1.fastq.gz,data/reads/sampleA_rep1_normal_R2.fastq.gz
sampleA,1,tumor,data/reads/sampleA_rep1_tumor_R1.fastq.gz,data/reads/sampleA_rep1_tumor_R2.fastq.gz
sampleA,2,normal,data/reads/sampleA_rep2_normal_R1.fastq.gz,data/reads/sampleA_rep2_normal_R2.fastq.gz
```

``` 
workflow {
    Channel.fromPath("data/samplesheet.csv")
    | splitCsv ( header: true )
    | view
}

operators -> nextflow run main.nf 
N E X T F L O W  ~  version 23.04.3
Launching `main.nf` [small_boltzmann] DSL2 - revision: b156d7f712
[id:sampleA, repeat:1, type:normal, fastq1:data/reads/sampleA_rep1_normal_R1.fastq.gz, fastq2:data/reads/sampleA_rep1_normal_R2.fastq.gz]
[id:sampleA, repeat:1, type:tumor, fastq1:data/reads/sampleA_rep1_tumor_R1.fastq.gz, fastq2:data/reads/sampleA_rep1_tumor_R2.fastq.gz]
[id:sampleA, repeat:2, type:normal, fastq1:data/reads/sampleA_rep2_normal_R1.fastq.gz, fastq2:data/reads/sampleA_rep2_normal_R2.fastq.gz]
[id:sampleA, repeat:2, type:tumor, fastq1:data/reads/sampleA_rep2_tumor_R1.fastq.gz, fastq2:data/reads/sampleA_rep2_tumor_R2.fastq.gz]
```  

Notice the key:value pairs inside square brackets.  

To extract three specific columns into a tuple (notice the use of the `file()` method to make sure that these are paths to files):  
```
workflow {

    reads = Channel.fromPath("data/samplesheet.csv")
    .splitCsv ( header: true )
    .map{ row -> tuple(row.id, file(row.fastq1), file(row.fastq2))}
    .view()
}

operators -> nextflow run main.nf 
N E X T F L O W  ~  version 23.04.3
Launching `main.nf` [elegant_lagrange] DSL2 - revision: 2c9851a553
[sampleA, [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_normal_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_normal_R2.fastq.gz]]
[sampleA, [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_tumor_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_tumor_R2.fastq.gz]]
[sampleA, [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep2_normal_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep2_normal_R2.fastq.gz]]
[sampleA, [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep2_tumor_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep2_tumor_R2.fastq.gz]]
```  

This would be the same. Using the default "it" name:
```
workflow {
    Channel.fromPath("data/samplesheet.csv")
    | splitCsv( header: true )
    | map { [it.id, [file(it.fastq1), file(it.fastq2)]] }
    | view
}
```  

What happens here is that we are iterating over _each row_ in the csv file. 

It can be a good idea to keep as much metadata as possible through the workflow. Little cost of doing that. 

Map objects are equivalent to python dictionaries.

```
workflow {

    reads = Channel.fromPath("data/samplesheet.csv")
    .splitCsv ( header: true )
    .map { row -> 
        meta = [id: row.id, type: row.type, repeat: row.repeat] 
        [meta, tuple(file(row.fastq1), file(row.fastq2))]
    }
    .view()
}

N E X T F L O W  ~  version 23.04.3
Launching `main.nf` [evil_hopper] DSL2 - revision: 9c91ffa343
[[id:sampleA, type:normal, repeat:1], [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_normal_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_normal_R2.fastq.gz]]
[[id:sampleA, type:tumor, repeat:1], [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_tumor_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep1_tumor_R2.fastq.gz]]
[[id:sampleA, type:normal, repeat:2], [/workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep2_normal_R1.fastq.gz, /workspace/gitpod/nf-training/advanced/operators/data/reads/sampleA_rep2_normal_R2.fastq.gz]]
```  

Here we gather the id, type and repeat columns into a map object. And then we gather that map object (meta. I think often referred to as the metamap) in a tuple with the two fastq file path columns. We can access the different elements in meta later in the processes by e.g., meta.id. 