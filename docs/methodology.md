# Methodology

## Dataset

ToN-IoT network dataset.

### Train / Test Split

- **Train**: 168,834 samples
- **Test**: 42,209 samples

### Targets

**Binary target (`label`):** 0 = normal, 1 = attack

**True multi-class target (`type`):**

| Class | Type |
|---|---|
| backdoor | Common |
| ddos | Common |
| dos | Common |
| injection | Common |
| mitm | **Rare** |
| normal | Common |
| password | Common |
| ransomware | Common |
| scanning | Common |
| xss | Common |

### Motivation

Binary detection on ToN-IoT is nearly saturated (Accuracy > 99.8%, AUC ~ 100%). Multi-class rare-class detection is a more meaningful and challenging problem. MITM is the rarest class (834 train samples, 209 test samples) and is the focus of this work.

## Preprocessing

- **Dropped** leakage / high-cardinality identity columns: `src_ip`, `dst_ip`, `http_uri`, `ssl_subject`, `ssl_issuer`, `timestamp`, `date`, `time`
- **Replaced** infinite values with NaN
- **Filled** missing numeric values using train-set column means
- **Encoded** categorical features consistently using train-set label mappings

## Notebooks

### Notebook 01: Binary ToN-IoT Benchmark

- **Notebook**: `notebooks/01_toniot_binary_baseline.ipynb`
- **Target**: `label` (binary)
- **Models**: XGBoost, Random Forest, Logistic Regression, K-NN, Decision Tree
- **Sampling**: KMeansSMOTE
- **Best model**: Random Forest + KMeansSMOTE
- **Best accuracy**: 99.89%
- **Best F1**: 99.93%
- **Best AUC**: 100.00%

### Notebook 02: True Multi-Class Baseline

- **Notebook**: `notebooks/02_toniot_multiclass_baseline.ipynb`
- **Target**: `type` (10 classes)
- **Model**: Random Forest
- **Pipeline**: Log transform + Mutual Information (MI) feature selection + PCA + KMeansSMOTE
- **Accuracy**: 98.47%
- **Weighted F1**: 98.46%
- **Macro F1**: 95.39%
- **MITM F1**: 68.02%

### Notebook 03: Final Model — ToN-IoT-rareclass-IDS

- **Notebook**: `notebooks/03_rare_class_lightgbm_ovr_final.ipynb`
- **Model**: LightGBM One-vs-Rest
- **Features**: Log transform + StandardScaler (no MI, no PCA), 37 features
- **Sampling**: MITM-targeted KMeansSMOTE
- **Thresholding**: Per-class threshold tuning
- **Accuracy**: 98.96%
- **Weighted F1**: 98.99%
- **Macro F1**: 96.39%
- **Balanced Accuracy**: 98.06%
- **MITM Precision**: 60.90%
- **MITM Recall**: 90.91%
- **MITM F1**: 72.94%

### Notebook 04: Ablation Suite

- **Notebook**: `notebooks/04_ablation_suite.ipynb`
- Compares XGBoost, CatBoost, LightGBM, correlation pruning, and MITM sampling ratio sweep
- Model D (LightGBM OVR + no MI/PCA + MITM KMeansSMOTE) selected as the final model
