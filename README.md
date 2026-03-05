# Flag-Antp ChIP-seq analysis

Bioinformatics pipeline for the analysis of **Flag-Antp ChIP-seq data generated from an endogenously FLAG-tagged Antennapedia (Antp) allele in *Drosophila melanogaster* embryos**.

The dataset describes genome-wide Flag-Antp binding profiles in **3–7 h AEL embryos**, obtained using chromatin immunoprecipitation followed by high-throughput sequencing. The data and associated analysis are reported in *Data in Brief*.

Raw sequencing data are available at **GEO: GSE318263**.

This repository contains the scripts used to reproduce the computational analysis.

---

## Analysis workflow

```mermaid
flowchart TD
  A[Raw FASTQ files] --> B[Quality control<br/>(FastQC)]
  B --> C[Read alignment<br/>(Bowtie2 → dm6)]
  C --> D[BAM processing<br/>(SAMtools)<br/>• sorting<br/>• MAPQ ≥ 30 filtering<br/>• duplicate removal<br/>• blacklist filtering]
  D --> E[Peak calling<br/>(MACS2)]
  E --> F[Replicate reproducibility<br/>(IDR ≤ 0.05)]
  F --> G[High-confidence Antp peaks<br/>(1,188 regions)]
  G --> H[Signal visualization<br/>(deepTools)]
  G --> I[Motif analysis<br/>(FIMO / MEME suite)]
