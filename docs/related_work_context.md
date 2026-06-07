# Related Work Context

## Reference

> "A novel intrusion detection system for class imbalance datasets using hybrid sampling with deep learning techniques"

## Reported Result Context

That paper reports ToN-IoT multi-class results, which are included here as context rather than as a strict directly reproduced baseline.

### Reference Paper ToN-IoT Multi-Class Reported Result

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

## Contextual Comparison Table

| Method / Source | Dataset | Task | Accuracy (%) | F1-score (%) | Notes |
|---|---|---|---:|---:|---|
| Reference paper reported result | ToN-IoT | Multi-class | 98.67 | 98.70 | Reported headline result from related work |
| ToN-IoT-rareclass-IDS final model | ToN-IoT | Multi-class | 98.96 | 98.99 weighted | Independent rare-class-focused implementation |

This table is included only as reported-result context. It is not a strict controlled comparison because the exact preprocessing, feature-selection, sampling, model-training, and metric-averaging protocols may differ. The main contribution of this repository is the rare-class-focused ToN-IoT analysis, especially explicit MITM precision, recall, and F1 reporting.
