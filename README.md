# 🧬 MyGenome  
Whole-Genome Assembly of *Pyricularia oryzae* Isolate **Bm88511**

This repository documents the complete genome assembly workflow for isolate **Bm88511**, from NCBI metadata submission through quality control, trimming, assembly optimization, graph visualization, and final assembly selection.

---

# 📚 Table of Contents

1. [Project Overview](#project-overview)  
2. [NCBI Submissions](#ncbi-submissions)  
3. [Raw Data Information](#raw-data-information)  
4. [Quality Control (FastQC)](#quality-control-fastqc)  
5. [Read Trimming (Trimmomatic)](#read-trimming-trimmomatic)  
6. [Genome Assembly with VelvetOptimizer](#genome-assembly-with-velvetoptimizer)  
7. [Genome Assembly with SPAdes (Selected)](#genome-assembly-with-spades-selected)  
8. [Assembly Comparison and Selection](#assembly-comparison-and-selection)  
9. [Genome Visualization with Bandage](#genome-visualization-with-bandage)  
10. [Final Assembly Files](#final-assembly-files)  
11. [Software Versions](#software-versions)  
12. [Repository Structure](#repository-structure)  

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

**Objective:** Generate and optimize a high-quality draft genome assembly and evaluate assembly performance across algorithms.

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

### Summary of Warnings and Errors

Raw reads showed:
- Adapter contamination  
- Elevated duplication levels  
- Minor per-base sequence quality warnings  

---

### Raw Data Images

#### Read 1

![Raw Summary R1](/data/raw_fastqc_summary_1.png)  
![Raw Adapter R1](/data/raw_fastqc_adapter_1.png)

#### Read 2

![Raw Summary R2](/data/raw_fastqc_summary_2.png)  
![Raw Adapter R2](/data/raw_fastqc_adapter_2.png)

---

## 2️⃣ Trimmed Data

### Summary After Trimming

- Adapter contamination removed  
- Overall sequence quality improved  
- Minor warnings remained but acceptable for assembly  

---

### Trimmed Paired Reads (Used for Assembly)

![Trimmed R1 Paired](/data/trimmed_fastqc_summary_1_paired.png)  
![Trimmed R2 Paired](/data/trimmed_fastqc_summary_2_paired.png)

---

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

Only paired reads were used for downstream assembly.

Genome Assembly with VelvetOptimizer

Velvet assemblies were generated first using VelvetOptimizer to explore k-mer performance.

Round 1 Optimization (Step = 10)
sbatch velvetoptimiser.sh Bm88511 39 79 10

Results:

Genome Size: 41,616,069 bp

Contigs: 9,046

N50: 14,242 bp

Round 2 Optimization (Step = 2)
sbatch velvetoptimiser.sh Bm88511 69 89 2

Results:

Genome Size: 41,611,510 bp

Contigs: 9,025

N50: 14,187 bp

Velvet assemblies were fragmented and exhibited relatively low N50 values.

Genome Assembly with SPAdes (Selected)

SPAdes was used for comparison using multi-k-mer assembly and built-in error correction.

sbatch spades.sh Bm88511

SPAdes uses multiple k-mers (21, 33, 55, 77).

Final SPAdes Assembly Metrics
Metric	Value
Genome Size	41,509,816 bp
Number of Contigs	6,482
N50	77,767 bp
Assembly Comparison and Selection
Assembler	Genome Size	Contigs	N50
Velvet (Step 10)	41,616,069	9,046	14,242
Velvet (Step 2)	41,611,510	9,025	14,187
SPAdes	41,509,816	6,482	77,767
Final Selection

SPAdes was selected because it:

Increased N50 by more than 5-fold

Reduced contig count substantially

Improved overall assembly contiguity

Genome Visualization with Bandage

The SPAdes assembly graph (assembly_graph.fastg) was visualized using Bandage.

Whole Assembly Graph

Single Contig + Scope = 4 (Node 119964)

<details> <summary>Graph Interpretation</summary>

The whole graph shows a dominant interconnected cluster with limited complex tangles, indicating good assembly continuity.

The zoomed node (scope = 4) reveals local connectivity and confirms proper contig extension without excessive branching.

</details>
Final Assembly Files
Bm88511_final.fasta
Bm88511_contig_map.txt
Software Versions
velveth --version
velvetg --version
spades.py --version
fastqc --version
bandage --version
Repository Structure
MyGenome/
│
├── data/
│   ├── FastQC images
│   ├── Bandage screenshots
│
├── results/
│   ├── Velvet assemblies
│   ├── SPAdes assembly
│
├── scripts/
│   ├── velvetoptimiser.sh
│   ├── spades.sh
│
└── README.md
