---
name: cluster-report
description: Explore several clustering algorithms on a user-supplied dataset, select one, and write a one-page report. Use only when the user explicitly invokes cluster-report by name.
disable-model-invocation: true
---

# Clustering analysis report

## Inputs (from the user's message)

Data file, the feature columns to cluster, an ID column, any known label column, and a seed (default 432). Ask once if the features are unclear. Never cluster on IDs or labels.

## Rules

- Fit, tune, and select using the features only. A known label may be used only after the final method is chosen, to interpret its clusters. Never use it to pick a method, K, or preprocessing.
- Report only numbers you computed in this run. Set the seed for every random step.

## Steps

1. **Check the data.** Report n, p, missing values, and feature scales. Stop and ask if features are missing.
2. **Preprocess.** If p > n, first keep the 1,000 features with the highest variance on their original scale (or all, if fewer). Then standardize each kept feature (mean 0, SD 1), compute PCA, and cluster on the first d component scores, where d is the smallest number explaining 50% of the variance (at least 2, at most 20). If p ≤ n, skip the filter and PCA, and cluster the standardized features. Report the filter, d, and the variance explained.
3. **Algorithms.** Fit at least three:
   - K-means (25 random starts),
   - agglomerative hierarchical clustering with Ward linkage,
   - one non-centroid method, preferably spectral clustering with a nearest-neighbor affinity, or a Gaussian mixture.
4. **Number of clusters.** For each algorithm, try K = 2 to min(8, n/5).
5. **Criteria.** For each algorithm and K, compute:
   - the mean silhouette width in the clustering space;
   - stability: refit on 50 random subsamples of 80% of the rows and average the adjusted Rand index (ARI) with the full-data labels on the shared rows.
6. **Selection.** Keep candidates with stability ≥ 0.75. Among those, choose the highest silhouette. If several are within 0.02 of it, prefer smaller K, then the simpler method (K-means, then hierarchical, then the rest). If none reach 0.75, choose the most stable and say the structure is weak.
7. **Interpret.** For the selected clustering only, report cluster sizes, the features that most separate the clusters (largest standardized mean differences, top 5 to 10), and, if a known label exists, a cluster-by-label table and the ARI with it.

## Report: one printed page, Letter size, 11-point text

The whole report, including all text, figures, and tables, must fit on one printed page. Present clustering results from the selected method only. Mention the other methods in one or two sentences, with their best silhouette and stability values, only to justify the choice. Do not show their cluster assignments, plots, or tables.

Order:

1. **Data and preprocessing** (2 sentences).
2. **Methods and selection** (2 to 3 sentences), naming the algorithms, the K range, the criteria, and the rule.
3. **Figure** (about 6.5 × 2.6 in): left, the selected clusters on the first two PCs; right, silhouette widths of every observation by cluster, or a heat map of the top separating features. Use colorblind-safe colors and plain axis labels.
4. **Table**: cluster sizes and, if available, the cluster-by-label counts.
5. **Interpretation** (3 to 4 sentences): what distinguishes the clusters, and how well they match any known label.
6. **Limitations** (2 sentences).

Write a self-contained HTML or PDF plus the analysis script. Print to Letter at 11 pt and shorten until the report fits on one page. Every number in the text must match printed output.
