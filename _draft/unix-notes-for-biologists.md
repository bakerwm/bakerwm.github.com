---
layout: post
title: "[Notes] Unix & Perl Biologists"
description: ""
category: 
- Perl
tags: [Unix, Perl]
---
{% include JB/setup %}

Make some notes when reading the web: Unix & Perl Primer for Biologists, published by [the Korf Lab](http://korflab.ucdavis.edu/)

# 1. Project 6 - Coden usage of a GeneBank file

Here are my opinion:    

  * use subroutine to calculate the usage of one seq    
  * check seq format: begin with `ATG` or `GTG` (in bacteria), and end with `TAA`, `TAG` and `TGA`.    


```{perl}
#!/usr/bin/perl -w
use strict;
use warnings;

# collect input parameters

# parsing the files

# subroutines

## 1. calculate the coden usage, cal_coden_usage()
## 2. check the format of a seq, check_seq_format()




```


# 2. BacSeq pipeline  

A bacterial RNA-Seq analysis pipeline [URL](https://wikis.utexas.edu/display/bioiteam/BacSeq)

Getting Started

Require a [fourierseq account](https://wikis.utexas.edu/display/bioiteam/Getting+an+account+on+GSAF+server-fourierseq) or [lonestart account](https://portal.tacc.utexas.edu/account-request;jsessionid=CC4BF6A8DF96ADDFACA6F2FD8A4B2D86.jvm2)  

Bioinformatics Pipelines  

Bioinformatics pipelines, contributed by BioITeam community members can be found at :
/corral-repl/utexas/BioITeam/bin

Other helpful scripts  

Contributed by BioITeam community members are located in:
/corral-repl/utexas/BioITeam/scripts




