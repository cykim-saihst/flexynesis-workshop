# Flexynesis Workshop

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

랩 서버에서 실행 시 데이터 별도 다운로드 불필요:
```
/data01/storage/flexynesis_workshop/
├── brca_metabric_processed/
└── brca_mini/
```

## Run interactively

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/cykim-saihst/flexynesis-workshop/main?labpath=flexynesis_workshop.ipynb)
