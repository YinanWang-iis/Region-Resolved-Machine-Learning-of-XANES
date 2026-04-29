# Decoding Dopant-Induced Electronic Modulation in Graphene via Region-Resolved Machine Learning of XANES

Code for the paper:

> Yinan Wang, Arpita Varadwaj, Teruyasu Mizoguchi, Masato Kotsugi. *Decoding Dopant-Induced Electronic Modulation in Graphene via Region Resolved Machine Learning of XANES*. [Journal, Year. DOI: to be added]

## Overview

This repository provides the analysis code (`code_for_paper.ipynb`) reproducing the machine learning results in the paper. We apply PCA, Random Forest classification, and Random Forest regression to DFT-simulated carbon K-edge XANES spectra of boron- and nitrogen-doped graphene.

All models are additionally augmented with two scalar features: the **energy positions of the π\* and σ\* absorption peaks**.

## Requirements

```
python >= 3.8
numpy, pandas, matplotlib, scikit-learn, openpyxl
```

```bash
pip install numpy pandas matplotlib scikit-learn openpyxl
```

## Data

The input file `data.csv` should be placed in the working directory. It is available from the corresponding author upon reasonable request.

## Citation

If you use this code, please cite the paper above.
Yinan Wang, Varadwaj Arpita*, Teruyasu Mizoguchi, Masato Kotsugi,"Decoding Dopant-Induced Electronic Modulation in Graphene via Region-Resolved Machine Learning of XANES",arXiv:2603.29370
https://doi.org/10.48550/arXiv.2603.29370


