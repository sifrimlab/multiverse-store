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
| Dataset | RNA | ATAC | ADT | Metadata |
|---|:---:|:---:|:---:|---|
| [PBMC10K](datasets/pbmc10k/) | ✓ | ✓ | — | Cell type key: `cell_type` |
| [teaseq](datasets/teaseq/) | ✓ | ✓ | ✓ | Cell type key: `cell_type` |

Dataset metadata is stored in each dataset directory's `dataset.yaml`. The
current entries reference `data/processed.h5mu` for the RNA data path.
