# Performance of stacking machine learning model for reducing coral reef bleaching
Full documentation can be found here: [Performance of stacking machine learning model for reducing coral reef bleaching.pdf](https://github.com/user-attachments/files/22663246/Performance.of.stacking.machine.learning.model.for.reducing.coral.reef.bleaching.pdf)

## Project Objective
Coral reefs play a crucial role in safeguarding marine ecosystems and coastal regions. However, they are increasingly threatened by climate change and global warming, which put their survival at risk. This project aims to investigate coral reef bleaching caused by climate change and use machine learning and modeling techniques to reduce bleaching probabilities.
## Dataset
- [BCO-DMO Global Bleaching & Environmental Data](https://www.bco-dmo.org/dataset/773466)

## Methods at a Glance
- **Target:** 3-class severity derived from percent bleaching (Mild 0–10%, Moderate 11–50%, Severe >50%).
- **Preprocess:** Clean “nd” → NaN, impute (mean/median), type cast; ordinal encode (`Exposure`, target class), one-hot encode ocean basin.
- **Feature Selection:** RF-RFE → **top-10** predictors used everywhere:  
  `Distance_to_Shore, Turbidity, Cyclone_Frequency, Depth_m, Percent_Cover, ClimSST, Windspeed, Temperature_Kelvin, SSTA, TSA`.
- **Models:**  
  - Baselines: **Random Forest**, **XGBoost** (5-fold GridSearch).  
  - **Stacking:** RF + XGB → **multinomial Logistic Regression** meta-learner (OOF probabilities; no leakage).
- **Training Protocol:** 70/30 **stratified** split; **StandardScaler** (fit on train only); **SMOTE** on **train only**; fixed seeds.
- **Explainability:**  
  - **Kernel SHAP** on the stack (model-agnostic).  
  - **TreeExplainer** on RF/XGB and an **RF surrogate** of the stack (for fast waterfalls).  
- **Evaluation:** Accuracy, **macro** Precision/Recall/F1, **normalised confusion matrix**.

## Figures

> Place exported images under `figs/` with the filenames below (or adjust paths).  
> All figures were generated from the notebooks in this repo.

### 1) Exploratory Data Analysis

<p align="center">
  <img src=" <img width="1189" height="788" alt="output" src="https://github.com/user-attachments/assets/19cf6af0-bf95-4050-80b4-652b469cca7c" />
 " alt="Log histograms of continuous variables" width="780">
</p>
<p align="center"><em>Fig. 1. Log1p histograms (Distance_to_Shore, Turbidity, Cyclone_Frequency, Depth_m, Percent_Cover, ClimSST, Temperature_Kelvin, Windspeed, SSTA, TSA).</em></p>

<p align="center">
  <img src="figs/categorical_distributions.png" alt="Categorical distributions" width="780">
</p>
<p align="center"><em>Fig. 2. Proportion plots for Ocean_Name, Exposure, and Percent_Bleaching_Category.</em></p>

<p align="center">
  <img src="figs/percent_bleaching_by_ocean_box.png" alt="Percent bleaching by ocean (boxplots)" width="780">
</p>
<p align="center"><em>Fig. 3. Percent Bleaching by ocean basin (box plots).</em></p>

<p align="center">
  <img src="figs/percent_bleaching_by_exposure_violin.png" alt="Percent bleaching by exposure (violin)" width="780">
</p>
<p align="center"><em>Fig. 4. Percent Bleaching by exposure (violin plots).</em></p>

<p align="center">
  <img src="figs/bleaching_over_time_global.png" alt="Global bleaching over time" width="780">
</p>
<p align="center"><em>Fig. 5. Mean Percent Bleaching over time (global).</em></p>

<p align="center">
  <img src="figs/bleaching_over_time_by_ocean.png" alt="Bleaching over time by ocean" width="780">
</p>
<p align="center"><em>Fig. 6. Mean Percent Bleaching over time by ocean basin.</em></p>

<p align="center">
  <img src="figs/correlation_heatmap.png" alt="Correlation heatmap" width="780">
</p>
<p align="center"><em>Fig. 7. Pearson correlation heatmap for numeric variables.</em></p>

---

### 2) Model Diagnostics

<p align="center">
  <img src="figs/feature_importances_across_models.png" alt="Feature importances across models" width="780">
</p>
<p align="center"><em>Fig. 8. Normalised feature importances for RF, XGB, and averaged for the stacking ensemble.</em></p>

<p align="center">
  <img src="figs/confusion_matrix_stacking.png" alt="Normalised confusion matrix for stacking model" width="600">
</p>
<p align="center"><em>Fig. 9. Normalised confusion matrix (test set) for the stacking model.</em></p>

---

### 3) SHAP Explainability

<p align="center">
  <img src="figs/shap_waterfall_mild.png" alt="SHAP waterfall - class Mild" width="700">
</p>
<p align="center"><em>Fig. 10. Local SHAP waterfall (class: Mild) for a representative test instance.</em></p>

<p align="center">
  <img src="figs/shap_waterfall_moderate.png" alt="SHAP waterfall - class Moderate" width="700">
</p>
<p align="center"><em>Fig. 11. Local SHAP waterfall (class: Moderate) for a representative test instance.</em></p>

<p align="center">
  <img src="figs/shap_waterfall_severe.png" alt="SHAP waterfall - class Severe" width="700">
</p>
<p align="center"><em>Fig. 12. Local SHAP waterfall (class: Severe) for a representative test instance.</em></p>

<p align="center">
  <img src="figs/mean_shap_by_class.png" alt="Mean SHAP by class" width="780">
</p>
<p align="center"><em>Fig. 13. Mean SHAP value per feature, stratified by class (Mild/Moderate/Severe).</em></p>

<p align="center">
  <img src="figs/shap_beeswarm_stack.png" alt="SHAP beeswarm for stacking model" width="780">
</p>
<p align="center"><em>Fig. 14. Global SHAP beeswarm for the stacking model (Kernel SHAP).</em></p>

## Key Results

### Test Performance
| Model          | Train Acc | Test Acc | Macro Precision | Macro Recall | Macro F1 |
|---|---:|---:|---:|---:|---:|
| Random Forest  | 0.9787 | 0.8605 | 0.6447 | 0.6933 | 0.6663 |
| XGBoost        | 0.8207 | 0.8163 | 0.5748 | 0.6769 | 0.6099 |
| **Stacking**   | **0.9854** | **0.8644** | **0.6554** | **0.6735** | **0.6631** |

**Takeaway:** The stack edges RF on test accuracy and macro precision, with comparable macro-F1; both exceed XGB.

### Class Probabilities (Stack, Test Set)
- Mild **0.7955**, Moderate **0.1396**, Severe **0.0649**.

### Confusion Highlights (Normalised Rows, Test)
- Mild recall **92.64%**; Moderate **58.46%** (most errors → Mild **29.04%**); Severe **50.95%** (errors split to Moderate/Mild).

### SHAP Insights (Global → Local)
- **Percent_Cover** is the dominant protective driver: increases push predictions toward **Mild** and away from **Moderate/Severe**.
- **Turbidity** has a smaller, bounded protective effect within this dataset’s range.
- Thermal/exposure variables (ClimSST, Temperature_Kelvin, SSTA, TSA, Windspeed, Cyclone_Frequency, Distance_to_Shore, Depth_m) refine risk with smaller, context-dependent impacts.
- Local **waterfalls** corroborate: cover typically provides the largest positive/negative shift from baseline for Mild.

### Feasible, SHAP-Guided Interventions (Test, Mean Probabilities)
| Class | Baseline | After Intervention | Abs Δ | Rel Δ |
|---|---:|---:|---:|---:|
| **Mild** | 0.7955 | **0.8989** | **+0.1033** | **+12.99%** |
| **Moderate** | 0.1396 | **0.0925** | **−0.0470** | **−33.71%** |
| **Severe** | 0.0649 | **0.0086** | **−0.0563** | **−86.75%** |

Edits: +10 percentage points to **Percent_Cover** where low; very small, bounded turbidity adjustme
