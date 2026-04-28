![alt text](https://github.com/sriram-lab/TACTIC/blob/master/TACTIC_logo.png?raw=true)

# TACTIC
Transfer learning And Crowdsourcing to predict Therapeutic Interactions Cross-species.

[![license badge](https://img.shields.io/badge/License-GNU%20GPL-blue)](https://github.com/sriram-lab/TACTIC/blob/master/LICENSE)
[![DOI badge](https://badgen.net/badge/DOI/10.1101%2F2024.06.04.597386/blue)](https://doi.org/10.1101/2024.06.04.597386)

# Summary
This GitHub repository contains all code and data files used to develop the TACTIC approach (citation below).  
  
Chung, C. H., Chang, D. C., Rhoads, N. M., Shay, M. R., Srinivasan, K., Okezue, M. A., Brunaugh, A. D., and Chandrasekaran, S. (2024). Transfer learning predicts species-specific drug interactions in emerging pathogens. *bioRxiv* [preprint](https://doi.org/10.1101/2024.06.04.597386)

# Installation
```bash
pip install -r requirements.txt
jupyter notebook
```

# Repository structure
```
TACTIC
├───data/                       [data files]
│   ├───Endophthalmitis/        [endophthalmitis strain data and orthology files]
│   ├───GSEA/                   [gene set enrichment analysis gene lists]
│   ├───OrtholugeDB/            [gene orthology maps vs. E. coli MG1655 and M. tb H37Rv]
│   └───sample/                 [tutorial sample data for K. pneumoniae]
│   LICENSE
│   README.md
│   requirements.txt            [Python package dependencies]
│   TACTIC_analysis.ipynb       [core manuscript analyses: model training, holdout validation, prediction landscape]
│   TACTIC_logo.png
│   TACTIC_tutorial.ipynb       [walkthrough for extending TACTIC to new strains/drugs]
│   TACTIC_visualization.ipynb  [generates all publication figures from TACTIC_data.pkl]
```

# License
Released via GPL GNU License  
&copy; 2024 The Regents of the University of Michigan  
Chandrasekaran Research Group - https://systemsbiologylab.org/  
Contact: csriram@umich.edu  
