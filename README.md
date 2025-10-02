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
### 1) Exploratory Data Analysis

<p align="center">
  <img src="https://github.com/user-attachments/assets/99528180-388f-4b4a-930c-252be026edb3" alt="Output figure 1" width="780"><br>
  <em>Figure 3: Log-Normal Distribution of Variables</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/88621e63-7586-49fd-b88a-8045bb53d9ea" alt="Output figure 2" width="780"><br>
  <em>Figure 8: Percent Bleaching Over Time by Oceans</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/b6a06501-9316-415b-afc1-6fa9cc69f6e2" alt="Output figure 3" width="780"><br>
  <em>Figure 9: Correlation Heatmap</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/cb87d6f0-891b-4722-9eb7-b404fbd766f3" alt="Performance comparison of models" width="780"><br>
  <em>Figure 4: Performance comparison of different models for coral reef bleaching prediction</em>
</p>


<p align="center">
  <img src="https://github.com/user-attachments/assets/e34e13da-9b87-4bec-86e8-de23dff5d34c" alt="Output figure 6" width="780"><br>
  <em>Figure 6: [Your caption for this figure]</em>
</p>


### 2) Model Diagnostics

<p align="center">
  <img src="https://github.com/user-attachments/assets/e967973e-0812-425f-a29c-6cde707d3c3a"  alt="Output figure 14" width="680"><br>
  <em>Figure 14: Normalized Confusion Matrix (Test Set)</em>
</p>

### 3) SHAP Explainability
<p align="center">
  <img src="https://github.com/user-attachments/assets/0954b464-4b6e-4a68-86ee-ab12f70bcf52" alt="Output figure 5" width="780"><br>
  <em>Figure 5: [Your caption for this figure]</em>
</p>


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
