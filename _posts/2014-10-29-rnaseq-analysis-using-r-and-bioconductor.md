---
layout: post
title: "使用R和Bioconductor进行RNASeq差异表达分析"
categories:
- R
tags:
- R
- RNASeq
- Bioconductor

comments: true
---

RNA-Seq analysis using R and Bioconductor
========================================================

# Count-based differential expression analysis of RNA sequencing data using R and Bioconductor

Published online 22 August 2013; doi:10.1038/nprot.2013.099
nature protocols | VOL.8 NO.9 | 2013

See the original material: [URL](http://www.nature.com/nprot/journal/v8/n9/full/nprot.2013.099.html)

# 1 Summary
The samples cited here can be found @ [URL](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3032923/)    
*Brooks, A.N. et al. Conservation of an RNA regulatory map between Drosophila and mammals. Genome Res. 21, 193–202 (2011).*


Library           Library Layout    shortname     Fastaq                
--------------    ---------------   -----------   ------------------
Untreated-3       PAIRED            CT.PA.1       SRR031714, *15        
Untreated-4       PAIRED            CT.PA.2       SRR031716, *17        
CG8144_RNAi-3     PAIRED            KD.PA.3       SRR031724, *25        
CG8144_RNAi-4     PAIRED            KD.PA.4       SRR031726, *27        
Untreated-1       SINGLE            CT.SI.5       SRR031708, *09-13     
CG8144_RNAi-1     SINGLE            KD.SI.6       SRR031718, *19-23     
Untreated-6       SINGLE            CT.SI.7       SRR031728, *29 
---------------   ---------------   -----------   ------------------

# 2 Method

*1 Go to work directory   

```R
setwd("/home/wangming/work/R_bioconductor/")
```

*2 Loading sample information

```R
readRDS("sri.txt")
```

**Start procedure**

*3 Reads quality control  

```R
#library("ShortRead")
#fqQC <- qa(dirPath="reads", pattern=".fastq$", type="fastq")
#report(fqQC, type="html", dest="fastqQAreport")
```

*4 Create sample table

```R
sri$LibraryName <- gsub("S2_DRSC_", "", sri$LibraryName) # trim the library name
samples <- unique(sri[, c("LibraryName", "LibraryLayout")])
for(i in 1:nrow(samples)) {
  rw <- (sri$LibraryName == samples$LibraryName[i])
  if(samples$LibraryLayout[i] == "PAIRED") {
    samples$fastq1[i]=paste0(paste("reads", sri$Run[rw], sep="/"), "_1.fastq", collapse=",")
    samples$fastq2[i]=paste0(paste("reads", sri$Run[rw], sep="/"), "_2.fastq", collapse=",")    
  } else {
    samples$fastq1[i]=paste0(paste("reads", sri$Run[rw], sep="/"), ".fastq", collapse=",")
    samples$fastq2[i]=""
  }
}
```

*5 Add description columns

```R
samples$condition <- "CTL"
samples$condition[grep("RNAi", samples$LibraryName)] <- "KD" # Knock down
samples$shortname <- paste(substr(samples$condition, 1, 2), 
                           substr(samples$LibraryLayout, 1, 2),
                           seq_len(nrow(samples)), sep=".")

```

*6 Inspect the table carefully  
*print out the "samples"*  

```R
head(samples, 5)
```

*7 Using R to create shell commands

```R
gtf <- "reference/Drosophila_melanogaster.BDGP5.70.gtf"
bowtieIndex <- "reference/Dme1_BDGP5_70"
bowtieCMD <- with(samples, paste("tophat2 -G", gtf, "-p 5 -o", 
                                 LibraryName, bowtieIndex, fastq1, fastq2))
bowtieCMD
```

*8 Run the command in Unix terminal

```R
write.table(bowtieCMD, "RunBowtie2.R", sep="\n", row.names=FALSE, col.names=FALSE)
```

*9 Organize the BAM files and create SAM files

```R
smCMD <- ""
for(i in 1:nrow(samples)) {
  lib <- samples$LibraryName[i]
  ob <- file.path(lib, "accepted_hits.bam")
  # sort bam files by name, convert to SAM for htseq-count
  sm1 <- paste0("samtools sort -n ", ob, " ", lib, "_sn")
  sm2 <- paste0("samtools view -o ", lib, "_sn.sam ", lib, "_sn.bam")
  # sort by position and index for IGV
  sm3 <- paste0("samtools sort ", ob, " ", lib, "_s")
  sm4 <- paste0("samtools index ", lib, "_s.bam")
  smCMD[i] <- paste(sm1, sm2, sm3, sm4, sep="\n")
  write.table(smCMD[i], paste0("RunSm-", samples$LibraryName[i], ".sh"), 
              row.names=FALSE, col.names=FALSE)
}
#write.table(smCMD, "RunSamtools.sh", sep="\n\n", row.names=FALSE, col.names=FALSE)

```
Run the above commands in UNIX terminal.


*10 Inspect the Alignment data using IGV
*D. melanogaster (dm3) genome, BAM files and GTF file.*

*11 Check the expressed transcript (Figure 3)

*12 Select the positive and negative controls for the system from other study.

*13 Count reads using htseq-count

```R
samples$countf <- paste(samples$LibraryName, "count", sep=".")
gtf <- "reference/Drosophila_melanogaster.BDGP5.70.gtf"
htseqCMD <- paste0("htseq-count -s no -a 10 ", samples$LibraryName, 
                   "_sn.sam ", gtf, " > ", samples$countf)
write.table(htseqCMD, "HTSeq-count.sh", row.names=FALSE, col.names=FALSE)
htseqCMD
```
Run the above commands in UNIX terminal.


*14 Differential expression analysis using edgeR.
A. edgeR - simple
B. edgeR - complex
C. DESeq - simple
D. DESeq - complex

*A. edgeR - simple

```R
##1 Start edgeR
library("edgeR")
counts <- readDGE(samples$countf)$counts
##2 Filter weakly expressed and noninformative features
noint <- rownames(counts) %in% c("no_feature", "ambiguous", "too_low_aQual",
                                 "alignment_not_unique")
cpms <- cpm(counts)
keep <- rowSums(cpms > 1) >= 3 & ! noint
counts <- counts[keep, ]
##2 Inspect the count table
colnames(counts) <- samples$shortname
head(counts[, order(samples$condition)], 5)
##4 Create a DGEList object.
d <- DGEList(counts = counts, group = samples$condition)
##5 Estimate normalization factors
d <- calcNormFactors(d)
##6 Inspect the relationships using multidimentional scaling (MDS) plot
plotMDS(d, labels = samples$shortname,
        col = c("darkgreen","blue")[factor(samples$condition)])
##7 Estimate tagwise dispersion (simple design)
d <- estimateCommonDisp(d)
d <- estimateTagwiseDisp(d)
##8 Create a visual representation of the mean-variance relationship using the 
##  plotMeanVar (Fig. 5a) and plotBCV (Fig. 5b) functions, as follows:
plotMeanVar(d, show.tagwise.vars = TRUE, NBline = TRUE)
plotBCV(d)
##9 Test for differential expression (‘classic’ edgeR),
de <- exactTest(d, pair = c("CTL","KD"))
##10 See Step 14B(##6-##9)
##//---Step 14B(##6-##9)---##
##6 Use the topTags function to present a tabular summary of the differential 
##  expression statistics.
tt <- topTags(de, n=nrow(d))
head(tt$table)
##7 Inspect the depth-adjusted reads per million for some of the top 
##  differentially expressed genes
nc <- cpm(d, normalized.lib.sizes=TRUE)
rn <- rownames(tt$table)
head(nc[rn, order(samples$condition)], 5)
##8 Create a graphical summary, such as an M (log-fold) VS A (log-average) plot,
##  here showing the genes selected as differentially expressed (FDR=0.05)
deg <- rn[tt$table$FDR < 0.05]
plotSmear(d, de.tags=deg)
##9 Save the result table as CSV file
write.csv(tt$table, file="toptags_edgeR.csv")
##//------##
```

*B. edgeR - complex  

```R
##1 Follow Step 14A(##1-##6)
##2 Create a design matrix (see ‘Experimental design’ for further details) to 
## specify the factors that are expected to affect expression levels:
design = model.matrix( ~ LibraryLayout + condition, samples)
design
##3 Estimate dispersion values, using the Cox-Reid (CR)-adjusted likelihood
d2 <- estimateGLMTrendedDisp(d, design)
d3 <- estimateGLMTagwiseDisp(d2, design)
##4 Given the design matrix and dispersion esitmates, fit a GLM to each feature
f <- glmFit(d2, design)
##5 Perform a likelihood ratio test, specifying the difference of interest
##  (Knockdown VS control, the 3rd column of the matrix)
de <- glmLRT(f, coef=3)
##6 Use the topTags function to present a tabular summary of the differential 
##  expression statistics.
tt <- topTags(de, n=nrow(d))
head(tt$table)
##7 Inspect the depth-adjusted reads per million for some of the top 
##  differentially expressed genes
nc <- cpm(d, normalized.lib.sizes=TRUE)
rn <- rownames(tt$table)
head(nc[rn, order(samples$condition)], 5)
##8 Create a graphical summary, such as an M (log-fold) VS A (log-average) plot,
##  here showing the genes selected as differentially expressed (FDR=0.05)
deg <- rn[tt$table$FDR < 0.05]
plotSmear(d, de.tags=deg)
##9 Save the result table as CSV file
write.csv(tt$table, file="toptags_edgeR.csv")
```


*C. DESeq - simple design  

```R
##1 Create a data.frame with required data: 
samplesDESeq <- with(samples, 
                     data.frame(shortname=I(shortname), countf=I(countf),
                                condition=condition,
                                LibraryLayout=LibraryLayout))
##2 Load the DESeq package and create a CountDataSet object (DESeq’s container 
##  for RNA-seq data) from the count tables and corresponding metadata:
library("DESeq")
cds <- newCountDataSetFromHTSeqCount(samplesDESeq)
##3 Estimate normalization factors
cds <- estimateSizeFactors(cds)
##4 Inspect the size factors
sizeFactors(cds)
##5 Inspect sample relationships, invoke a variance-stabilizing trasformation
##  and inspect a principal component analysis (PCA) plot
cdsB <- estimateDispersions(cds, method="blind")
vsd <- varianceStabilizingTransformation(cdsB)
p <- plotPCA(vsd, intgroup=c("condition", "LibraryLayout"))
##6 Use estimateDispersions to calculate dispersion values
cds <- estimateDispersions(cds)
##7 Inspect the estimated dispersions using the plotDispEsts function
plotDispEsts(cds)
##8 Perform the test for differential expression by using nbinomTest
res <- nbinomTest(cds,"CTL","KD")
##9 Given the table of differential expression results, use plotMA to display
##  differential expression (log-fold) VS expression strength (log-average)
plotMA(res)
##10 Inspect the result tables of significantly upregulated and downregulated 
##   genes: FDR=0.1
resSig <- res[which(res$padj < 0.1), ]
head(resSig[order(resSig$log2FoldChange, decreasing=TRUE), ])
head(resSig[order(resSig$log2FoldChange, decreasing=FALSE), ])
##11 Count the number of genes with significant differential expression at 
##   FDR=0.1
table(res$padj < 0.1)
##12 Create a persistent storage of results using a CSV file
write.csv(res, file="res_DESeq.csv")
##13 Perform a sanity check by inspecting a histogram of unadjusted P values
##   for the differential expression results
hist(res$pval, breaks=100)
```

*D. DESeq - complex design  

```R
##1 Follow Step 14C(##1-##5)
##2 Calculate the CR-adjusted profile likelihood dispersion estimates relative 
##  to the factors specified, developed by McCarthy et al., according to:
cds <- estimateDispersions(cds, method = "pooled-CR",
                              modelFormula = count ~ LibraryLayout + condition)
##3 Test for differential expression in the GLM setting by fitting both a full 
##  model and reduced model (i.e., with the factor of interest taken out):
fit1 <- fitNbinomGLMs(cds, count ~ LibraryLayout + condition)
fit0 <- fitNbinomGLMs(cds, count ~ LibraryLayout)
##4 By using the two fitted models, compute likelihood ratio statistics and 
##  associated P values, as follows:
pval <- nbinomGLMTest(fit1, fit0)
##5 Adjust the reported P values for multiple testing:
padj <- p.adjust(pval, method = "BH")
##6 Assemble a result table from full model fit and the raw and adjusted P 
##  values and print the first few upregulated and downregulated genes (FDR < 10%):
res <- cbind(fit1, pval=pval, padj=padj)
resSig <- res[which(res$padj < 0.1),]
head(resSig[order(resSig$conditionKD, decreasing=TRUE), ])
head(resSig[order(resSig$conditionKD, decreasing=FALSE), ])
##7 Follow Step 14C(##11-##13).
##//------##
##11 Count the number of genes with significant differential expression at 
##   FDR=0.1
table(res$padj < 0.1)
##12 Create a persistent storage of results using a CSV file
write.csv(res, file="res_DESeq.csv")
##13 Perform a sanity check by inspecting a histogram of unadjusted P values
##   for the differential expression results
hist(res$pval, breaks=100)
##//------##
```

*15. As another spot check, point the IGV genome browser (with GTF and BAM 
files loaded) to a handful of the top differentially expressed genes and 
confirm that the counting and differential expression statistics are 
appropriately represented.





