# Additional analyses

This document describes supplementary analyses that complement the main
manuscript workflow documented in the top-level
[`README.md`](../README.md). All analyses below are implemented in the same
notebook, `Region-Resolved-Machine-Learning-of-XANES.ipynb`, alongside the
main workflow (Sections 1–11).

## UMAP / t-SNE visualizations (Section 9 / 9b)

Section 9 builds the same intensity-feature matrix used for the manuscript's
PCA analysis (raw intensities in the 265–290 eV full window, no labels or
metadata included). Section 9b reuses that matrix to additionally fit UMAP and
t-SNE projections, alongside PCA, and reproduces the corresponding scatter
plots (colored by dopant family and concentration) and PCA PC1
loading-vs-mean-spectrum overlays for each dopant group.

The manuscript's main result uses only the PCA projection (see the top-level
README); the UMAP and t-SNE projections here are provided as supplementary
comparisons on the same feature matrix.

- **Output**: `dimensionality_reduction_265_290/dimensionality_reduction_coordinates_265_290.csv`

## Baseline-model comparisons (Section 12)

This section evaluates the same named energy-window features and the same
two split protocols used in the manuscript (spectrum-level stratified 80/20;
configuration-level grouped 5-fold cross-validation), but with additional
model families alongside the Random Forest used in the main workflow:

- Random Forest (RF)
- XGBoost
- Lasso (L1-regularized linear/logistic regression)
- Multi-layer perceptron (MLP)

For the Lasso classification baseline, a fixed-`C` L1 logistic regression
(`C = 1.0`) is used instead of `LogisticRegressionCV`, to avoid unstable
multi-class coefficient-shape behavior in small grouped folds while
preserving the intended sparse linear baseline.

Execution is controlled by a Boolean flag in the notebook:

```python
RUN_BASELINE_EXPERIMENTS = True
```

- **Output**: regression and classification summary tables (stratified and
  grouped) for each model family, alongside feature importances where
  applicable.

## Standard XANES descriptor comparison (Section 14)

This section compares conventional, hand-crafted XANES descriptors against
the region-resolved intensity features used in the main manuscript workflow,
using the same models and split protocols as Sections 11/12.

### Descriptor groups

| Key | Name | Features |
|---|---|---|
| `A_peak` | Peak-only | π\*/σ\*/post-edge peak energy & height (6) |
| `B_area` | Area-only | Region areas, area ratios, height ratios (9) |
| `C_edge` | Edge/centroid | Centroid energies, edge derivative, half-max energy, post-edge slope/mean (9) |
| `D_all` | All standard | Union of A + B + C |
| `E_pi_int` | π\* intensity | Raw spectral intensities in the π\* window (265–273.5 eV) |
| `E_full_int` | Full intensity | Raw spectral intensities in the full window (265–290 eV) |

### Models

RF, XGBoost, Lasso, and MLP, evaluated with the same stratified 80/20 and
grouped 5-fold cross-validation protocols as Sections 11/12.

### Outputs (written to `xanes_descriptor_baseline/`)

| File | Description |
|---|---|
| `xanes_descriptors.csv` | Extracted descriptor values for all spectra. |
| `sample_splits.csv` | Train/test/fold assignments. |
| `descriptor_regression_summary_stratified.csv` / `_grouped.csv` | Regression results per descriptor group and split protocol. |
| `descriptor_classification_summary_stratified.csv` / `_grouped.csv` | Classification results per descriptor group and split protocol. |
| `descriptor_comparison_tables.xlsx` | Pivot tables (models × feature sets). |
| `descriptor_vs_intensity_regression_bar.png` | Bar-chart comparison, regression. |
| `descriptor_vs_intensity_classification_bar.png` | Bar-chart comparison, classification. |
| `conclusions.txt` | Auto-generated comparison summary. |

Execution is controlled by a Boolean flag in the notebook, left `False` by
default because this section runs approximately 72 model fits and takes
several minutes:

```python
RUN_XANES_DESCRIPTOR_BASELINE = True
```

## Notes

- All three analyses reuse the named energy-window definitions and the
  splitting protocols established in the main workflow; no additional
  positional column slicing is introduced.
- These sections are independent of one another and can be run selectively
  via their respective Boolean flags.
