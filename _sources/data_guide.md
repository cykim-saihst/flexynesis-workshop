# DataImporter Reference

> **필수 입력**: `path`, `data_types` 

```python
data_importer = flexynesis.data.DataImporter(
    path=PATH,
    data_types=['gex', 'cna', 'mut'],
    ...
)
train_dataset, test_dataset = data_importer.import_data()
```

## Parameters

| 파라미터 | 기본값 | 필수 | 설명 |
|----------|--------|:----:|------|
| `path` | — | ✅ | train/test 폴더가 있는 상위 디렉토리 경로 |
| `data_types` | — | ✅ | 사용할 omics 레이어 리스트 (폴더 내 파일명 기준, 예: `['gex', 'cna']`) |
| `concatenate` | `False` | | `False`=intermediate fusion (레이어별 개별 encoder) / `True`=early fusion (concat 후 single encoder) |
| `top_percentile` | `20` | | Laplacian score 상위 N% feature만 유지 |
| `min_features` | `None` | | percentile 적용 후 최소 보장 feature 수 (부족하면 하위 feature로 보충) |
| `correlation_threshold` | `0.9` | | 이 이상 상관된 feature 쌍에서 하나 제거 (낮출수록 더 공격적으로 제거) |
| `variance_threshold` | `0.01` | | 분산 하위 N% feature 제거 (높일수록 더 많이 제거, 0~1 사이) |
| `na_threshold` | `0.1` | | feature의 NA 비율이 이 이상이면 해당 feature 제거 |
| `log_transform` | `False` | | `True`면 전처리 후 log1p 변환 (raw count 데이터에 유용) |
| `covariates` | `None` | | batch correction용 공변량 변수명 리스트 (예: `['COHORT']`, clin.csv 컬럼 기준) |
| `downsample` | `0` | | `0`=전체 샘플 / N>0이면 N개로 랜덤 다운샘플 |
| `restrict_to_features` | `None` | | 특정 feature만 사용 — **텍스트 파일 경로**를 지정 |

## restrict_to_features File Format

feature selection보다 먼저 적용. 이 리스트 안에서만 Laplacian score를 계산.

```
# gene_panel.txt
BRCA1
BRCA2
TP53
ERBB2
ESR1
```

```python
data_importer = flexynesis.data.DataImporter(
    path=PATH,
    data_types=['gex'],
    restrict_to_features='./gene_panel.txt',
)
```

## Directory Structure

```
PATH/
├── train/
│   ├── clin.csv      # 샘플 × 임상변수 (index=sample ID)
│   ├── gex.csv       # feature × 샘플
│   ├── cna.csv       # feature × 샘플
│   └── mut.csv       # feature × 샘플
└── test/
    ├── clin.csv
    ├── gex.csv
    ├── cna.csv
    └── mut.csv
```

> omics 파일은 **행=feature, 열=sample** 형식.
