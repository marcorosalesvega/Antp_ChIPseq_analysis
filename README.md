# Antp_ChIPseq_analysis
Bioinformatics pipeline for the analysis of Flag-Antp ChIP-seq data in Drosophila melanogaster embryos

# Antp ChIP-seq analysis pipeline

This repository contains the scripts used for the analysis of Flag-Antp ChIP-seq data in *Drosophila melanogaster*.

Pipeline steps:

1. Alignment to dm6 genome using Bowtie2
2. BAM processing using SAMtools
3. Peak calling using MACS2
4. Reproducible peak detection using IDR
5. Visualization using deepTools
6. Motif scanning using FIMO (MEME suite)

Software versions:
Bowtie2 v2.2.5  
SAMtools v1.16.1  
MACS2 v2.1.1  
BEDTools v2.30.0  
deepTools v3.x  
MEME suite (FIMO)
