# PPI Cancer Prediction

Predicting AML (Acute Myeloid Leukemia)-associated genes from protein-protein
interaction (PPI) network structure. The idea: genes that interact heavily
with known cancer genes are themselves more likely to be relevant to cancer,
so network position and connectivity to known AML genes are used as features
for classical ML models.

## Data

- [`9606.protein.aliases.v12.0.txt`](data/9606.protein.aliases.v12.0.txt) -> STRING protein ID to gene symbol aliases (human, taxon 9606)
- [`9606.protein.links.v12.0.txt.gz`](data/9606.protein.links.v12.0.txt.gz) ->  STRING protein-protein interaction edges with combined confidence scores
- [`Cosmic_CancerGeneCensus_v103_GRCh37.tsv`](data/Cosmic_CancerGeneCensus_v103_GRCh37.tsv) -> COSMIC Cancer Gene Census, used to label genes as AML-associated or not

Data files are not tracked in git. Download them from
[STRING](https://string-db.org/) and [COSMIC](https://cancer.sanger.ac.uk/census)
and place them under `data/`.

## Setup

```
python -m venv ppi-env
.\ppi-env\Scripts\Activate
pip install -r requirements.txt
```
