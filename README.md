# 🧬 MyGenome  
Whole-Genome Assembly of *Pyricularia oryzae* Isolate **Bm88511**

This repository documents the complete genome assembly workflow for isolate **Bm88511**, from NCBI metadata submission through quality control, trimming, assembly optimization, graph visualization, and final assembly selection.

---

# 📚 Table of Contents

1. [Project Overview](#project-overview)  
2. [NCBI Submissions](#ncbi-submissions)  
3. [Data Retrieval](#data-retrieval)  
4. [Raw Data Information](#raw-data-information)  
5. [Quality Control (FastQC)](#quality-control-fastqc)  
6. [Read Trimming (Trimmomatic)](#read-trimming-trimmomatic)  
7. [Genome Assembly with VelvetOptimizer](#genome-assembly-with-velvetoptimizer)  
8. [Genome Assembly with SPAdes (Selected)](#genome-assembly-with-spades-selected)  
9. [Assembly Comparison and Selection](#assembly-comparison-and-selection)  
10. [Genome Visualization with Bandage](#genome-visualization-with-bandage)  
11. [Final Assembly Files](#final-assembly-files)  
12. [BUSCO Assessment](#busco-assessment)
13. [Gene Prediction Analysis](#gene-prediction-analysis) 
14. [Software Versions](#software-versions)  
15. [Repository Structure](#repository-structure)   
---

# Project Overview

- **Researcher:** Gabriel Chitolina  
- **Organism:** *Pyricularia oryzae*  
- **Isolate:** Bm88511  
- **Host:** *Urochloa mutica*  
- **Collection Location:** Philippines: Nueva Ecija  
- **Collection Year:** 1988  
- **Sequencing Platform:** Illumina NovaSeq X  
- **Library Layout:** Paired-end  
- **Library Preparation:** Twist Library Preparation EF2.0  

**VM username:** gde267@gde267.cs.uky.edu  
**MCC username:** gde267@mcc.uky.edu  

**Objective:** Generate and optimize a high-quality genome assembly
---

# Data Retrieval

Raw sequencing data were retrieved from Dr. Mark Farman's personal computer and transferred to the local working environment using secure copy (SCP).

```bash
scp -r ngs@10.163.188.11:~/Desktop/Bm88511 ~/sequences
```

This command recursively copies the directory Bm88511 from the remote machine (10.163.188.11) to the local directory ~/sequences.

-r: Recursively transfers all files and subdirectories
Source: Dr. Mark Farman's personal computer (user: ngs)
Destination: Local working directory (~/sequences)

The transferred directory contained the raw paired-end FASTQ files used for downstream analysis.
---

# NCBI Submissions

## BioSample Submission

- **BioProject:** PRJNA926786  
- **BioSample:** SAMN55064733  

Metadata submitted using the MIGS Eukaryotic: plant-associated package.

## Sequence Read Archive (SRA)

- **SRA Accession:** SRR37270005  
- **Release Date:** 12/31/2026  

Raw paired-end FASTQ files:

Bm88511_1.fq.gz  
Bm88511_2.fq.gz  
---

# Raw Data Information

- Raw reads (single-end): **4,306,107**  
- Cleaned paired reads used for assembly: **3,638,894**  
- Total bases (cleaned R1 + R2): **1,087,853,572 bp**  
- Estimated genome size: **~40 Mb**

---

# Quality Control (FastQC)

Quality control was performed on the VM using FastQC.

## 1️⃣ Raw Data

## FastQC Commands

Raw read quality assessment:

```bash
fastqc Bm88511/Bm88511_1.fq.gz Bm88511/Bm88511_2.fq.gz -o ~/sequences
```

### Summary of Warnings and Errors

Raw reads showed:
- Adapter contamination  
- Elevated duplication levels  
- Minor per-base sequence quality warnings  

### Raw Data Images

#### Read 1

![Raw Summary R1](/data/raw_fastqc_summary_1.png)  
![Raw Adapter R1](/data/raw_fastqc_adapter_1.png)

#### Read 2

![Raw Summary R2](/data/raw_fastqc_summary_2.png)  
![Raw Adapter R2](/data/raw_fastqc_adapter_2.png)

---

## 2️⃣ Trimmed Data

Post-trimming quality assessment:

```bash
fastqc Bm88511_1_paired.fastq Bm88511_2_paired.fastq
```

### Summary After Trimming

- Adapter contamination removed  
- Overall sequence quality improved  
- Minor warnings remained but acceptable for assembly  

### Trimmed Paired Reads (Used for Assembly)

![Trimmed R1 Paired](/data/trimmed_fastqc_summary_1_paired.png)  
![Trimmed R2 Paired](/data/trimmed_fastqc_summary_2_paired.png)

### Trimmed Unpaired Reads

![Trimmed R1 Unpaired](/data/trimmed_fastqc_summary_1_unpaired.png)  
![Trimmed R2 Unpaired](/data/trimmed_fastqc_summary_2_unpaired.png)
---

# Read Trimming (Trimmomatic)

Reads were trimmed using Trimmomatic v0.38:

```bash
java -jar trimmomatic-0.38.jar PE -threads 2 -phred33 \
-trimlog Bm88511_errorlog.txt \
Bm88511_1.fq.gz Bm88511_2.fq.gz \
Bm88511_1_paired.fastq Bm88511_1_unpaired.fastq \
Bm88511_2_paired.fastq Bm88511_2_unpaired.fastq \
ILLUMINACLIP:adaptors.fa:2:30:10 \
SLIDINGWINDOW:20:20 MINLEN:125
```

Only paired reads were used for downstream assembly.

---

# Genome Assembly with VelvetOptimizer

Velvet assemblies were generated first using VelvetOptimizer to explore k-mer performance.

## Round 1 Optimization (Step = 10)

```bash
sbatch velvetoptimiser.sh Bm88511 39 79 10
```

Results:

- Genome Size: 41,616,069 bp  
- Contigs: 9,046  
- N50: 14,242 bp  

## Round 2 Optimization (Step = 2)

```bash
sbatch velvetoptimiser.sh Bm88511 69 89 2
```

Results:

- Genome Size: 41,611,510 bp  
- Contigs: 9,025  
- N50: 14,187 bp  

Velvet assemblies were fragmented and exhibited relatively low N50 values.

---

# Genome Assembly with SPAdes (Selected)

SPAdes was used for comparison using multi-k-mer assembly and built-in error correction.

```bash
sbatch spades.sh Bm88511
```

SPAdes uses multiple k-mers (21, 33, 55, 77).

## Final SPAdes Assembly Metrics

| Metric | Value |
|--------|--------|
| Genome Size | 41,509,816 bp |
| Number of Contigs | 6,482 |
| N50 | 77,767 bp |

---

# Assembly Comparison and Selection

| Assembler | Genome Size | Contigs | N50 |
|-----------|-------------|---------|------|
| Velvet (Step 10) | 41,616,069 | 9,046 | 14,242 |
| Velvet (Step 2)  | 41,611,510 | 9,025 | 14,187 |
| **SPAdes** | **41,509,816** | **6,482** | **77,767** |

### Final Selection

SPAdes was selected because it:

- Increased N50 by more than 5-fold  
- Reduced contig count substantially  
- Improved overall assembly contiguity  

---

# Genome Visualization with Bandage

The SPAdes assembly graph (`assembly_graph.fastg`) was visualized using Bandage.

## Whole Assembly Graph

![Whole Assembly Graph](/data/Bm88511_graph_spades.jpg)

## Single Contig + Scope = 4 (Node 119964)

![Node 119964 Scope 4](/data/Bm88511_graph_spades_node119964_4.jpg)

<details>
<summary>Graph Interpretation</summary>

The whole graph shows a  interconnected cluster.

The zoomed node (scope = 4) reveals local connectivity and confirms contig extension without excessive branching.

</details>

---

# Final Assembly Files

Bm88511_final.fasta  

---

# BUSCO Assessment

**Run details:**
- **BUSCO version:** 5.7.0  
- **Lineage dataset:** ascomycota_odb10 (n = 1706 BUSCOs)  
- **Mode:** euk_genome_min  
- **Gene predictor:** miniprot  

### Completeness Metrics
- **Complete (C):** 98.6%  
  - **Single-copy (S):** 98.4%  
  - **Duplicated (D):** 0.2%  
- **Fragmented (F):** 0.2%  
- **Missing (M):** 1.2%  
- **Total BUSCO groups (n):** 1706  
- **Error rate (E):** 3.5%  

### BUSCO Counts
- Complete BUSCOs: 1683  
  - Complete and single-copy: 1679  
  - Complete and duplicated: 4  
- Fragmented BUSCOs: 3  
- Missing BUSCOs: 20  
- BUSCOs with internal stop codons: 59  

### Assembly Statistics
- Number of scaffolds: 3,595  
- Number of contigs: 3,676  
- Total assembly length: 41,129,872 bp  
- Percent gaps: 0.016%  
- Scaffold N50: 75 KB  
- Contig N50: 69 KB  

### Interpretation
This assembly shows very high completeness (98.6%) with minimal duplication (0.2%) and very few missing genes (1.2%), indicating a high-quality genome assembly.

---

# Gene Prediction Analysis (SNAP vs AUGUSTUS)

## Methods

### SNAP

Training SNAP required generating a species-specific HMM using annotations from a related genome.

#### Start screen session
```bash
screen -S genes bash -l
```

#### Navigate to working directory
```bash
cd ~/genes/snap
```

#### Prepare training data
```bash
echo '##FASTA' | cat B71Ref2_a0.3.gff3 - B71Ref2.fasta > B71Ref2.gff3
```

#### Convert to SNAP ZFF format
```bash
maker2zff B71Ref2.gff3
```

#### Evaluate annotation statistics
```bash
fathom genome.ann genome.dna -gene-stats
```

#### Categorize gene models
```bash
fathom genome.ann genome.dna -categorize 1000
```

#### Extract high-quality gene set
```bash
fathom uni.ann uni.dna -gene-stats
```

#### Export sequences for training
```bash
fathom uni.ann uni.dna -export 1000 -plus
```

#### Train model
```bash
forge export.ann export.dna
```

#### Assemble HMM
```bash
hmm-assembler.pl Moryzae . > Moryzae.hmm
```

### Gene prediction with SNAP:

#### Run SNAP gene prediction
```bash
snap-hmm Moryzae.hmm Bm88511.fasta > Bm88511-snap.zff
```

#### Generate statistics
```bash
fathom Bm88511-snap.zff Bm88511.fasta -gene-stats
```

#### Convert to GFF2
```bash
snap-hmm Moryzae.hmm Bm88511.fasta -gff > Bm88511-snap.gff2
```
---

### AUGUSTUS
AUGUSTUS was run using a pre-trained model for a closely related species.

#### Navigate to working directory
```bash
cd ~/genes/augustus
```

#### Run AUGUSTUS gene prediction
```bash
augustus --species=magnaporthe_grisea --gff3=on \
--singlestrand=true --progress=true \
Bm88511_final.fasta > Bm88511-augustus.gff3
```

## Results
### Predicted Gene Counts
Tool	Number of Predicted Genes
SNAP	TBD
AUGUSTUS	TBD

### IGV Visualization
#### SNAP-only Gene Prediction

#### AUGUSTUS-only Gene Prediction

#### Same Exon/Intron Structure (SNAP vs AUGUSTUS)

#### Different Exon/Intron Structure (SNAP vs AUGUSTUS)

#### Notes
SNAP predictions are based on a trained HMM specific to *M. oryzae*.
AUGUSTUS uses a generalized HMM with species-specific parameters (magnaporthe_grisea).
SNAP outputs simpler exon-based annotations, while AUGUSTUS provides more detailed gene structures including CDS and protein sequences.

---

# Software Versions

```bash
velveth --version
velvetg --version
spades.py --version
fastqc --version
bandage --version
```
---
# ✅ Workflow Status

✔ BioSample submitted  
✔ SRA submitted  
✔ QC completed  
✔ Trimming completed  
✔ Velvet optimization completed  
✔ SPAdes selected  
✔ Graph visualization completed  
✔ Final assembly generated  
