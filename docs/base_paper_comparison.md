# Base Paper Comparison

## Reference

> "A novel intrusion detection system for class imbalance datasets using hybrid sampling with deep learning techniques"

## Reported Results

### Base Paper ToN-IoT Multi-Class

- **Accuracy**: 98.67%
- **F1-score**: 98.70%

### ToN-IoT-rareclass-IDS Final Model

- **Model**: No MI, no PCA + LightGBM OVR + MITM-targeted KMeansSMOTE
- **Accuracy**: 98.9552%
- **Weighted F1**: 98.9858%
- **Macro F1**: 96.3870%
- **Balanced Accuracy**: 98.0609%
- **MITM Precision**: 60.8974%
- **MITM Recall**: 90.9091%
- **MITM F1**: 72.9367%

### Improvement

- **Accuracy improvement**: +0.29 percentage points
- **F1 improvement**: +0.29 percentage points (comparing the base paper headline F1 to this project's weighted F1)

## Caveat

The base paper reports a single headline F1-score, while this project explicitly reports weighted F1, macro F1, and MITM-specific precision / recall / F1. The comparison should therefore be interpreted as a headline-level comparison unless the base paper's averaging method is fully matched.

## Comparison Table

| Method | Dataset | Task | Accuracy (%) | F1-score (%) | Macro F1 (%) | MITM Recall (%) | MITM F1 (%) |
|---|---|---|---:|---:|---:|---:|
| Base paper | ToN-IoT | Multi-class | 98.67 | 98.70 | Not reported | Not reported | Not reported |
| ToN-IoT-rareclass-IDS | ToN-IoT | Multi-class | 98.96 | 98.99 weighted | 96.39 | 90.91 | 72.94 |

## Claim

> ToN-IoT-rareclass-IDS improves the reported headline ToN-IoT multi-class performance while adding explicit rare-class MITM analysis.
