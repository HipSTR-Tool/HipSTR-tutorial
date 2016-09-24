# HipSTR-tutorial
Simple tutorial demonstrating how to run HipSTR with human WGS data

## Introduction
In this tutorial, we'll be genotyping 599 STRs that are part of the Marshfield panel of markers. These markers are some of the most polymorphic STRs in the human genome. We'll be using whole-genome sequencing data for NA12878, NA12891 and NA12892, a [trio](https://catalog.coriell.org/0/Sections/Collections/NIGMS/CEPHFamiliesDetail.aspx?PgId=441&fam=1463&) of individuals that is widely analyzed in genomics.

## Getting setup

First, download this repository and all of its files from github

    git clone https://github.com/HipSTR-Tool/HipSTR-tutorial.git
    cd HipSTR-tutorial

Next, let's download HipSTR from github and build it: 

    git clone --recursive https://github.com/tfwillems/HipSTR.git
    cd HipSTR
    make
    cd ../
    
The last source of input we'll need are FASTA files for the human genome.
Since our BAM alignments are relative to the hg19 reference genome, we'll
download this version of the human reference:

    mkdir fasta
    cd fasta
    wget http://hgdownload.cse.ucsc.edu/goldenPath/hg19/bigZips/chromFa.tar.gz
    tar -xzvf chromFa.tar.gz
    cd ../

## Input files
In the tutorial directory, we've provided a **regions.bed** file that contains the required information about each STR. Each line in the BED file contains the chromosomal positions of an STR, its period, the number of repeat copies and it's name:

    head -n 5 regions.bed
    
```
chr1	6464771	6464818	4	12	MFD424-TTTA003
chr1	7142501	7142552	4	13	GATA23G09
chr1	10987588	10987617	3	10	AAT238
chr1	13784267	13784306	4	10	GATA27E01
chr1	16540224	16540267	4	11	TTTA063
```

We've also provided sorted and indexed BAM files in **bams/** that were prefiltered to save space. These files only contain reads and their mate pairs that overlap the STRs we're interested in. 

Let's take a look at the sample information in these BAMs:

    for file in bams/*.bam; do samtools view -H $file | grep @RG; done

```
@RG	ID:ERR194147	LB:ERR194147	PL:Illumina	SM:NA12878	PU:ERR194147
@RG	ID:ERR194160	LB:ERR194160	PL:Illumina	SM:NA12891	PU:ERR194160
@RG	ID:ERR194161	LB:ERR194161	PL:Illumina	SM:NA12892	PU:ERR194161
@RG	ID:SRR826427	LB:SRR826427	PL:Illumina	SM:NA12891	PU:SRR826427
@RG	ID:SRR826428	LB:SRR826428	PL:Illumina	SM:NA12892	PU:SRR826428
@RG	ID:SRR826448	LB:SRR826448	PL:Illumina	SM:NA12891	PU:SRR826448
@RG	ID:SRR826463	LB:SRR826463	PL:Illumina	SM:NA12878	PU:SRR826463
@RG	ID:SRR826465	LB:SRR826465	PL:Illumina	SM:NA12891	PU:SRR826465
@RG	ID:SRR826467	LB:SRR826467	PL:Illumina	SM:NA12878	PU:SRR826467
@RG	ID:SRR826469	LB:SRR826469	PL:Illumina	SM:NA12878	PU:SRR826469
@RG	ID:SRR826471	LB:SRR826471	PL:Illumina	SM:NA12892	PU:SRR826471
@RG	ID:SRR826473	LB:SRR826473	PL:Illumina	SM:NA12892	PU:SRR826473
```
Each of the 12 BAM files has a single read group, corresponding to 1 of 3 samples: NA12878, NA12891 or NA12892. HipSTR uses the **SM** tags to determine what sample each read comes from and uses the **LB** tag to determine what sequencing library each read comes from.

## Running HipSTR
Now that we have the required input files, let's run HipSTR. We can analyze all of the BAMs jointly to genotype the 599 STRs as follows:

    HipSTR/HipSTR --str-vcf trio.marshfield.no_snps.vcf.gz --log trio.marshfield.no_snps.log --bams bams/ERR194147.bam,bams/ERR194160.bam,bams/ERR194161.bam,bams/SRR826427.bam,bams/SRR826428.bam,bams/SRR826448.bam,bams/SRR826463.bam,bams/SRR826465.bam,bams/SRR826467.bam,bams/SRR826469.bam,bams/SRR826471.bam,bams/SRR826473.bam --regions regions.bed --fasta fasta/ --min-reads 25 --def-stutter-model --use-all-reads --viz-out trio.marshfield.no_snps.html.gz --read-qual-trim \#


