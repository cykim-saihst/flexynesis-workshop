# Flexynesis Workshop

**Workshop site**: https://cykim-saihst.github.io/flexynesis-workshop/

| 페이지 | 내용 |
|--------|------|
| [소개 + 데이터](https://cykim-saihst.github.io/flexynesis-workshop/intro.html) | BRCA METABRIC 데이터 설명 |
| [모델 설명](https://cykim-saihst.github.io/flexynesis-workshop/models.html) | 5가지 모델 아키텍처 상세 |
| [DataImporter 레퍼런스](https://cykim-saihst.github.io/flexynesis-workshop/data_guide.html) | 파라미터 가이드 |
| [노트북](https://cykim-saihst.github.io/flexynesis-workshop/flexynesis_workshop.html) | 실습 코드 |

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
conda create -n flexy python=3.11 -y
conda activate flexy
pip install flexynesis jupyter
```

### 실행

```bash
conda activate flexy
jupyter notebook flexynesis_workshop.ipynb
```