# 🧬 MyGenome  
Whole-Genome Assembly of *Pyricularia oryzae* Isolate Bm88511

This repository documents the complete genome assembly workflow performed for isolate **Bm88511** using Illumina paired-end sequencing data.

---

# 📚 Table of Contents

1. [Project Overview](#project-overview)  
2. [Sample Information](#sample-information)  
3. [Raw Data Information](#raw-data-information)  
4. [FastQC Analysis](#fastqc-analysis)  
5. [Genome Assembly](#genome-assembly)  
6. [Assembly Results](#assembly-results)  
7. [Software Versions](#software-versions)  

---

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
- **Library Preparation:** Twist Library Preparation EF2.0 with Enzymatic Fragmentation  

**Objective:** Generate a high-quality draft genome assembly from Illumina paired-end reads.

---

---

# Sample Information

- Submission Type: Single BioSample  
- Sample Type: MIGS Eukaryotic: plant-associated  
- Environmental Medium: plant leaf  
- Projected Release Date: 12/31/2026  

---

---

# Raw Data Information

- Raw reads (single-end): **4,306,107**  
- Cleaned paired reads used for assembly: **3,638,894**  
- Total bases (cleaned R1 + R2): **1,087,853,572 bp**  
- Recommended k-mer: **79**

FASTQ files:

```
Bm88511_1.fq.gz
Bm88511_2.fq.gz
```

---

---

# FastQC Analysis

## 1️⃣ Raw Data Quality Assessment

### Summary of Warning (Orange) and Error (Red) Messages

**Warnings (Orange):**
- Per sequence GC content  
- Sequence duplication levels  

**Errors (Red):**
- Adapter content  

---

### Raw Data FastQC Images

#### 🔹 Read 1 (R1)

**Summary Tab**

![Raw Summary R1](/data/raw_fastqc_summary_1.png)

**Adapter Content Tab**

![Raw Adapter R1](/data/raw_fastqc_adapter_1.png)

---

#### 🔹 Read 2 (R2)

**Summary Tab**

![Raw Summary R2](/data/raw_fastqc_summary_2.png)

**Adapter Content Tab**

![Raw Adapter R2](/data/raw_fastqc_adapter_2.png)

---

## 2️⃣ Trimmed Data Quality Assessment

### Summary of Warning (Orange) and Error (Red) Messages After Trimming

**Warnings (Orange):**
- Sequence duplication levels  

**Errors (Red):**
- None detected  

---

### Trimmed Data FastQC Images

#### 🔹 Paired Reads (Used for Assembly)

**Read 1 Paired**

![Trimmed Summary R1 Paired](/data/trimmed_fastqc_summary_1_paired.png)

**Read 2 Paired**

![Trimmed Summary R2 Paired](/data/trimmed_fastqc_summary_2_paired.png)

---

#### 🔹 Unpaired Reads

**Read 1 Unpaired**

![Trimmed Summary R1 Unpaired](/data/trimmed_fastqc_summary_1_unpaired.png)

**Read 2 Unpaired**

![Trimmed Summary R2 Unpaired](/data/trimmed_fastqc_summary_2_unpaired.png)

---

<details>
<summary>Click to expand: Interpretation of FastQC Results</summary>

Raw reads showed adapter contamination and elevated duplication levels, which justified trimming prior to assembly.

After trimming, adapter contamination was successfully removed and overall quality improved. Remaining warnings were minor and did not prevent assembly.

</details>

---

---

# Genome Assembly

## Assembly with SPAdes

```
spades.py \
-1 clean_R1.fq.gz \
-2 clean_R2.fq.gz \
-k 79 \
-o spades_output
```

Paired-end Illumina reads were assembled using the recommended k-mer size of 79.

---

---

# Assembly Results

## Final SPAdes Assembly

| Metric | Value |
|--------|--------|
| Genome Size | 41,509,816 bp |
| Number of Contigs | 6,482 |
| N50 | 77,767 bp |

---

## Comparison (Step = 10)

| Metric | Value |
|--------|--------|
| Genome Size | 41,616,069 bp |
| Number of Contigs | 9,046 |
| N50 | 14,242 bp |

---

## Comparison (Step = 2)

| Metric | Value |
|--------|--------|
| Genome Size | 41,611,510 bp |
| Number of Contigs | 9,025 |
| N50 | 14,187 bp |

---

---

# Software Versions

```
spades.py --version
fastqc --version
singularity --version
```

---

---

# Repository Structure

```
MyGenome/
│
├── data/
├── results/
├── scripts/
└── README.md
```

---
