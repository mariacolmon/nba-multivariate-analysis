# Multivariate Analysis of NBA Player Profiles (2022–23)

Unsupervised exploration of a mixed-type dataset of 402 NBA players using dimensionality reduction and clustering techniques. The analysis was developed as part of the *Multivariate Analysis* course in the MSc in Statistics for Data Science (Universidad Carlos III de Madrid).

**Tools:** R · ggplot2 · FactoMineR · factoextra · dbrobust · cluster

---

## Overview

The dataset originates from [Kaggle](https://www.kaggle.com/datasets/justinas/nba-players-data) and covers the 2022–23 NBA season. After preprocessing, it contains 402 drafted players described by 9 quantitative variables (age, height, weight, draft pick, games played, points, rebounds, assists, true shooting %) and 2 categorical variables (draft experience tier, US/international origin).

The analysis is split into two tasks:

### Task 1 — EDA and Principal Component Analysis

- Exploratory analysis of a mixed-type dataset: summary statistics, normalization, log-transformations for skewed variables, pairwise scatterplots, correlation heatmap, and conditional visualizations by player experience tier.
- PCA performed on the **correlation matrix** via manual eigendecomposition (verified against `prcomp`). Component loadings are interpreted to identify a *performance axis* (PC1) and a *player role axis* (PC2: interior vs. perimeter players).
- Component selection compared across five criteria: cumulative variance threshold, Kaiser's rule, Jolliffe's modification, Cattell's scree plot, and a formal dimensionality test. Four components are retained (>70% variance explained).
- **Stability analysis** via jackknife leave-one-out resampling: the PCA configuration is recomputed 402 times and per-player Euclidean displacement is measured. Influential observations are identified and mapped back to domain knowledge (elite/statistically extreme players).

### Task 2 — MDS and Cluster Analysis

- **Gower's distance** computed for the mixed-type dataset. Non-Euclidean property detected via eigenanalysis of the Gram matrix; Euclidean embedding applied via `make_euclidean()`.
- **Robust Generalized Gower distance** (robust Mahalanobis + Jaccard + Hamming) computed using `dbrobust`. Compared against classical Gower through cophenetic correlation and explained variance (3.7% vs. 46.9% in the first two MDS axes).
- MDS configurations interpreted by correlating principal coordinates with original variables (Pearson, Spearman, Cramér's V). Variable influence visualized via partial influence curves.
- **Hierarchical clustering** (single / complete / average linkage) applied to both distance matrices. Linkage selected by cophenetic correlation. Dendrogram cut at k=14 with rare profiles aggregated into an outlier group.
- **K-means** (k=3 and k=8) fitted in standardized feature space and in PCA-reduced space (4 components). K selected by elbow plot and silhouette index. Three interpretable archetypes recovered: *interior/big-man*, *low-usage role player*, and *experienced scorer/playmaker*.
- **PAM** (k-medoids) run with Euclidean and Mahalanobis distances as a robustness check. Results confirm the continuous, overlapping nature of player profiles.

---

## Repository Structure

```
├── data/
│   └── nba_2022_23.csv          # Preprocessed dataset (402 players, 2022–23 season)
├── analysis/
│   ├── 01_eda.Rmd               # Data preprocessing and exploratory analysis
│   ├── 02_pca.Rmd               # PCA: eigendecomposition, component selection, stability
│   └── 03_mds_clustering.Rmd   # MDS, hierarchical clustering, k-means, PAM
└── reports/
    ├── task1_report.pdf         # Rendered report: EDA + PCA
    └── task2_report.pdf         # Rendered report: MDS + Clustering
```

---

## Key Results

| Method | Main finding |
|---|---|
| PCA | PC1 = on-court performance; PC2 = body size / player role. 4 components retain >70% variance. |
| PCA stability | Configuration robust under jackknife; influential players match known NBA elite profiles. |
| MDS (Gower) | Only 3.7% variance in first 2 axes; PC1 ↔ experience/scoring, PC2 ↔ height/origin. |
| MDS (robust) | 46.9% variance in first 2 axes; clear separation by origin (PC1) and experience tier (PC2). |
| Hierarchical | Average linkage + robust Gower: cophenetic r = 0.921. Large homogeneous core + small specialized clusters. |
| K-means (k=3) | Three archetypes stable across raw and PCA-reduced spaces. Silhouette consistent at ~0.19–0.24. |

---

## How to Run

1. Clone the repository and open it in RStudio.
2. Install required packages if needed:
```r
install.packages(c("tidyverse", "FactoMineR", "factoextra", "cluster",
                   "kableExtra", "GGally", "patchwork", "dendextend"))
# dbrobust may require manual installation — see https://github.com/[source]
```
3. Knit each `.Rmd` file in order (`01` → `02` → `03`). All scripts read from `data/nba_2022_23.csv`.

---

## Authors

María Colado Montañés · Luis Calderón Yunda  
MSc Statistics for Data Science, Universidad Carlos III de Madrid
