---
layout: post
title: 使用BioPytho将GenBank转换为Fasta格式
description: 从GenBank文件中提取基因信息
category: Biopython
tags: [Biopython, GenBank, fasta]
---
Converting GenBank files to FASTA format with Biopython

# 1 Description  
This post will show how to extrat info from GenBank file using Biopython, and convert to FASTA file (nucleotide sequences and amino acid sequence).  

# 2 Purpose  
* 1. Extract entire DNA sequence as on record.    
* 2. Extract DNA sequences for each gene.  
* 3. Extract amino acid sequences for each gene.  

# 3 Materials
Here I choose a small bacteria complete genome to show the method: Buchnera_aphidicola (NCBI NC_011833, GI:219681389).
NCBI FTP [site ](ftp://ftp.ncbi.nlm.nih.gov/genomes/Bacteria/Buchnera_aphidicola_5A__Acyrthosiphon_pisum__uid59285)

* GenBank file: [NC_011833.gbk](ftp://ftp.ncbi.nlm.nih.gov/genomes/Bacteria/Buchnera_aphidicola_5A__Acyrthosiphon_pisum__uid59285/NC_011833.gbk) (2251 kb)     
* Genome FASTA File: [NC_011833.fna](ftp://ftp.ncbi.nlm.nih.gov/genomes/Bacteria/Buchnera_aphidicola_5A__Acyrthosiphon_pisum__uid59285/NC_011833.fna) (636 kb)   
* Gene FASTA File: [NC_011833.ffn](ftp://ftp.ncbi.nlm.nih.gov/genomes/Bacteria/Buchnera_aphidicola_5A__Acyrthosiphon_pisum__uid59285/NC_011833.ffn) (613 kb)   
* Amino Acid File: [NC_011833.faa](ftp://ftp.ncbi.nlm.nih.gov/genomes/Bacteria/Buchnera_aphidicola_5A__Acyrthosiphon_pisum__uid59285/NC_011833.faa) (246 kb)   

# 4 Methods & Results   
Using the module Bio.SeqIO to deal with genbank file.

* 1. Genome: GenBank -> FASTA   
Convert GenBank file to FASTA is simple：  

Option 1:  

``` python
>>> from Bio import SeqIO  
>>> SeqIO.convert("NC_011833.gbk", "genbank", "NC_011833_cov.fna", "fasta")
```

Option 2:  

``` python
import Bio.SeqIO
gbk_file = "NC_011833.gbk"
fna_file = "NC_011833_cov.fna"
input = open(gbk_file, "r")
output = open(fna_file, "w")

for seq in SeqIO.parse(input, "genbank") :
    print "Convert %s from GenBank to FASTA format" % seq.id
	output.write(">%s %s\n%s\n" % (
        seq.id, seq.description, seq.seq))

input.close()
output.close()
```

This strain contains only one chromosome, so we can get one sequence using above code.

* 2. Protein AA: GenBank -> FASTA
Extract the sequences of each coding sequences and convert to FASTA format.  
We can use **features** to extract different information in GenBank file.

``` python
import Bio.SeqIO

gbk_file = "NC_011833.gbk"
fna_file = "NC_011833_cov.fna"
faa_file = "NC_011833_cov.faa"
input = open(gbk_file, "r")
geneNC = open(fna_file, "w")
geneAA = open(faa_file, "w")

for seq in SeqIO.parse(input, "genbank") :
    print "Dealing with GenBank file of %s, \nOutput: \ngene nc: %s \ngene aa: %s" % (
        seq.id,
        fna_file,
        faa_file)
    for seq_feature in seq.features :
        geneSeq = seq_feature.extract(seq.seq)
        if seq_feature.type == "CDS" :
            assert len(seq_feature.qualifiers['translation']) == 1
            geneAA.write(">%s %s, %s\n%s\n" % (
                seq_feature.qualifiers['locus_tag'][0],
                seq.name,
                seq.description,
                seq_feature.qualifiers['translation'][0]))
            geneNC.write(">%s %s, %s\n%s\n" % (
                seq_feature.qualifiers['locus_tag'][0],
                seq.name,
                seq.description,
                geneSeq ))

input.close()
geneNC.close()
geneAA.close()
```

The result will contain FASTA record of each CDSin each GenBank record.  
Note:
Not all CDS features of GenBank file contain translation.  I think we can extract the sequences of each gene, and translate them to amino acid sequences.

* 3. Gene : GenBank -> FASTA
We will use seq.extract() to get sequence.

``` python
import Bio.SeqIO

gbk_file = "NC_011833.gbk"
fna_file = "NC_011833_cov.fna"
faa_file = "NC_011833_cov.faa"
input = open(gbk_file, "r")
geneNC = open(fna_file, "w")
geneAA = open(faa_file, "w")

for seq in SeqIO.parse(input, "genbank") :
    print "Dealing with GenBank file of %s, \nOutput: \ngene nc: %s \ngene aa: %s" % (
        seq.id,
        fna_file,
        faa_file)
    for seq_feature in seq.features :
        geneSeq = seq_feature.extract(seq.seq)
        if seq_feature.type == "CDS" :
            assert len(seq_feature.qualifiers['translation']) == 1
            geneNC.write(">%s %s, %s\n%s\n" % (
                seq_feature.qualifiers['locus_tag'][0],
                seq.name,
                seq.description,
                geneSeq ))

input.close()
geneNC.close()
geneAA.close()
```

* Expand    

Learn Biopython Bio.SeqIO usage: 

  * 1. Peter cock, http://www2.warwick.ac.uk/fac/sci/moac/people/students/peter_cock/python/genbank2fasta/   
  * 2. Biopython cookbook, http://biopython.org/DIST/docs/tutorial/Tutorial.html   
