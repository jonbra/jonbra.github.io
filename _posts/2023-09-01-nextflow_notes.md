---
title: "Some notes on using Nextflow"
excerpt: "Some notes on Hybrid deployment, Nextflow Tower and Operators"
tags:
  - Nextflow
---

{% include base_path %}

## Hybrid deployment
It is possible to have some processes running on your local computer and some running through AWS Batch. See the [Nextflow documentation](https://training.nextflow.io/basic_training/executors/#hybrid-deployments)

## Nextflow Tower  
[Nextflow Tower](https://cloud.tower.nf/) does not have access to your data that is analysed in the pipeline. It will only see information about run commands, computer usage, paths to the different work directories and so on. You can run local pipelines, but view the information on the Tower website. You need an access token that is stored locally.  

[Documentation with tutorial on getting started](https://training.nextflow.io/basic_training/tower/).

### Resource optimization  
The plot with boxplots showing the requested computer resources for each process, and the actual resources used. This is useful to optimize how much resources are requested for each process.  

### Organization  
It's possible to create an organization and share info with different users. E.g. at work. And then we can deploy the same pipelines for example to Azure etc. 

## Operators
[Documentation](https://www.nextflow.io/docs/latest/operator.html).  
The `map()` operator allows us to do "something" to the elements of a channel. For example multiply every element with a number. 

`it` is how I understand it a default name of an element in a channel. The name can be changed with `->`.  `map` can also be used to change order of elements, or remove certain elements of a channel.