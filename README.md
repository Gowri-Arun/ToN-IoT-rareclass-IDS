# ToN-IoT-rareclass-IDS

An imbalance-aware intrusion detection framework for rare attack classification in IoT networks, focused on the ToN-IoT dataset and the rare MITM attack class.

## Overview

Most intrusion detection models perform extremely well on binary normal-vs-attack classification, but rare attack classes remain harder to detect in true multi-class settings. This project studies that problem on the ToN-IoT network dataset.

The main focus is **rare-class multi-class intrusion detection**, especially the **MITM** class, which has far fewer samples than other attack categories.

## Project Motivation

Initial binary experiments showed that normal-vs-attack classification on ToN-IoT is nearly saturated. The best binary models achieved almost perfect results.

Because of that, this repository focuses on the harder and more meaningful task:

> Can we improve true multi-class attack classification while explicitly improving detection of the rare MITM class?

## Dataset

Dataset used: **ToN-IoT network dataset**

Train/test split:

| Split | Samples |
|---|---:|
| Train | 168,834 |
| Test | 42,209 |

The binary experiments use the `label` column:

| Label | Meaning |
|---|---|
| 0 | Normal |
| 1 | Attack |

The true multi-class experiments use the `type` column:

```text
backdoor
ddos
dos
injection
mitm
normal
password
ransomware
scanning
xss
```

MITM is the rarest class:

| Class | Train Samples | Test Samples |
| ----- | ------------: | -----------: |
| MITM  |           834 |          209 |

## Repository Structure

```text
ToN-IoT-rareclass-IDS/
│
├── notebooks/
│   ├── 01_toniot_binary_baseline.ipynb
│   ├── 02_toniot_multiclass_baseline.ipynb
│   ├── 03_rare_class_lightgbm_ovr_final.ipynb
│   └── 04_ablation_suite.ipynb
│
├── results/
│   ├── 01_toniot_binary_baseline_results.csv
│   ├── 02_toniot_multiclass_baseline_summary.csv
│   ├── 02_toniot_multiclass_baseline_per_class_report.csv
│   ├── 02_toniot_multiclass_baseline_confusion_matrix.csv
│   ├── final_model_d_summary.csv
│   ├── final_model_d_per_class_report.csv
│   ├── final_model_d_confusion_matrix.csv
│   ├── table_1_related_work_context_rounded.csv
│   ├── table_2_ablation_results_rounded.csv
│   └── table_3_per_class_report_rounded.csv
│
├── docs/
│   ├── methodology.md
│   ├── related_work_context.md
│   ├── rare_class_analysis.md
│   └── experiment_log.md
│
├── paper/
│   └── tables/
│       ├── README.md
│       ├── table_1_related_work_context_rounded.csv
│       ├── table_2_ablation_results_rounded.csv
│       └── table_3_per_class_report_rounded.csv
│
├── requirements.txt
├── .gitignore
└── README.md
```

## Experiments

### 1. Binary ToN-IoT Benchmark

Notebook:

```text
notebooks/01_toniot_binary_baseline.ipynb
```

Target column:

```text
label
```

Models tested:

* XGBoost
* Random Forest
* Logistic Regression
* K-NN
* Decision Tree

Best binary result:

| Model                       | Accuracy |     F1 | Weighted F1 |     AUC |
| --------------------------- | -------: | -----: | ----------: | ------: |
| Random Forest + KMeansSMOTE |   99.89% | 99.93% |      99.89% | 100.00% |

Conclusion: binary detection is nearly saturated, so the main research focus shifts to true multi-class rare-class detection.

---

### 2. True Multi-Class Baseline

Notebook:

```text
notebooks/02_toniot_multiclass_baseline.ipynb
```

Target column:

```text
type
```

Pipeline:

```text
Log transform + Mutual Information + PCA + KMeansSMOTE + Random Forest
```

Results:

| Metric            |  Score |
| ----------------- | -----: |
| Accuracy          | 98.47% |
| Weighted F1       | 98.46% |
| Macro F1          | 95.39% |
| Balanced Accuracy | 95.03% |
| MITM Precision    | 72.43% |
| MITM Recall       | 64.11% |
| MITM F1           | 68.02% |

Conclusion: the baseline is strong overall, but MITM recall is limited.

---

### 3. Final Model

Notebook:

```text
notebooks/03_rare_class_lightgbm_ovr_final.ipynb
```

Final selected model:

```text
No MI, No PCA + LightGBM One-vs-Rest + MITM-targeted KMeansSMOTE
```

Key design choices:

* True multi-class classification using `type`
* Log transformation
* Standard scaling
* No Mutual Information feature selection
* No PCA
* MITM-targeted KMeansSMOTE
* One-vs-Rest LightGBM classifiers
* Per-class threshold tuning

