# Flag-Antp ChIP-seq analysis
Bioinformatics pipeline for the analysis of Flag-Antp ChIP-seq data generated from an endogenously FLAG-tagged Antennapedia (Antp) allele in *Drosophila melanogaster* embryos (GEO: _**GSE318263**_).

The dataset describes genome-wide Flag-Antp binding profiles in 3–7 h AEL embryos, obtained using chromatin immunoprecipitation followed by high-throughput sequencing. 
The data and associated analysis are reported in *Data in Brief*.

This repository contains the scripts used to reproduce the computational analysis.

**#Analysis workflow**

Raw FASTQ files
      │
      ▼
Quality control
(FastQC)
      │
      ▼
Read alignment
(Bowtie2 → dm6 genome)
      │
      ▼
BAM processing (SAMtools)
• sorting
• MAPQ ≥ 30 filtering
• duplicate removal
• blacklist filtering
      │
      ▼
Peak calling
(MACS2)
      │
      ▼
Replicate reproducibility
(IDR ≤ 0.05)
      │
      ▼
High-confidence Antp peaks
(1,188 regions)
      │
      ├──────────► Signal visualization
      │              (deepTools)
      │
      └──────────► Motif analysis
                     (FIMO / MEME suite)

**#Pipeline steps:**
1. Alignment to dm6 genome using Bowtie2
2. BAM processing using SAMtools
3. Peak calling using MACS2
4. Reproducible peak detection using IDR
5. Visualization using deepTools
6. Motif scanning using FIMO (MEME suite)

**#Software versions:**
Bowtie2 (2.2.5).
SAMtools (1.16.1).
MACS2 (2.1.1).
BEDTools (2.30.0).
deepTools (3.5.1).
MEME suite (FIMO). 
