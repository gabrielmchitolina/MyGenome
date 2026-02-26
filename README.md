# 🧬 MyGenome  
De novo genome assembly and quality assessment workflow.

This repository documents the step-by-step process used to assemble and evaluate a genome from raw sequencing reads.

---

# 📚 Table of Contents

1. [Project Overview](#project-overview)  
2. [Data Organization](#data-organization)  
3. [Assess Sequence Quality](#assess-sequence-quality)  
4. [Trim Reads](#trim-reads)  
5. [Genome Assembly](#genome-assembly)  
6. [Assembly Evaluation](#assembly-evaluation)  
7. [Software Versions](#software-versions)

---

# Project Overview

- **Organism:** Insert organism name  
- **Sequencing Platform:** Illumina paired-end  
- **Assembly Tool:** SPAdes  
- **Objective:** Generate a draft genome assembly  

---

# Data Organization

The repository contains the following directories:

- `data/` – raw sequencing reads  
- `results/` – output files from analyses  
- `scripts/` – commands or helper scripts  

---

# Assess Sequence Quality

## Step 1: Run FastQC

```
singularity run --app fastqc /path/to/container.sif \
fastqc sample_R1.fastq.gz sample_R2.fastq.gz
```

**Purpose:**  
Evaluate raw read quality before trimming.

---

<details>
<summary>Click to expand: FastQC Output Explanation</summary>

FastQC reports include:

- Per base sequence quality  
- GC content  
- Adapter contamination  
- Overrepresented sequences  

These metrics help determine trimming parameters.

</details>

---

# Trim Reads

## Step 2: Remove Adapters and Low-Quality Bases

```
trimmomatic PE sample_R1.fastq.gz sample_R2.fastq.gz \
trimmed_R1.fastq.gz unpaired_R1.fastq.gz \
trimmed_R2.fastq.gz unpaired_R2.fastq.gz \
ILLUMINACLIP:adapters.fa:2:30:10 SLIDINGWINDOW:4:20 MINLEN:50
```

**Purpose:**  
Remove sequencing adapters and low-quality regions.

---

# Genome Assembly

## Step 3: Run SPAdes

```
spades.py \
-1 trimmed_R1.fastq.gz \
-2 trimmed_R2.fastq.gz \
-o spades_output
```

**Purpose:**  
Assemble trimmed paired-end reads into contigs.

---

<details>
<summary>Click to expand: Assembly Parameters</summary>

- Default k-mers used  
- Paired-end mode enabled  
- No hybrid reads included  

</details>

---

# Assembly Evaluation

## Step 4: Evaluate Assembly with QUAST

```
quast.py spades_output/contigs.fasta -o quast_results
```

### Assembly Metrics

| Metric | Value |
|--------|--------|
| Total Length | Insert value |
| N50 | Insert value |
| Number of Contigs | Insert value |
| GC Content | Insert value |

---

# Software Versions

```
spades.py --version
fastqc --version
quast.py --version
singularity --version
```

---
