# AAM-reference
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22684383.svg)](https://doi.org/10.5281/zenodo.22684383)

AAM, Age-associated microglia, integration reference - in preparation

## Overview

This repository provides the integrated single-cell/single-nucleus reference of young and aged mouse microglia used in Moridono et al., "PAPER TITLE". Along with a script for projecting new datasets onto this reference via Seurat label transfer. 

The reference integrates: 
- 7 public scRNA-seq datasets (young 2-6 months, aged 18-29 months, C57BL/6 mice)
- 4 public snRNA-seq datasets (young 1-4 months, aged 18-24 months, C57BL/6 mice)

See Supplementary table of the paper for the full list of source datasets.

## Object structure

- `scRNA_integrated.rds` / `.h5ad`: integrated scRNA-seq object (15 clusters, 
  clustering resolution 0.8, CCA integration, Seurat v4.3.0)
- `snRNA_integrated.rds` / `.h5ad`: integrated snRNA-seq object (11 clusters, 
  prefixed `n0`–`n10`, CCA integration)

### Assays
- `RNA`: raw / log-normalized counts (`LogNormalize`)
- `integrated`: CCA-corrected values used for PCA, clustering, and UMAP

### Reductions
- `pca`: computed on the `integrated` assay (CCA)
- `umap`: computed from PCA

## meta.data columns

| Column | Description |
|---|---|
| `seurat_clusters` | Numeric cluster ID (scRNA: 0–14; snRNA: n0–n10) |
| `AAM_label` | Assigned cell state (see definitions below — TBD) |
| `dataset` / `author` | Source publication (see Supplementary Table 1) |
| `age` | young / aged |
| `sex` | mouse sex |
| `brain_region` | whole brain / hippocampus / subventricular zone, etc. |
| `dissociation_protocol` | enzyme-based / direct homogenization |
| `sorting` | with or without microglial sorting |
| `sequencing_chemistry` / `sequencing_platform` | 10x Chromium-based drop-seq / MARS-seq, etc. |
| `sample_id` | individual mouse/sample identifier |

## AAM label definitions

*LATER*

## Label transfer 

Anchors are identified using `FindTransferAnchors` (SCTransform normalization), and predictions are made with `TransferData`. Cells are annotated to a cluster if the prediction score exceeds the threshold; otherwise labeled "unpredicted".

- Mouse data: prediction score threshold > 0.7 (as used in the original study)
- Human data (cross-species via homologous genes, Ensembl ID mapping through biomaRt): prediction score threshold > 0.3 (as used in the original study)

**Note on thresholds**: These values were selected for the datasets analyzed in this study and are provided as defaults in the script. The optimal threshold may vary depending on the depth, quality, and biological similarity of a new dataset to this reference. Users are encouraged to inspect the distribution of prediction scores for their own data and adjust the threshold accordingly. We recommend reporting the threshold used when citing results derived from this reference.

### Input requirements

The query object must already be **subset to microglia** before running this script. 
For marker genes, see the Methods section of the associated manuscript for the approach used in this study (Tmem119, Aif1 positive; Hexb for excluding monocytes/macrophages) as a reference, but this step is left to the user's judgment for their own data.
