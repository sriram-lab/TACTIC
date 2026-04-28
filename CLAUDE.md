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
| `Saureus_validation.ipynb` | S. aureus prediction validation | moderate |
| `ClinicalTrial_scraper.ipynb` | NLP pipeline (CRF model) that extracts pathogen/treatment data from clinical trial XML files | moderate |

`TACTIC_data.pkl` (~1.5 GB) is the serialized output of `TACTIC_analysis.ipynb` and is the primary input to visualization and downstream notebooks. If it is absent or stale, re-run the analysis notebook first.

## Data Layout

```
data/
  omics_data.xlsx       # E. coli chemogenomics + M. tb transcriptomics (48 MB)
  ixn_data.xlsx         # Experimental drug interaction outcomes + drug key
  TACTIC_output.xlsx    # Full prediction landscape (19 MB)
  OrtholugeDB/          # Gene orthology maps per strain vs. E. coli MG1655 and M. tb H37Rv
  Endophthalmitis/      # SynergyFinder Bliss/Loewe scores for endophthalmitis strains
  GSEA/                 # Gene set enrichment results
  sample/               # Tutorial sample data (K. pneumoniae orthology)
TACTIC_data.pkl         # Serialized trained models + predictions (primary runtime artifact)
TACTIC_data2.pkl        # Alternate serialized dataset
LF_data.pkl             # LB-medium-specific dataset
```

Orthology files in `data/OrtholugeDB/` follow the naming convention `<strain>_orthologs.xlsx` and are required to extend predictions to new strains.

## Architecture

**Feature pipeline:**
1. Drug pairs are featurized via `indigopy.featurize()` using E. coli chemogenomic sparse vectors (324 stressors).
2. Orthology maps translate E. coli and M. tb gene features to the target strain's gene space.
3. The combined sparse feature matrix (E. coli + M. tb components) feeds a `sklearn` RandomForestRegressor.

**Key external dependency — `indigopy`:** This is a non-PyPI library (the INDIGO chemogenomics package). It must be installed separately; check the README or ask the lab for the source. Functions used: `indigopy.featurize()`, `indigopy.classify()`.

**Prediction output:** Continuous interaction scores (roughly −2 to +2); negative = antagonism, positive = synergy. Post-processing classifies into synergistic/antagonistic using a threshold.

**Transfer learning logic:** A single model is trained on all available strains; new strains are added by providing their orthology file — no retraining from scratch is needed for inference.

## Dependencies

No `requirements.txt` exists. Packages used across notebooks:

- `scikit-learn` ≥ 1.2.0 (RandomForestRegressor and other estimators)
- `pandas`, `numpy`, `scipy`
- `seaborn`, `matplotlib`
- `openpyxl` (Excel I/O)
- `statsmodels` (multiple testing correction via `multipletests`)
- `sklearn_crfsuite` (CRF model in `ClinicalTrial_scraper.ipynb`)
- `cv2` / OpenCV (image digitization in validation notebook)
- `tqdm`
- `indigopy` (non-PyPI, see above)