Final results:

| Metric            |  Score |
| ----------------- | -----: |
| Accuracy          | 98.96% |
| Weighted F1       | 98.99% |
| Macro F1          | 96.39% |
| Balanced Accuracy | 98.06% |
| Macro AUC OVR     | 99.94% |
| MITM Precision    | 60.90% |
| MITM Recall       | 90.91% |
| MITM F1           | 72.94% |

The final model improves rare MITM recall substantially compared with the Random Forest baseline.

---

### 4. Ablation Suite

Notebook:

```text
notebooks/04_ablation_suite.ipynb
```

Ablations tested:

| ID | Variant                                                   |
| -- | --------------------------------------------------------- |
| A  | No MI, no PCA + OVR XGBoost + MITM KMeansSMOTE            |
| B  | Correlation pruning only + OVR XGBoost + MITM KMeansSMOTE |
| C  | No MI, no PCA + OVR CatBoost + MITM KMeansSMOTE           |
| D  | No MI, no PCA + LightGBM OVR + MITM KMeansSMOTE           |
| E  | MITM sampling ratio sweep: 0.30, 0.50, 0.70, 0.90         |

Best overall model:

| Model                              | Accuracy | Weighted F1 | Macro F1 | MITM F1 |
| ---------------------------------- | -------: | ----------: | -------: | ------: |
| D. LightGBM OVR + MITM KMeansSMOTE |   98.96% |      98.99% |   96.39% |  72.94% |

Best rare-class MITM-F1 ablation:

| Model                                | MITM Precision | MITM Recall | MITM F1 |
| ------------------------------------ | -------------: | ----------: | ------: |
| B. Correlation pruning + OVR XGBoost |         62.08% |      88.52% |  72.98% |

Model D is selected as the final model because it gives the best overall weighted F1, macro F1, accuracy, and MITM recall.

## Related Work Context

This project was motivated by prior work on class imbalance in intrusion detection, including the paper:

> A novel intrusion detection system for class imbalance datasets using hybrid sampling with deep learning techniques

That paper reports strong ToN-IoT multi-class results, including 98.67% accuracy and 98.70% F1-score. This repository uses that reported result only as context, not as a strict directly reproduced baseline.

| Method / Source | Dataset | Task | Accuracy (%) | F1-score (%) | Notes |
|---|---|---|---:|---:|---|
| Reference paper reported result | ToN-IoT | Multi-class | 98.67 | 98.70 | Reported headline result from related work |
| ToN-IoT-rareclass-IDS final model | ToN-IoT | Multi-class | 98.96 | 98.99 weighted | Independent rare-class-focused implementation |

Because the exact preprocessing, sampling procedure, model configuration, train/test handling, and F1 averaging protocol may differ, this should be interpreted as reported-result context rather than a strict apples-to-apples benchmark.

The main value of this project is the explicit rare-class MITM analysis.

## Key Contribution

This project contributes:

1. A clean binary-to-multi-class experimental progression.
2. A true multi-class ToN-IoT rare-class IDS pipeline.
3. Explicit MITM rare-class analysis.
4. MITM-targeted KMeansSMOTE oversampling.
5. One-vs-Rest LightGBM classification with threshold tuning.
6. Ablation experiments across XGBoost, CatBoost, LightGBM, correlation pruning, and MITM sampling ratios.
7. Paper-ready result tables and documentation.

## Main Finding

Binary ToN-IoT intrusion detection is nearly saturated, but rare-class multi-class detection remains more challenging.

The final ToN-IoT-rareclass-IDS model achieves strong ToN-IoT multi-class performance while adding explicit rare-class MITM analysis.

## How to Reproduce

Install dependencies:

```bash
pip install -r requirements.txt
```

Run notebooks in this order:

```text
01_toniot_binary_baseline.ipynb
02_toniot_multiclass_baseline.ipynb
03_rare_class_lightgbm_ovr_final.ipynb
04_ablation_suite.ipynb
```

Expected main output files:

```text
results/final_model_d_summary.csv
results/final_model_d_per_class_report.csv
results/final_model_d_confusion_matrix.csv
paper/tables/table_1_related_work_context_rounded.csv
paper/tables/table_2_ablation_results_rounded.csv
paper/tables/table_3_per_class_report_rounded.csv
```

## Documentation

More details are available in:

```text
docs/methodology.md
docs/related_work_context.md
docs/rare_class_analysis.md
docs/experiment_log.md
```

## Notes

This repository currently prioritizes research reproducibility through notebooks, result files, documentation, and paper-ready tables.

Future versions may refactor the notebook logic into reusable Python modules under `src/` and executable scripts under `scripts/`.


