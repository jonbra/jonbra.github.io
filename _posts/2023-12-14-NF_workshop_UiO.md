---
title: "NF workshop UiO"
excerpt: "Notes from the NF workshop at UiO in December 2023"
tags:
  - Nextflow
---

Course page: [https://www.mn.uio.no/sbi/english/events/oslo-bioinformatics-workshop-week-2023/index.html](https://www.mn.uio.no/sbi/english/events/oslo-bioinformatics-workshop-week-2023/index.html)  

## Notes  
Tasks: For example one task per fastq file pair. So one process for FastQC, but several tasks for each file pair.

There is something called nf-prov - for provenance and clinical data. Might be relevant for NIPH someday...  

[nf-test](https://code.askimed.com/nf-test/). Testing framework for Nextflow pipelines. For unit testing.  

nf-core community have a big pot of hours on GitPod available to share with us...  

Fix the Nextflow version before you run a pipeline. Also fix the version of the pipeline.
export NXF_VER=23.10.0  


The output of a process can be the standard out:  
```
output:
stout
```

### The work directory:  
.command.run is a wrapper file around the .command.sh script. 
You can for example run .command.run when you use a docker container. The mounting instructions and input files will be there.  

Q: How to output a value from a process into a channel?

### nextflow log  
You can `-resume` specific runs. Not only the last one.  


### Channel.fromPath:
Nextflow will then actually stage _files_ into the process and not simply the filenames as strings (if e.g., with `channel.of()`).  


config:
process.MULTIQC.container = ''
process withName: {

}

### nf-core create-params-file
Will create a params file based on the needed parameters of the pipeline

### Use branch to split the samplesheet by AGENS?

## NF-core
Shared configs. For example for different clusters. 
Test data: https://nf-co.re/docs/contributing/test_data_guidelines  
https://github.com/nf-core/test-datasets
Nextflow will recognize https or ftp addresses to files and automatically download them. 

Add pipeline: https://nf-co.re/docs/contributing/adding_pipelines  

### Automation
Harshil Patel talk in the Barcelona 2023 Summit. Automation of the Seqera platform.

### Random notes  
Don't need to specify `main.nf`. Just give the directory name to `nextflow run`.

nf-core tools can also remove a module. 

Can install subworkflows with nf-core tools instead of modules. E.g. bowtie align samtools...

nextflow run somepipeline --help - the help menu comes from the schema file. 

#### How to edit the schema file:  
`nf-core schema build`
They have a web builder also. 