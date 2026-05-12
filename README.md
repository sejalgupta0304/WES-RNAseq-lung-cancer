# Somatic Variant Analysis of Mouse Lung Tumor Using WES with Transcriptomic Integration

# Project Overview

This project presents an end-to-end **multi-omics bioinformatics pipeline** for identifying somatic mutations and functionally relevant genes involved in mouse lung tumorigenesis.

The study integrates:

- Whole Exome Sequencing (WES) for tumor-normal somatic variant detection  
- RNA-Seq transcriptomic analysis for differential gene expression  
- Functional enrichment analysis for biological interpretation

The main objective was to discover genes that are:

Somatically mutated  
Differentially expressed  
Associated with cancer-related pathways  

---

# Objectives

This study was designed to:

- Detect somatic mutations in mouse lung tumor samples using tumor-normal paired WES data  
- Identify significantly differentially expressed genes (DEGs) from RNA-seq samples  
- Integrate DNA-level mutations with RNA-level expression changes  
- Perform Gene Ontology (GO) and KEGG pathway enrichment analysis  
- Identify candidate driver genes involved in tumor progression  

---

# Biological Background

Lung cancer is one of the leading causes of cancer-related mortality worldwide. Tumor development occurs through accumulation of somatic mutations, dysregulated signaling pathways, and abnormal gene expression.

Whole Exome Sequencing helps identify coding mutations such as:

- SNPs  
- INDELs  
- Missense variants  
- Frameshift variants  

RNA-seq helps in:

- Quantifying gene expression changes  
- Detecting dysregulated pathways  
- Identifying biologically active mutated genes  

Integrating WES and RNA-seq provides a stronger systems-level understanding of tumor biology.

---

# Datasets Used

## Whole Exome Sequencing (Mouse Tumor-Normal)

- Tumor1 vs Normal1  
- Tumor2 vs Normal2  

Species: Mus musculus  
Reference Genome: mm10 / GRCm38

## RNA-seq Dataset

Dataset: **GSE51144**  
Source: NCBI GEO

Samples used:

- WT1 (Normal)
- WT2 (Normal)
- TUM1 (Tumor)
- TUM2 (Tumor)

---

# Workflow Overview

## WES Pipeline

FASTQ → BWA-MEM → SAMtools → Picard → GATK Mutect2 → FilterMutectCalls → Ensembl VEP → Mutated Genes

## RNA-seq Pipeline

FASTQ / Count Matrix → HISAT2 → featureCounts → DESeq2 → Volcano Plot → PCA → Heatmap

## Functional Analysis

Integrated Genes → gProfiler2 → GO Terms → KEGG Pathways

---

# Tools Used

| Step | Tool |
|------|------|
| WES Alignment | BWA-MEM |
| BAM Processing | SAMtools |
| Duplicate Marking | Picard |
| Somatic Variant Calling | GATK Mutect2 |
| Variant Annotation | Ensembl VEP |
| RNA-seq Alignment | HISAT2 |
| Read Counting | featureCounts |
| DEG Analysis | DESeq2 |
| Visualization | ggplot2 / pheatmap |
| Functional Analysis | gProfiler2 |

---

# WES Pipeline Commands

## Alignment

---bash
bwa mem -t 2 reference/mm10.fa tumor1_R1.fastq.gz tumor1_R2.fastq.gz | \
samtools view -Sb - | samtools sort -o tumor1.sorted.bam

## BAM Indexing
samtools index tumor1.sorted.bam

## Duplicate Marking
picard MarkDuplicates \
I=tumor1.sorted.bam \
O=tumor1.markdup.bam \
M=tumor1.metrics.txt

## Variant Calling
gatk Mutect2 \
-R reference/mm10.fa \
-I tumor1.markdup.bam \
-I normal1.markdup.bam \
-tumor tumor1 \
-normal normal1 \
-O tumor1.vcf

## Variant Filtering
gatk FilterMutectCalls \
-V tumor1.vcf \
-O tumor1.filtered.vcf

## Variant Annotation
perl ~/ensembl-vep/vep \
--cache \
--offline \
--species mus_musculus \
--assembly GRCm38 \
-i tumor1.filtered.vcf \
-o tumor1.annotated.vcf

# RNA-seq Pipeline Commands
## Alignment
hisat2 -p 2 -x mm10_index \
-1 WT1_R1.fastq.gz \
-2 WT1_R2.fastq.gz | \
samtools sort -o WT1.bam

## Counting
featureCounts -T 2 -p \
-a genes.gtf \
-o counts.txt \
WT1.bam WT2.bam TUM1.bam TUM2.bam

## DESeq2 Analysis
library(DESeq2)

dds <- DESeqDataSetFromMatrix(countData, colData, design=~condition)
dds <- DESeq(dds)
res <- results(dds)

Key Results
| Result                            | Value                |
| --------------------------------- | -------------------- |
| Differentially Expressed Genes    | 2668                 |
| Integrated Genes (Mutated + DEGs) | 387                  |
| Tumor vs Normal Separation        | Clear (PCA)          |
| Major Upregulated Genes           | Krt4, Krt13, S100a14 |
| Major Candidate Genes             | Uhrf1, Tbx4          |


# Figures Generated
- Volcano Plot
- PCA Plot
- Heatmap of Top Integrated Genes
- GO Enrichment Dotplot
- KEGG Pathway Plot

# Functional Enrichment Analysis
GO and KEGG analysis of integrated genes identified strong enrichment in pathways related to:
GO Biological Processes
- Regulation of epithelial cell proliferation
- Chemotaxis
- Cell migration
- Chromosome segregation

# KEGG Pathways
- cGMP-PKG signaling
- Cytoskeletal organization
- Motor proteins
- Immune-related pathways

# Biological Insights
The study suggests that tumor samples undergo:
- Strong transcriptional reprogramming
- Epithelial remodelin
- Signaling dysregulation
- Tumor microenvironment interactions
Genes such as Uhrf1, Krt13, Krt4, and S100a14 may play key roles in tumorigenesis.

# Final Output Files
- tumor1.filtered.vcf
- tumor2.filtered.vcf
- annotated_variants.vcf
- DEG_results.csv
- significant_genes.csv
- integrated_genes.csv
- volcano_plot.jpeg
- heatmap.jpeg
- PCA_plot.jpeggo_plot.jpeg
- kegg_plot.jpeg

# Future Scope
- Increase sample size
- Use raw RNA-seq reads for complete pipeline
- Add methylation / proteomics data
- Validate genes experimentally
- Compare with human lung cancer datasets

# Author
Sejal Gupta
Bioinformatics | Cancer Genomics | Integrative Analysis
