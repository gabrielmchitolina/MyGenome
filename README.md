# 🧬 MyGenome  
Whole-Genome Assembly of *Pyricularia oryzae* Isolate Bm88511

This repository documents the complete genome assembly workflow performed for isolate **Bm88511** using Illumina paired-end sequencing data.

---

# 📚 Table of Contents

1. [Project Overview](#project-overview)  
2. [Raw Data Information](#raw-data-information)  
3. [Quality Control (FastQC)](#quality-control-fastqc)  
4. [Genome Assembly with Velvet](#genome-assembly-with-velvet)  
5. [Genome Assembly with SPAdes](#genome-assembly-with-spades)  
6. [Assembly Comparison](#assembly-comparison)  
7. [Genome Visualization with Bandage](#genome-visualization-with-bandage)  
8. [Software Versions](#software-versions)  
9. [Repository Structure](#repository-structure)  

---

# Project Overview

- **Researcher:** Gabriel Chitolina  
- **SRA Accession:** SRR37270005  
- **BioProject:** PRJNA926786  
- **BioSample:** SAMN55064733  
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

**Objective:** Generate and optimize a high-quality draft genome assembly.

---

# Raw Data Information

- Raw reads (single-end): **4,306,107**  
- Cleaned paired reads used for assembly: **3,638,894**  
- Total bases (cleaned R1 + R2): **1,087,853,572 bp**  
- Estimated genome size: **~40 Mb**

FASTQ files:

```
Bm88511_1.fq.gz
Bm88511_2.fq.gz
```

---

# Quality Control (FastQC)

## 1️⃣ Raw Data

### Summary of Warning (Orange) and Error (Red) Messages

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

### Summary of Warning (Orange) and Error (Red) Messages

After trimming:
- Adapter contamination removed
- Overall quality improved
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

# Genome Assembly with Velvet

Velvet assemblies were generated first using **VelvetOptimizer**, testing a range of k-mer values around the Velvet Advisor recommendation.

## Round 1 Optimization (Step = 10)

```
sbatch velvetoptimiser.sh Bm88511 lowK highK 10
```

Results:

- Genome Size: 41,616,069 bp  
- Contigs: 9,046  
- N50: 14,242 bp  

---

## Round 2 Optimization (Step = 2)

Further optimization was performed using a narrower k-mer range.

```
sbatch velvetoptimiser.sh Bm88511 newLowK newHighK 2
```

Results:

- Genome Size: 41,611,510 bp  
- Contigs: 9,025  
- N50: 14,187 bp  

---

# Genome Assembly with SPAdes

SPAdes was then used for comparison.

```
sbatch spades.sh Bm88511
```

SPAdes uses multiple k-mers (21, 33, 55, 77) and built-in error correction.

---

## Final SPAdes Assembly Metrics

| Metric | Value |
|--------|--------|
| Genome Size | 41,509,816 bp |
| Number of Contigs | 6,482 |
| N50 | 77,767 bp |

---

# Assembly Comparison

| Assembler | Genome Size | Contigs | N50 |
|-----------|-------------|---------|------|
| Velvet (Step 10) | 41,616,069 | 9,046 | 14,242 |
| Velvet (Step 2) | 41,611,510 | 9,025 | 14,187 |
| **SPAdes** | **41,509,816** | **6,482** | **77,767** |

SPAdes was selected as the final assembly because it:

- Produced significantly higher N50  
- Reduced number of contigs  
- Improved overall contiguity  

---

# Genome Visualization with Bandage

The final SPAdes assembly graph was visualized using **Bandage**.

## Whole Assembly Graph

![Whole Assembly Graph](/data/Bm88511_graph_spades.jpg)

---

## Single Contig + Scope = 4 (Node 119964)

![Node 119964 Scope 4](/data/Bm88511_graph_spades_node119964_4.jpg)

---

<details>
<summary>Interpretation</summary>

The whole graph shows a dominant interconnected cluster with limited complex tangles, indicating good assembly continuity.

The zoomed node (scope = 4) reveals local connectivity and confirms proper contig extension without excessive branching.

</details>

---

# Software Versions

```
velveth --version
velvetg --version
spades.py --version
fastqc --version
bandage --version
```

---

# Repository Structure

```
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
```
