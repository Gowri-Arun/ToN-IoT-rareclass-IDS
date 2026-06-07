# Rare Class Analysis: MITM

## Overview

MITM (Man-in-the-Middle) is the rarest class in the ToN-IoT dataset. This analysis compares the final model against the multi-class baseline.

## MITM Data Distribution

- **Train samples**: 834
- **Test samples**: 209

## Final Model (Notebook 03) MITM Metrics

| Metric | Value |
|---|---|
| Precision | 60.90% |
| Recall | 90.91% |
| F1-score | 72.94% |

## Baseline (Notebook 02) MITM Metrics

| Metric | Value |
|---|---|
| Precision | 72.43% |
| Recall | 64.11% |
| F1-score | 68.02% |

## Interpretation

- The **baseline** achieves higher MITM precision (72.43% vs 60.90%).
- The **final model** achieves much higher MITM recall (90.91% vs 64.11%), correctly identifying 190 out of 209 MITM test samples.
- For intrusion detection, **high recall** is typically more valuable — catching rare attacks is the primary objective.
- The moderate precision indicates that some non-MITM samples are flagged as MITM, which is an acceptable trade-off.

## Confusion Sources

From the final model's confusion matrix:

**MITM true samples (209 total):**
- 190 correctly predicted as MITM
- Remaining errors include confusion with `dos`, `injection`, `normal`, `password`, `ransomware`, `scanning`, and `xss`

**Non-MITM samples misclassified as MITM** (primarily from):
- `dos` (51 false positives)
- `password` (40 false positives)
- `normal` (13 false positives)
- `scanning` (9 false positives)

## Future Work

- Improve MITM precision using SHAP-based feature analysis
- Add pairwise MITM-vs-confuser specialist models
- Use calibrated probabilities for better decision thresholds
- Run multiple seeds for variance estimation
- Compare directly against the reference paper pipeline under the same train/test split
