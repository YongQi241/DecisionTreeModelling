# Lab 2 Decision Tree — Technical Handoff

## Reproducibility

- Dataset: Breast Cancer Wisconsin (Diagnostic), loaded from scikit-learn
- Source: https://doi.org/10.24432/C5DW2B
- Samples/features: 569 / 30
- Classes: malignant (0), benign (1)
- Split: 80/20, stratified
- Random state: 42
- Hyperparameter selection: 5-fold cross-validation on training data

## Locked results

- Baseline: test accuracy 0.9123, error rate 0.0877, depth 7, leaves 19
- Max Depth: max_depth=4, test accuracy 0.9386
- Min Samples Leaf: min_samples_leaf=3, test accuracy 0.9123
- Pruning: ccp_alpha=0.005934, test accuracy 0.9386
- Selected model: Max Depth

## Selection rationale

Max Depth and Pruning tie at 93.86% test accuracy. Max Depth is selected because malignant recall is 92.86%, compared with 90.48% for Pruning, and its weighted F1 is slightly higher. Pruning remains the simplest competitive alternative.

## Files

- `Decision_Tree_2.ipynb`: executed source notebook and embedded outputs
- `results/model_comparison.csv`: authoritative model metrics
- `results/hyperparameter_summary.csv`: selected settings and CV scores
- `results/figures/`: report-ready trees, confusion matrix, feature importance, tuning, and comparison figures

All report and presentation numbers should be copied from these files.
