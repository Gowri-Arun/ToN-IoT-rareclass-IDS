# Experiment Log

## Experiment 01: Binary ToN-IoT Benchmark

- **Notebook**: `notebooks/01_toniot_binary_baseline.ipynb`
- **Target**: `label` (binary: 0 = normal, 1 = attack)
- **Models**: XGBoost, Random Forest, Logistic Regression, K-NN, Decision Tree
- **Sampling**: KMeansSMOTE
- **Best model**: Random Forest + KMeansSMOTE
- **Accuracy**: 99.89%
- **F1**: 99.93%
- **AUC**: 100.00%
- **Conclusion**: Binary detection is nearly saturated on ToN-IoT. Further work should focus on multi-class rare-class detection.

---

## Experiment 02: True Multi-Class Baseline

- **Notebook**: `notebooks/02_toniot_multiclass_baseline.ipynb`
- **Target**: `type` (10 classes)
- **Model**: Random Forest
- **Pipeline**: Log transform + Mutual Information (20 features) + PCA (11 components) + KMeansSMOTE
- **Accuracy**: 98.47%
- **Weighted F1**: 98.46%
- **Macro F1**: 95.39%
- **MITM Precision**: 72.43%
- **MITM Recall**: 64.11%
- **MITM F1**: 68.02%
- **Conclusion**: Strong overall baseline, but rare-class MITM recall (64.11%) is limited and requires improvement.

---

## Experiment 03: Final ToN-IoT-rareclass-IDS Model

- **Notebook**: `notebooks/03_rare_class_lightgbm_ovr_final.ipynb`
- **Model**: LightGBM One-vs-Rest
- **Features**: Log transform + StandardScaler (no MI, no PCA), 37 features
- **Sampling**: MITM-targeted KMeansSMOTE (ratio = 0.7)
- **Thresholding**: Per-class threshold tuning
- **Accuracy**: 98.96%
- **Weighted F1**: 98.99%
- **Macro F1**: 96.39%
- **Balanced Accuracy**: 98.06%
- **MITM Precision**: 60.90%
- **MITM Recall**: 90.91%
- **MITM F1**: 72.94%
- **Conclusion**: Selected as the final model. Gives the best overall weighted F1 and the strongest MITM recall among all variants.

---

## Experiment 04: Ablation Suite

- **Notebook**: `notebooks/04_ablation_suite.ipynb`

### Variants

| ID | Feature Strategy | Model | MITM Ratio | Accuracy | Weighted F1 | Macro F1 | MITM F1 |
|---|---|:---:|---:|---:|---:|---:|
| A | No MI, no PCA | OVR XGBoost | 0.7 | 98.82% | 98.85% | 96.11% | 71.46% |
| B | Correlation pruning only | OVR XGBoost | 0.7 | 98.84% | 98.86% | 96.27% | **72.98%** |
| C | No MI, no PCA | OVR CatBoost | 0.7 | 98.73% | 98.77% | 95.85% | 69.53% |
| **D** | **No MI, no PCA** | **OVR LightGBM** | **0.7** | **98.96%** | **98.99%** | **96.39%** | **72.94%** |
| E (0.3) | No MI, no PCA | OVR XGBoost | 0.3 | 98.87% | 98.89% | 96.17% | 71.57% |
| E (0.5) | No MI, no PCA | OVR XGBoost | 0.5 | 98.88% | 98.91% | 96.24% | 72.20% |
| E (0.7) | No MI, no PCA | OVR XGBoost | 0.7 | 98.82% | 98.85% | 96.11% | 71.46% |
| E (0.9) | No MI, no PCA | OVR XGBoost | 0.9 | 98.87% | 98.90% | 96.26% | 72.48% |

### Conclusions

- **Model D (LightGBM OVR)** is selected as the final model because it achieves the best overall metrics (accuracy, weighted F1, macro F1) while delivering strong MITM recall.
- **Model B (Correlation pruning + OVR XGBoost)** achieves the highest MITM F1 (72.98%) among all ablations and is noted as the strongest rare-class-focused variant.
