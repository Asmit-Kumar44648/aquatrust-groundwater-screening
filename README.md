# AquaTrust: Groundwater Hardness-Exceedance Screening

**A station-aware machine-learning study using groundwater monitoring data from Bihar, India.**

## Project overview

AquaTrust investigates whether basic groundwater measurements and geographic location can predict whether a groundwater observation exceeds a selected total-hardness threshold, and whether additional chemical measurements improve predictive performance.

The study compares two Random Forest classifiers using a common complete-case dataset and a station-disjoint training/evaluation split.

**Research objective:** Quantify the additional predictive value of bicarbonate, chloride and sodium beyond pH, electrical conductivity and location.

## Research question

Can adding bicarbonate (HCO₃), chloride (Cl) and sodium (Na) improve classification of groundwater hardness exceedance compared with using pH, electrical conductivity (EC), latitude and longitude alone?

## Dataset

The source is the National Water Data Portal groundwater chemical-parameter dataset associated with the Central Ground Water Board.

- [Dataset download](https://nwdp.nwic.gov.in/dataset/7bb1e7c7-bcc1-48bb-8bcd-32c19473804a/resource/1b8e3f68-4d04-49f3-8121-52bbe25f6323/download/gwq_chemical_parameter_manual_cgwb_br_1961_2025.csv)
- Original file: 3,112 observations and 46 columns.
- Cleaned dataset: 2,998 observations, 752 stations and 37 districts.
- Complete-case modelling population: 2,105 observations from 735 stations.

The modelling population was restricted to observations with the required measurements available for both feature sets and the target.

The cleaned dataset was created using location filtering, exact-duplicate removal and repeated-chemistry filtering. These steps can remove genuine repeated measurements; the resulting population should not be assumed to represent every groundwater observation in Bihar.

## Prediction target

The binary target is:

- Positive (1): measured hardness > 200 mg/L as CaCO₃.
- Negative (0): measured hardness ≤ 200 mg/L as CaCO₃.

This is a hardness-threshold classification task, not a general assessment of drinking-water safety.

## Feature sets

**Set A — Baseline**

- pH
- Electrical conductivity (EC)
- Latitude
- Longitude

**Set B — Extended chemistry**

- All Set A features
- Bicarbonate (HCO₃)
- Chloride (Cl)
- Sodium (Na)

## Model and evaluation design

Both feature sets were evaluated using a Random Forest classifier with 400 trees, balanced class weights and random seed 42.

The complete-case population was partitioned by monitoring station:

- Training: 1,585 observations from 551 stations.
- Held-out evaluation: 520 observations from 184 stations.
- Reported station overlap: zero.

The models were evaluated on the same held-out observations using ROC-AUC, accuracy, precision, recall, specificity, F1 score, balanced accuracy and Matthews correlation coefficient (MCC).

A paired station-cluster bootstrap with 3,000 replicates was used to estimate uncertainty in the differences between the two feature sets.

## Results

| Metric | Set A | Set B |
|---|---:|---:|
| ROC-AUC | 0.9263 | 0.9768 |
| Accuracy | 84.04% | 92.12% |
| F1 score | 0.8425 | 0.9210 |
| Recall | 87.40% | 94.09% |
| Specificity | 80.83% | 90.23% |
| MCC | 0.6830 | 0.8431 |

### Paired station-cluster bootstrap: Set B minus Set A

| Metric | Observed improvement | 95% percentile CI |
|---|---:|---:|
| ROC-AUC | +0.0505 | +0.0329 to +0.0712 |
| Accuracy | +8.08 percentage points | +5.14 to +11.11 percentage points |
| F1 score | +0.0785 | +0.0482 to +0.1117 |
| Recall | +6.69 percentage points | +2.40 to +11.55 percentage points |

All four reported intervals exclude zero.

These results support the incremental predictive value of the additional chemical features on this evaluation population. They do not prove that any individual added feature caused the improvement.

## Main contribution

AquaTrust provides a comparative, station-aware evaluation of basic and extended chemical feature sets for classifying groundwater hardness exceedance in Bihar.

The contribution lies in the research question, evaluation design and quantified comparison—not in proposing a new machine-learning algorithm.

## Limitations and research status

- The evaluation is station-disjoint but is not pristine external validation; test results were examined during earlier project comparisons.
- Geographic transfer to unseen districts, other states and future time periods has not been established by the reported station-held-out results.
- The complete-case population may be affected by missing-data and selection bias.
- Repeated-measurement filtering is heuristic and may remove legitimate observations.
- The expected Step 25 SHAP output files were missing at the Step 30 evidence freeze. No completed SHAP-based findings are claimed here.
- Calibration files exist, but probability calibration must be reviewed before drawing conclusions about probability reliability.
- Independent geographic validation and an operational cost analysis remain future work.

AquaTrust is a research prototype for hardness-exceedance screening. It does not replace laboratory analysis or determine overall drinking-water safety.

## Reproducibility

The repository contains the analysis notebook, documented dependencies and selected results and audit artifacts.

Run the notebook in Google Colab after verifying its data-download URL and output paths. Review the notebook from the beginning to the end; the existence of saved outputs alone does not guarantee that every analysis can be reproduced.

The fixed seed is 42. See the notebook, result tables and reproducibility manifest for additional experiment details.

## Data and responsible reuse

The source dataset is linked above. Check its applicable access and reuse terms before redistributing copies. Do not commit credentials, access tokens or unrelated personal data.

## Project status

**Comparative evaluation completed; explainability and calibration audit not yet fully verified.**
