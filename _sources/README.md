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

## 실행 방법

### 환경 설정

```bash
git clone https://github.com/cykim-saihst/flexynesis-workshop.git
cd flexynesis-workshop
conda env create -f environment.yml
```

### 실행

```bash
conda activate flexy
jupyter notebook flexynesis_workshop.ipynb
```
