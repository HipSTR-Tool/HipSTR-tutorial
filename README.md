# HipSTR-tutorial
Simple tutorial demonstrating how to run HipSTR with human WGS data

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
In this tutorial, we'll be  genotyping 599 STRs contained in **regions.bed**. These markers are part of the Marshfield panel and are some of the most polymorphic STRs in the human genome

We've also provided sorted and indexed BAM files in **bams/** that were prefiltered to save space. These files only contain reads and their mate pairs that overlap the STRs we're interested in. Let's take a look at the sample information in these BAMs:

    for file in bams/*.bam; do samtools view -H $file | grep @RG; done



