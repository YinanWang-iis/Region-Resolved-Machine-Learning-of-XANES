# Region-resolved Machine Learning analysis of Carbon K-edge XANES in doped graphene

This repository provides the public reproducibility workflow for the manuscript on
region-resolved machine-learning analysis of DFT-simulated C K-edge XANES spectra of
B- and N-doped graphene.

The main executable file is:

```text
Region-Resolved-Machine-Learning-of-XANES.ipynb
```

## Workflow Overview

The released repository already includes the processed datasets required to
reproduce the machine-learning analyses reported in the manuscript. Users
reproducing the published results can begin from Section 5 ("Interpolate to a
common energy grid") of the notebook. Sections 1–4 document the preprocessing
pipeline from the raw simulated spectra to the processed datasets and are only
required when regenerating the processed data.

## Installation

The workflow was written in Python and uses standard scientific Python packages.

A minimal environment can be created with:

```bash
conda create -n xanes-ml python=3.11
conda activate xanes-ml
pip install numpy pandas scipy scikit-learn matplotlib jupyter openpyxl
```

## Workflow Execution

Open the reproducibility notebook:

```bash
jupyter lab Region-Resolved-Machine-Learning-of-XANES.ipynb
```

To reproduce the machine-learning analyses reported in the manuscript, begin
execution from Section 5 ("Interpolate to a common energy grid").

The main workflow consists of the following sections:

| Section | Purpose |
|---|---|
| 5 | Interpolate spectra onto a common energy grid |
| 6 | Generate the data dictionary |
| 7 | Load `data_interpolated.csv` and construct named spectral features |
| 9 | Perform principal component analysis (PCA) |
| 10 | Define concentration-stratified and configuration-grouped validation protocols |
| 11 | Perform the random-forest analyses reported in the manuscript |

The notebook contains a Boolean flag controlling the main experiments:

```python
RUN_RF_EXPERIMENTS = True
```

Additional analyses, including baseline-model comparisons, UMAP/t-SNE
visualizations, and standard XANES descriptor comparisons, are documented in
[`additional_analysis/README.md`](additional_analysis/README.md).

## Dataset structure

### `data.csv`

`data.csv` is a per-spectrum wide table. Each row corresponds to one spectrum.

The spectral columns are stored as paired energy/intensity values:

```text
x1, y1, x2, y2, ..., xN, yN
```

These are followed by metadata and target columns.

### `data_interpolated.csv`

`data_interpolated.csv` is the main machine-learning input table.

- rows with numeric index values are photon energies in eV;
- each spectrum is one column;
- metadata rows are appended after the energy rows;
- the common grid uses 0.1 eV spacing;
- machine-learning features are the interpolated intensity values only.

## Data dictionary

The following fields are used throughout the machine-learning workflow:

| Field | Meaning |
|---|---|
| `spectrum_name` | Spectrum/sample identifier. |
| `file_name` or `file_name.1` | Original spectrum file name when available. |
| numeric energy rows | Interpolated XANES intensity at the corresponding photon energy. |
| `type` | Dopant-family label: 0 = pristine, 1 = B-doped graphene, 2 = N-doped graphene. |
| `rate` | Dopant concentration used for classification. |
| `B_rate` | Boron concentration label. |
| `N_rate` | Nitrogen concentration label. |
| `mbl` | Mean nearest-neighbor dopant–carbon bond length. |
| `bader` | Mean dopant Bader charge. |
| `configuration_id` | Parent atomic configuration ID used for grouped validation. |
| `dopant_family` | Pristine, B-doped graphene, or N-doped graphene. |
| `concentration` | Parsed dopant concentration used for stratified splitting. |

## Spectral windows

All feature construction uses named energy windows rather than hard-coded
positional column slicing.

| Window | Energy range |
|---|---:|
| `PI` | 265.0–273.5 eV |
| `SIGMA` | 273.5–283.5 eV |
| `POSTEDGE` | 283.5–290.0 eV |
| `FULL` | 265.0–290.0 eV |

Only interpolated intensity values inside these windows are used as spectral
features. The high-energy terminal region affected by the artificial intensity
decay associated with the finite number of unoccupied states in the XANES
calculations is excluded during feature construction.

## Splitting protocols

### 1. Spectrum-level concentration-stratified split

This is an 80/20 train/test split stratified by dopant family and concentration.
It is used to evaluate site-level predictive performance while preserving the
dopant/concentration distribution in the training and test subsets.

### 2. Configuration-grouped validation

This is a 5-fold grouped validation using atomic configuration ID as the grouping
variable. Spectra generated from the same atomic configuration are assigned to the
same fold. This prevents spectra from the same parent configuration from appearing
simultaneously in training and validation subsets.

## Machine-learning tasks

### Classification

B- and N-doping concentration classifications are performed independently.
Pristine graphene is included as the 0% concentration class.

Metrics:

- accuracy;
- F1 score;
- fold-to-fold standard deviation where applicable.

### Regression

Two local descriptors are predicted:

- mean nearest-neighbor dopant–carbon bond length (`mbl`);
- mean dopant Bader charge (`bader`).

Metrics:

- R²;
- RMSE;
- MAE.

## Dimensionality reduction (PCA)

PCA is applied to the spectral features to visualize the separation between
pristine, B-doped, and N-doped graphene spectra.

(UMAP and t-SNE projections are provided as supplementary comparisons; see
[`additional_analysis/README.md`](additional_analysis/README.md).)

## Feature-importance analysis

For random-forest models trained on the full spectral range, feature importance is
computed as a function of photon energy.

These results are used to identify which spectral regions are most informative for:

- dopant concentration classification;
- mean dopant–carbon bond-length regression;
- mean Bader-charge regression.

## Repository contents and workflow outputs

### Repository contents

The repository already includes the following processed files, which serve as
inputs to the machine-learning workflow (see [Dataset structure](#dataset-structure)
and [Data dictionary](#data-dictionary) above):

| File | Description |
|---|---|
| `data.csv` | Per-spectrum wide table with energy/intensity pairs and metadata. |
| `data_interpolated.csv` | Common-grid table used for ML and dimensionality reduction. |
| `data_dictionary.csv` | Data-column and metadata-row definitions. |

### Machine-learning analyses performed

- Classification of B- and N-doping concentration.
- Regression of mean dopant–carbon bond length (`mbl`) and mean dopant Bader
  charge (`bader`).
- Principal component analysis (PCA) of spectral features.
- Feature-importance analysis as a function of photon energy.

### Workflow Outputs

Running the notebook from Section 5 onward reproduces:

- classification accuracy, F1 score, and fold-to-fold standard deviation;
- regression R², RMSE, and MAE for `mbl` and `bader`;
- a PCA visualization separating pristine, B-doped, and N-doped graphene spectra;
- energy-resolved feature-importance profiles for each prediction task.

## Notes on reproducibility

- Random seeds are fixed in the notebook where applicable.
- Spectral features are selected by energy-window names, not by positional column
  indices.
- Grouped validation uses parsed `configuration_id` values so that spectra from the
  same configuration remain in the same fold.
- The released processed dataset is sufficient to reproduce the machine-learning
  analyses from Section 5 onward.
- Sections 1–4 are retained to document how the processed tables can be regenerated
  when raw spectra are available.

## Citation

If you use this dataset or workflow, please cite the associated arxiv manuscript.
https://doi.org/10.48550/arXiv.2603.29370
