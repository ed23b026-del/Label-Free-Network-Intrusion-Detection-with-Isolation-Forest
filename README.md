# Label-Free Network Intrusion Detection with Isolation Forest

Unsupervised anomaly detection for network intrusion using **Isolation Forest** on the **NSL-KDD** dataset — detecting attacks without ever training on attack labels, and benchmarked against a supervised Random Forest baseline to quantify the real-world trade-off between the two approaches.

## Overview

Most intrusion detection projects use supervised classification, which requires labeled examples of every attack type it needs to catch — a real limitation, since new ("zero-day") attack types by definition have no labeled examples yet. This project uses **Isolation Forest**, an unsupervised method that flags anomalies based on how easily a data point can be isolated by random partitioning, rather than learning from labeled attack examples.

## Dataset

- **NSL-KDD** — a refined, de-duplicated version of the KDD Cup 99 network intrusion benchmark
- 125,973 labeled network connection records, 41 features (traffic volume, connection behavior, error rates, host-based statistics)
- Labels collapsed to binary: `normal` vs. `attack` (~46.5% attack rate)

## Results

| Metric | Value |
|---|---|
| Baseline F1 (unscaled, untuned) | 0.576 |
| Tuned F1 (after preprocessing + `max_samples` fix) | **0.915** |
| F1 (proper train/test split) | 0.867 |

**Threshold trade-off (precision-recall analysis):**

| Strategy | Precision | Recall |
|---|---|---|
| F1-optimal | 0.913 | 0.919 |
| Alert-budget (10% FPR) | 0.890 | 0.925 |
| Recall-biased (≥97%) | 0.693 | 0.970 |

**Supervised vs. unsupervised benchmark:**

| Model | F1 (known attacks) | Detection rate (simulated zero-day) |
|---|---|---|
| Random Forest (supervised) | 0.996 | 0.757 |
| Isolation Forest (unsupervised) | 0.867 | **0.904** |

## Key Finding

Random Forest outperforms Isolation Forest decisively on attack types it has seen labeled examples of. But when tested on a completely unseen attack type — simulating a real zero-day scenario — Isolation Forest generalizes better (90.4% vs. 75.7% detection), since it never depended on attack labels in the first place. This reflects the real justification for anomaly detection in production security: supervised models are stronger against known threats, but unsupervised methods have a measurable edge against threats that haven't been labeled yet.

## Tech Stack

`Python` · `pandas` · `scikit-learn` · `matplotlib`

