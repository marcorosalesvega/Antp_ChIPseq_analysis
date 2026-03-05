# Flag-Antp ChIP-seq analysis
Bioinformatics pipeline for the analysis of Flag-Antp ChIP-seq data in *Drosophila melanogaster* embryos (GEO: GSE318263)

This repository contains the scripts used for the analysis of Flag-Antp ChIP-seq data reported in Data in Brief.

Pipeline steps:
1. Alignment to dm6 genome using Bowtie2
2. BAM processing using SAMtools
3. Peak calling using MACS2
4. Reproducible peak detection using IDR
5. Visualization using deepTools
6. Motif scanning using FIMO (MEME suite)

Software versions:
Bowtie2 (2.2.5).
SAMtools (1.16.1).
MACS2 (2.1.1).
BEDTools (2.30.0).
deepTools (3.5.1).
MEME suite (FIMO). 
