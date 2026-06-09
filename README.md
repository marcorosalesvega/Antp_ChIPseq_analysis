# Flag-Antp ChIP-seq analysis

Bioinformatics pipeline for the analysis of **Flag-Antp ChIP-seq data generated from an endogenously FLAG-tagged Antennapedia (Antp) allele in *Drosophila melanogaster* embryos**.

The dataset describes genome-wide Flag-Antp binding profiles in **3–7 h AEL embryos**, obtained using chromatin immunoprecipitation followed by high-throughput sequencing. The data and associated analysis are reported in *Data in Brief*.

Raw sequencing data are available at **GEO: GSE318263**.

This repository contains the scripts used to reproduce the computational analysis.

---

## Bioinformatics workflow

The computational workflow consisted of:

1. Quality control (FastQC)
2. Read alignment to dm6 (Bowtie2)
3. BAM processing and duplicate removal (SAMtools)
4. Peak calling (MACS2)
5. Reproducibility assessment (IDR)
6. Genomic annotation (BEDTools)
7. Motif analysis (MEME Suite / FIMO)
8. Data visualization (deepTools)

---

## Repository structure

```text
.
├── scripts/
├── annotation/
├── motif_analysis/
├── figures/
└── README.md
```

---

## 1. Quality control with FastQC

Raw paired-end FASTQ files were first inspected using FastQC.

```bash
mkdir -p fastqc_results

fastqc \
  sample_R1.fastq.gz \
  sample_R2.fastq.gz \
  -o fastqc_results
```

---

## 2. Read alignment with Bowtie2

Quality-filtered paired-end reads were aligned to the *Drosophila melanogaster* reference genome (dm6) using Bowtie2. Mapped reads with MAPQ ≥ 30 were retained and converted directly to BAM format using SAMtools.

```bash
bowtie2 \
  --very-sensitive \
  --mm \
  --no-mixed \
  --no-discordant \
  -x dm6_index \
  -1 sample_R1.fastq.gz \
  -2 sample_R2.fastq.gz \
| samtools view \
  -b \
  -F 0x4 \
  -q 30 \
  - \
| samtools sort \
  -n \
  -o sample.namesort.bam -
```

---

## 3. BAM processing and duplicate removal

Paired-end alignments were processed with SAMtools. Mate information was corrected, alignments were coordinate-sorted, and PCR duplicates were removed.

```bash
samtools fixmate \
  -m \
  sample.namesort.bam \
  sample.fixmate.bam

samtools sort \
  -o sample.coordsort.bam \
  sample.fixmate.bam

samtools markdup \
  -r \
  sample.coordsort.bam \
  sample.bam

samtools index sample.bam
```

---

## 4. Peak calling with MACS2

Antp binding sites were identified using MACS2 with paired-end mode (BAMPE) and the corresponding input control.

A permissive threshold (**q = 0.1**) was used during peak calling to maximize candidate peak detection prior to reproducibility assessment by IDR analysis.

```bash
macs2 callpeak \
  -t Antp-Flag_S2.bam \
  -c Input.bam \
  -f BAMPE \
  -g dm \
  -n Flag-Antp_S2 \
  -q 0.1 \
  --outdir CallPeak
```

---

## 5. Reproducibility assessment using IDR

Peaks identified independently from biological replicates were compared using the Irreproducible Discovery Rate (IDR) framework to define a high-confidence set of reproducible Antp binding sites.

```bash
idr \
  --samples rep1_peaks.narrowPeak rep2_peaks.narrowPeak \
  --input-file-type narrowPeak \
  --rank p.value \
  --output-file Antp_IDR.txt \
  --plot
```

---

## 6. Genomic annotation

Antp peaks were assigned to a single genomic category using a sequential annotation strategy implemented with BEDTools. Peaks were classified in the following order:

```text
promoter > exon > intron > intergenic
```

Peaks overlapping promoter regions were assigned as promoter peaks. Remaining peaks were sequentially assigned to exon, intron, or intergenic categories.

```bash
bedtools intersect -u \
  -a peaks.sorted.bed \
  -b promoter.sorted.bed \
  > peaks_promoter.bed

bedtools intersect -u \
  -a peaks_not_promoter.bed \
  -b exon.sorted.bed \
  > peaks_exon.bed

bedtools intersect -u \
  -a peaks_not_promoter_not_exon.bed \
  -b intron.sorted.bed \
  > peaks_intron.bed
```

Remaining peaks were classified as intergenic regions.

---

## 7. Motif analysis

Motif enrichment within Antp-bound regions was assessed using FIMO from the MEME Suite. Motif occurrences were searched in 200 bp sequences centered on peak summits using a significance threshold of p < 1 × 10⁻³

```bash
fimo \
  --oc fimo_peakcenter200 \
  --thresh 1e-3 \
  motifs.meme \
  Antp.peakcenter200.fa
```

FIMO was run using the default non-redundant background model, scanning both DNA strands. Peak-centered sequences (200 bp) were used as input for motif detection.

---

## 8. CPM-normalized bigWig generation

For visualization, BAM files were converted to CPM-normalized bigWig tracks using deepTools.

```bash
bamCoverage \
  --bam sample.bam \
  --outFileName sample_CPMnorm.bw \
  --outFileFormat bigwig \
  --binSize 25 \
  --normalizeUsing CPM \
  --minMappingQuality 30 \
  --smoothLength 75 \
  --skipNonCoveredRegions \
  --ignoreDuplicates
```

---

## Citation

If you use these scripts or data, please cite the associated publication and the GEO dataset:

**GSE318263** – Flag-Antp ChIP-seq in *Drosophila melanogaster* embryos.
