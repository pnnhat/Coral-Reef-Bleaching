# Performance of stacking machine learning model for reducing coral reef bleaching
> Predicting bleaching severity and turning explanations into “what-if” scenarios.

**Full paper:** [Performance of stacking machine learning model for reducing coral reef bleaching (PDF)](https://github.com/user-attachments/files/22663246/Performance.of.stacking.machine.learning.model.for.reducing.coral.reef.bleaching.pdf)  
**Dataset:** [BCO-DMO Global Bleaching & Environmental Data](https://www.bco-dmo.org/dataset/773466)

## Table of Contents
- [Project Objective](#project-objective)
- [Methods at a Glance](#methods-at-a-glance)
- [Key Results](#key-results)
- [Figures](#figures)
- [Interpretation in One Paragraph](#interpretation-in-one-paragraph)

## Project Objective
Coral reefs play a crucial role in safeguarding marine ecosystems and coastal regions. However, they are increasingly threatened by climate change and global warming, which put their survival at risk. This study predicts **bleaching severity** (Mild/Moderate/Severe) using a **stacking ensemble** and translates model explanations into **feasible interventions** that reduce the probability of high-severity bleaching.

## Methods at a Glance
- **Target:** 3-class severity from percent bleaching (Mild 0–10%, Moderate 11–50%, Severe >50%).  
- **Preprocessing:** Impute mean/median on null values; **Ordinal encode** (`Exposure`, target), **One-Hot encode** ocean basin.  
- **Feature Selection (RF-RFE):** **Top-10** predictors used throughout:  
  `Distance_to_Shore, Turbidity, Cyclone_Frequency, Depth_m, Percent_Cover, ClimSST, Windspeed, Temperature_Kelvin, SSTA, TSA`.  
- **Models:**  
  - Baselines: **Random Forest**, **XGBoost**.  
  - **Stacking:** RF + XGB → **multinomial Logistic Regression** meta-learner (out-of-fold probabilities; no leakage).  
- **Training Protocol:** 70/30 split, StandardScaler, SMOTE.  
- **Explainability:** **Kernel SHAP** on the stack (global), **TreeExplainer** on RF/XGB and an **RF surrogate** of the stack (fast waterfalls).  
- **Evaluation:** Accuracy, **macro** Precision/Recall/F1, **normalised confusion matrix**.  
- **Interventions:** SHAP-guided, **feasible** edits of modifiable features (e.g., +cover).
  
## Key Results

###  Exploratory Data Analysis

<p align="center">
  <img src="https://github.com/user-attachments/assets/99528180-388f-4b4a-930c-252be026edb3" alt="Output figure 1" width="650"><br>
  <em>Figure 3: Log-Normal Distribution of Variables</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/88621e63-7586-49fd-b88a-8045bb53d9ea" alt="Output figure 2" width="650"><br>
  <em>Figure 8: Percent Bleaching Over Time by Oceans</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/b6a06501-9316-415b-afc1-6fa9cc69f6e2" alt="Output figure 3" width="600"><br>
  <em>Figure 9: Correlation Heatmap</em>
</p>

### Model Diagnostics

| Model          | Train Acc | Test Acc | Macro Precision | Macro Recall | Macro F1 |
|---|---:|---:|---:|---:|---:|
| Random Forest  | 0.9787 | 0.8605 | 0.6447 | 0.6933 | 0.6663 |
| XGBoost        | 0.8207 | 0.8163 | 0.5748 | 0.6769 | 0.6099 |
| **Stacking**   | **0.9854** | **0.8644** | **0.6554** | **0.6735** | **0.6631** |

**Class probabilities (Stack, test):** Mild **0.7955**, Moderate **0.1396**, Severe **0.0649**  
**Confusion Matrix:** Mild recall **92.64%**; Moderate **58.46%**; Severe **50.95%**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e967973e-0812-425f-a29c-6cde707d3c3a"  alt="Confusion matrix" width="500"><br>
  <em>Figure 14: Normalized confusion matrix (test set)</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/25319836-f2e8-4e2f-9c01-37600e9d0041" alt ="Feature importance" width = "650"><br>
  <em>Feature importance across models (Percent_Cover dominates)</em>
</p>

### SHAP Explainability

<p align="center">
  <img src="https://github.com/user-attachments/assets/cb87d6f0-891b-4722-9eb7-b404fbd766f3" alt="Mean SHAP by class" width="650"><br>
  <em>Figure 15: Mean SHAP values by class for the stacking model: Cover is the dominant protective driver.</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/0954b464-4b6e-4a68-86ee-ab12f70bcf52" alt="Beeswarm" width="650"><br>
  <em>Figure 16: Beeswarm (global explanation): High cover shifts predictions toward Mild; Other factors have smaller effects.</em>
</p>

### Feasible Interventions
SHAP-guided changes to modifiable features. **Percent_Cover** give large probability shifts on the test set.

| Class | Baseline | After Intervention | Abs Δ | Rel Δ |
|---|---:|---:|---:|---:|
| **Mild** | 0.7955 | **0.8989** | **+0.1033** | **+12.99%** |
| **Moderate** | 0.1396 | **0.0925** | **−0.0470** | **−33.71%** |
| **Severe** | 0.0649 | **0.0086** | **−0.0563** | **−86.75%** |

<p align="center">
  <img src="https://github.com/user-attachments/assets/e34e13da-9b87-4bec-86e8-de23dff5d34c" alt="Before/After interventions" width="650"><br>
  <em>Figure 21: Before vs After interventions.</em>
</p>

## Interpretation in One Paragraph
The stacking ensemble attains the strongest overall test accuracy while maintaining balanced macro performance under class imbalance. SHAP analyses consistently show that higher live coral cover is the key factor in protecting reefs. More coral cover boosts the chances of Mild bleaching outcomes while reducing the likelihood of Moderate or Severe ones. Other factors like turbidity, thermal conditions, and exposure play smaller roles. By translating this into practical steps, small, achievable increases in coral cover (paired with careful water quality improvements) can significantly lower the risk of severe bleaching. This gives a clear, reliable guide for focusing restoration and protection efforts where they’ll have the biggest impact.
