# Multiverse Store

Multiverse Store provides containerized model definitions and dataset
metadata for multimodal single-cell omics workflows.

## Models

The model catalog currently contains the following implementations. A check
mark indicates that the model supports the corresponding modality; `any`
means the model is configured for arbitrary omics inputs.

| Model | RNA | ATAC | ADT | Other modalities | GPU |
|---|:---:|:---:|:---:|---|:---:|
| [Cobolt](models/cobolt/) | ✓ | ✓ | — | — | Required |
| [MOFA](models/mofa/) | — | — | — | Any | No |
| [MOFA-Cpu](models/mofa-cpu/) | — | — | — | Any | No |
| [Mowgli](models/mowgli/) | — | — | — | Any | No |
| [MultiVI](models/multivi/) | ✓ | ✓ | — | — | Required |
| [PCA](models/pca/) | — | — | — | Any | No |
| [TotalVI](models/totalvi/) | ✓ | — | ✓ | — | Required |

Each model directory includes:

- `model.yaml`, with the model name, version, supported omics, runtime image,
  and execution requirements.
- `hyperparameters.schema.json`, defining the accepted hyperparameters.
- Container build files and the model runner.

## Datasets

The dataset catalog currently contains the following multimodal datasets.

| Dataset | Technology | Tissue | RNA | ATAC | ADT | Original source |
|---|---|---|:---:|:---:|:---:|---|
| [PBMC10K](datasets/pbmc10k/) | 10x Chromium Single Cell Multiome ATAC + Gene Expression | Human PBMCs | ✓ | ✓ | — | [10x Genomics](https://www.10xgenomics.com/datasets/pbmc-from-a-healthy-donor-granulocytes-removed-through-cell-sorting-10-k-1-standard-1-0-0) |
| [teaseq](datasets/teaseq/) | TEA-seq (transcripts, epitopes, accessibility) | Human PBMCs | ✓ | ✓ | ✓ | [Swanson et al., eLife 2021](https://doi.org/10.7554/eLife.63632) |

### PBMC10K

Paired gene expression and chromatin accessibility profiles from about 10,000
peripheral blood mononuclear cells (PBMCs).

- **Sample:** Cryopreserved PBMCs from a healthy 25-year-old female donor,
  obtained by 10x Genomics from AllCells. Granulocytes were removed by cell
  sorting before nuclei isolation.
- **Assay:** 10x Genomics Chromium Single Cell Multiome ATAC + Gene
  Expression, sequenced on an Illumina NovaSeq 6000.
- **Modalities:** RNA (gene expression counts) and ATAC (peak accessibility).
- **Annotations:** Cell type labels in `obs["cell_type"]`.
- **Source:** 10x Genomics public dataset "PBMC from a Healthy Donor -
  Granulocytes Removed Through Cell Sorting (10k)". Multiverse uses the
  [Cell Ranger ARC 1.0.0](https://www.10xgenomics.com/datasets/pbmc-from-a-healthy-donor-granulocytes-removed-through-cell-sorting-10-k-1-standard-1-0-0)
  release; a [Cell Ranger ARC 2.0.0](https://www.10xgenomics.com/datasets/pbmc-from-a-healthy-donor-granulocytes-removed-through-cell-sorting-10-k-1-standard-2-0-0)
  reprocessing also exists.
- **License:** Creative Commons Attribution (CC BY 4.0 on the ARC 2.0.0
  listing).
- **Paper:** None. This is a 10x Genomics demonstration dataset with no
  associated publication; cite the 10x Genomics dataset page.
- **Preprocessed input:** The Multiverse pipeline starts from the
  preprocessed `10x-Multiome-Pbmc10k-RNA.h5ad` and
  `10x-Multiome-Pbmc10k-ATAC.h5ad` files. They were prepared for the GLUE benchmark
  ([Cao & Gao, Nature Biotechnology 2022](https://doi.org/10.1038/s41587-022-01284-4);
  [preprocessing scripts](https://github.com/gao-lab/GLUE/tree/master/data))
  from the Cell Ranger ARC 1.0.0 output. That processing removes low-quality
  cells and DoubletFinder doublets and assigns cell types by Seurat
  weighted-nearest-neighbour (WNN) clustering. The same files are distributed
  by SnapATAC2 (`snapatac2.datasets.pbmc10k_multiome`).
  - `RNA.h5ad`: 9,631 cells × 29,095 genes.
  - `ATAC.h5ad`: 9,631 cells × 107,194 peaks.
  - 19 cell types: CD4 Naive, CD4 TCM, CD4 TEM, CD8 Naive, CD8 TEM_1,
    CD8 TEM_2, CD14 Mono, CD16 Mono, HSPC, Intermediate B, MAIT, Memory B,
    NK, Naive B, Plasma, Treg, cDC, gdT, pDC.

### teaseq

Trimodal single-cell profiles of PBMCs measuring transcripts, cell-surface
protein epitopes and chromatin accessibility in the same cells.

- **Sample:** Permeabilized human PBMCs from a leukapheresis (leukopak)
  sample, GEO sample `GSM4949911` (`X061-AP0C1W1`, full sequencing depth).
- **Assay:** TEA-seq, built on the 10x Genomics Chromium Single Cell
  Multiome ATAC + Gene Expression platform with oligo-tagged antibodies for
  surface protein measurement.
- **Modalities:** RNA and ATAC (from the Cell Ranger ARC filtered feature
  matrix) and ADT (antibody-derived tag counts).
- **Original files:**
  - `GSM4949911_X061-AP0C1W1_leukopak_perm-cells_tea_fulldepth_cellranger-arc_filtered_feature_bc_matrix.h5`
    (RNA and ATAC)
  - `GSM4949911_tea_fulldepth_adt_counts.csv.gz` (ADT)
- **Annotations:** None. The original GEO release has no cell type labels.
- **Data accession:** [GEO GSE158013](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE158013).
- **Paper:** Swanson E, Lord C, Reading J, et al. *Simultaneous trimodal
  single-cell measurement of transcripts, epitopes, and chromatin
  accessibility using TEA-seq.* eLife 10:e63632 (2021).
  [doi:10.7554/eLife.63632](https://doi.org/10.7554/eLife.63632) ·
  [PMC8034981](https://pmc.ncbi.nlm.nih.gov/articles/PMC8034981/) ·
  [bioRxiv preprint](https://doi.org/10.1101/2020.09.04.283887)

### Dataset metadata

Each dataset directory contains a `dataset.yaml` with:

- `name`: the dataset identifier.
- `omics`: the modalities present in the dataset.
- `raw_files`: paths to the input data. Both datasets currently point to
  `data/processed.h5mu`, a MuData file that holds all modalities.
- `metadata_keys.cell_type_key`: the `obs` column holding cell type labels
  (`cell_type` for both datasets; for teaseq this column does not exist yet).

### Citing the datasets

If you use these datasets, please cite the original sources:

```bibtex
@article {10.7554/eLife.63632,
article_type = {journal},
title = {Simultaneous trimodal single-cell measurement of transcripts, epitopes, and chromatin accessibility using TEA-seq},
author = {Swanson, Elliott and Lord, Cara and Reading, Julian and Heubeck, Alexander T and Genge, Palak C and Thomson, Zachary and Weiss, Morgan DA and Li, Xiao-jun and Savage, Adam K and Green, Richard R and Torgerson, Troy R and Bumol, Thomas F and Graybuck, Lucas T and Skene, Peter J},
editor = {Tyler, Jessica K and Chang, Howard Y and Adey, Andrew C},
volume = 10,
year = 2021,
month = {apr},
pub_date = {2021-04-09},
pages = {e63632},
citation = {eLife 2021;10:e63632},
doi = {10.7554/eLife.63632},
url = {https://doi.org/10.7554/eLife.63632},
abstract = {Single-cell measurements of cellular characteristics have been instrumental in understanding the heterogeneous pathways that drive differentiation, cellular responses to signals, and human disease. Recent advances have allowed paired capture of protein abundance and transcriptomic state, but a lack of epigenetic information in these assays has left a missing link to gene regulation. Using the heterogeneous mixture of cells in human peripheral blood as a test case, we developed a novel scATAC-seq workflow that increases signal-to-noise and allows paired measurement of cell surface markers and chromatin accessibility: integrated cellular indexing of chromatin landscape and epitopes, called ICICLE-seq. We extended this approach using a droplet-based multiomics platform to develop a trimodal assay that simultaneously measures transcriptomics (scRNA-seq), epitopes, and chromatin accessibility (scATAC-seq) from thousands of single cells, which we term TEA-seq. Together, these multimodal single-cell assays provide a novel toolkit to identify type-specific gene regulation and expression grounded in phenotypically defined cell types.},
keywords = {transcription, chromatin, epitopes, sequencing, genomics, multiomics},
journal = {eLife},
issn = {2050-084X},
publisher = {eLife Sciences Publications, Ltd},
}


@misc{10x2020pbmc10kmultiome,
  title        = {{PBMC} from a Healthy Donor - Granulocytes Removed Through
                  Cell Sorting (10k), Single Cell Multiome {ATAC} + Gene
                  Expression Dataset by Cell Ranger {ARC} 1.0.0},
  author       = {{10x Genomics}},
  year         = {2020},
  howpublished = {\url{https://www.10xgenomics.com/datasets/pbmc-from-a-healthy-donor-granulocytes-removed-through-cell-sorting-10-k-1-standard-1-0-0}}
}
```