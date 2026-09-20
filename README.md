# PPI Cancer Prediction

Predicting AML (Acute Myeloid Leukemia)-associated genes from protein-protein
interaction (PPI) network structure. The idea: genes that interact heavily
with known cancer genes are themselves more likely to be relevant to cancer,
so network position and connectivity to known AML genes are used as features
for classical ML models.

## Data

- [`9606.protein.aliases.v12.0.txt`](data/9606.protein.aliases.v12.0.txt) — STRING protein ID to gene symbol aliases (human, taxon 9606)
- [`9606.protein.links.v12.0.txt.gz`](data/9606.protein.links.v12.0.txt.gz) — STRING protein-protein interaction edges with combined confidence scores
- [`Cosmic_CancerGeneCensus_v103_GRCh37.tsv`](data/Cosmic_CancerGeneCensus_v103_GRCh37.tsv) — COSMIC Cancer Gene Census, used to label genes as AML-associated or not

Data files are not tracked in git (see `.gitignore`) — download them from
[STRING](https://string-db.org/) and [COSMIC](https://cancer.sanger.ac.uk/census)
and place them under `data/`.

## Approach

1. **Data preparation** — map STRING protein IDs to HGNC gene symbols, merge
   into a labeled PPI edge list (`ppi_labeled`), and mark genes present in the
   COSMIC census with an AML-associated tumour type.
2. **Network construction** — build a weighted, undirected interaction graph
   with `networkx`, using STRING's `combined_score` as edge weight.
3. **Feature engineering** — per-gene features derived from the graph:
   degree centrality, fraction of neighbors that are AML genes, weighted
   "AML influence" (neighbor interaction strength), and a residual comparing
   observed vs. expected AML-neighbor count given a gene's degree.
4. **Modeling** — Logistic Regression, Random Forest, and XGBoost (baseline +
   `RandomizedSearchCV`-tuned), evaluated with stratified k-fold CV and a
   held-out test set, plus SHAP analysis on the tuned XGBoost model.
5. **Model comparison** — ROC-AUC and PR-AUC across models. PR-AUC is treated
   as the primary metric since AML genes are a small minority class and
   ROC-AUC is optimistic under that imbalance.
6. **Graph Neural Network** (in progress) — planned as a follow-up to the
   classical models, learning representations directly from graph structure
   instead of hand-engineered features.

### Avoiding label leakage

Graph features like "fraction of neighbors that are AML genes" are
label-derived, so they must be computed with test-set genes' AML status
masked as unknown — otherwise a node's feature can leak information about
which of its neighbors are held out for evaluation. The notebook splits
genes into train/test *before* computing these features, and only uses
training-set labels when building them.

## Setup

```
python -m venv ppi-env
.\ppi-env\Scripts\Activate
pip install -r requirements.txt
```

## Status

Classical models (Logistic Regression, Random Forest, XGBoost) are trained
and compared; XGBoost has generally performed best on PR-AUC. The Graph
Neural Network section is a stub and not yet implemented — see Section 8 in
[`ppi_cancer_prediction.ipynb`](ppi_cancer_prediction.ipynb).
