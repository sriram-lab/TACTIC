# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**TACTIC** (Transfer learning And Crowdsourcing to predict Therapeutic Interactions Cross-species) is a machine learning pipeline that predicts bacterial drug-drug interaction outcomes across species using transfer learning. It maps E. coli chemogenomic profiles and M. tuberculosis transcriptomic profiles onto novel pathogen strains via orthology, then trains a RandomForestRegressor to predict synergy/antagonism scores.

Associated publication: Chung et al. (2024), *bioRxiv* https://doi.org/10.1101/2024.06.04.597386

## Running Notebooks

There is no build system or test runner. All work is done by executing Jupyter notebooks directly:

```bash
jupyter notebook
```

Key notebooks and their roles:

| Notebook | Purpose | Approx. Runtime |
|---|---|---|
| `TACTIC_analysis.ipynb` | Core manuscript analysis — trains models, runs holdout validation, produces prediction results | ~6–8 hours |
| `TACTIC_visualization.ipynb` | Generates all publication figures from `TACTIC_data.pkl` | minutes–hours |
| `TACTIC_tutorial.ipynb` | Walkthrough for extending TACTIC to new strains/drugs (K. pneumoniae example) | moderate |

`TACTIC_data.pkl` (~1.5 GB) is the serialized output of `TACTIC_analysis.ipynb` and is the primary input to visualization and downstream notebooks. If it is absent or stale, re-run the analysis notebook first.

## Data Layout

```
data/
  omics_data.xlsx         # E. coli chemogenomics + M. tb transcriptomics (48 MB)
  ixn_data.xlsx           # Experimental drug interaction outcomes + drug key
  LF_standard.xlsx        # LB-medium-specific experimental data
  biolog_pm01.xlsx        # Biolog PM01 carbon source data
  Van_2023_fig2a_hm.jpg   # Heatmap image from Van et al. 2023 (used for image digitization)
  OrtholugeDB/            # Gene orthology maps per strain vs. E. coli MG1655 and M. tb H37Rv
  Endophthalmitis/        # Endophthalmitis strain data (experimental data + orthology files)
  GSEA/                   # Gene set enrichment gene lists
  sample/                 # Tutorial sample data (K. pneumoniae orthology + screenshots)
TACTIC_data.pkl           # Serialized trained models + predictions (~1.5 GB; primary runtime artifact)
requirements.txt          # Python package dependencies
```

Orthology files in `data/OrtholugeDB/` follow the naming convention `<strain>_orthologs.txt` and are required to extend predictions to new strains.

## Architecture

**Feature pipeline:**
1. Drug pairs are featurized via `indigopy.featurize()` using E. coli chemogenomic sparse vectors (324 stressors).
2. Orthology maps translate E. coli and M. tb gene features to the target strain's gene space.
3. The combined sparse feature matrix (E. coli + M. tb components) feeds a `sklearn` RandomForestRegressor.

**Key external dependency — `indigopy`:** The INDIGO chemogenomics package. Install with `pip install indigopy-sriram-lab`. Functions used: `indigopy.featurize()`, `indigopy.classify()`.

**Prediction output:** Continuous interaction scores (roughly −2 to +2); negative = antagonism, positive = synergy. Post-processing classifies into synergistic/antagonistic using a threshold.

**Transfer learning logic:** A single model is trained on all available strains; new strains are added by providing their orthology file — no retraining from scratch is needed for inference.

## Dependencies

All dependencies are listed in `requirements.txt` and can be installed with:

```bash
pip install -r requirements.txt
```

Key packages:

- `scikit-learn` ≥ 1.2.0 (RandomForestRegressor and other estimators)
- `pandas`, `numpy`, `scipy`
- `seaborn`, `matplotlib`, `plotly`, `matplotlib-venn`, `statannot`
- `openpyxl` (Excel I/O)
- `statsmodels` (multiple testing correction via `multipletests`)
- `opencv-python` (image digitization in `TACTIC_analysis.ipynb` section 7)
- `tqdm`
- `indigopy` — install via `pip install indigopy-sriram-lab`
