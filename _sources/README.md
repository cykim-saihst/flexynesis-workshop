# Flexynesis Workshop

**Workshop site**: https://cykim-saihst.github.io/flexynesis-workshop/

| Page | Content |
|------|---------|
| [Introduction & Dataset](https://cykim-saihst.github.io/flexynesis-workshop/intro.html) | BRCA METABRIC dataset overview |
| [Model Architectures](https://cykim-saihst.github.io/flexynesis-workshop/models.html) | All 5 model architectures |
| [DataImporter Reference](https://cykim-saihst.github.io/flexynesis-workshop/data_guide.html) | Parameter guide |
| [HyperparameterTuning Reference](https://cykim-saihst.github.io/flexynesis-workshop/hpo_guide.html) | HPO parameter guide |
| [Evaluation Metrics](https://cykim-saihst.github.io/flexynesis-workshop/evaluation.html) | Metrics by task type |
| [Using Your Own Data](https://cykim-saihst.github.io/flexynesis-workshop/custom_data.html) | Custom data format guide |
| [Workshop Notebook](https://cykim-saihst.github.io/flexynesis-workshop/flexynesis_workshop.html) | Hands-on tutorial |

Deep learning multi-omics integration using [flexynesis](https://github.com/BIMSBbioinfo/flexynesis).

## Models covered

| Model | Task |
|-------|------|
| DirectPred | Classification / Survival |
| supervised_vae | Classification / Unsupervised |
| MultiTripletNetwork | Metric learning |
| GNN | Graph neural network (STRING DB) |
| CrossModalPred | Cross-modal prediction |

## Dataset

BRCA METABRIC — gex + cna + mut (mini: 200 train / 80 test)

Data is pre-installed on the lab server — no download needed:
```
/data01/storage/flexynesis_workshop/
├── brca_metabric_processed/
└── brca_mini/
```

## Setup

```bash
conda create -n flexy python=3.11 -y
conda activate flexy
pip install flexynesis jupyter
```

## Run

```bash
conda activate flexy
jupyter notebook /home/cykim/workshop/flexynesis_workshop.ipynb
```