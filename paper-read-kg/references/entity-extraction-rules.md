# Entity Extraction Normalization Rules

> How to recognize and standardize method names, datasets, evaluation metrics,
> author names, and theoretical foundations across papers. Ensures cross-paper
> consistency.

---

## Method Name Extraction

| Pattern | Example | Standardized Output |
|---|---|---|
| Abbreviation + full name | "DDMF (Distance Distribution and Multi-Index)" | `DDMF` |
| Algorithm keyword + name | "Proposed YYY algorithm based on XXX" | `YYY` |
| Well-known method reference | "Based on K-Shell decomposition" | `K-Shell` |
| Custom model name | "GNNTALA model" | `GNNTALA` |
| Chinese method description | "Relative importance node mining based on neighborhood propagation" | Extract short form (e.g., `neighborhood_propagation`) |

## Dataset Identification

| Pattern | Example |
|---|---|
| Proper noun / capitalized abbreviation | "CIFAR-10", "ImageNet" |
| Domain + data description | "Primary and secondary school screening data from City X, 2018–2022" |
| Public database name | "MIMIC-III", "PhysioNet" |
| Self-collected data | Annotate with `[self-collected]` |
| Not mentioned | Write `No specific dataset mentioned` |

## Evaluation Metric Standardization

| Raw Text | Standardized | Applicable Scenario |
|---|---|---|
| "Accuracy", "ACC" | `Accuracy` | Classification |
| "AUC", "Area under ROC" | `AUC` | Classification / ranking |
| "Kendall τ", "Kendall coefficient" | `Kendall τ` | Ranking correlation |
| "F1", "F1-score" | `F1` | Classification |
| "Epidemic threshold" | `Epidemic Threshold` | Spreading model |
| "MSE", "RMSE", "MAE" | `MSE` / `RMSE` / `MAE` | Regression |
| "SIR infection rate" | `SIR Infection Rate` | Spreading dynamics |

## Author Disambiguation

| Situation | Handling |
|---|---|
| Same author, Chinese + English name | Establish mapping: e.g. `赵娜 ↔ Zhao Na`, unify under one canonical form |
| Advisor/student relationship | Infer from corresponding author notation and acknowledgments; annotate in the relationships field |
| Author order | Preserve original order; mark corresponding author with `*` |

## Theoretical Foundation Annotation

Identify cited foundational theories from the paper and annotate as a tag:

| Theory Domain | Recognition Keywords |
|---|---|
| Graph theory | degree distribution, adjacency matrix, connectivity, shortest path, minimum spanning tree |
| Information theory | information entropy, mutual information, KL divergence, Shannon entropy |
| Statistical learning | VC dimension, bias-variance, regularization, Bayesian inference |
| Spreading dynamics | SIR, SIS, SIRS, basic reproduction number |
| Game theory | Nash equilibrium, evolutionary game |
| Optimization theory | convex optimization, gradient descent, Lagrange multiplier |
