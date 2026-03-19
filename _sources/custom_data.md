# Using Your Own Data

## Directory Structure

```
my_data/
├── train/
│   ├── clin.csv
│   ├── protein.csv       # protein expression (있으면)
│   ├── microbiome.csv    # microbiome abundance (있으면)
│   ├── genomic_pc.csv    # genotype PCs from PLINK --pca (있으면)
│   └── prs.csv           # multi-trait PRS (있으면)
└── test/
    ├── clin.csv
    ├── protein.csv
    ├── microbiome.csv
    ├── genomic_pc.csv
    └── prs.csv
```

---

## File Format

### omics 파일 (protein.csv, microbiome.csv, genotype.csv 등)

- **행 = feature** (단백질명, OTU/ASV ID, SNP rsID 등)
- **열 = sample ID**
- index(첫 번째 열)는 feature 이름

```
              Subject_001  Subject_002  Subject_003
IL6           2.31         1.54         3.21
TNF           0.87         2.10         1.43
VEGFA         4.52         3.87         2.98
```

### clin.csv

- **행 = sample**
- **열 = 표현형/임상변수**
- index(첫 번째 열)는 sample ID — omics 파일의 열 이름과 일치해야 함

```
              DISEASE     BMI    AGE   COHORT
Subject_001   case        27.3   45    batch1
Subject_002   control     22.1   38    batch2
Subject_003   case        31.0   52    batch1
```

> sample ID가 omics와 clin 간에 일치하지 않으면 DataImporter가 자동으로 교집합만 사용.

---

## Data Type별 특징

| 데이터 | 특징 | 전처리 권장 |
|--------|------|-------------|
| Protein expression | 연속값, 정규분포에 가까움 | log2 transform 또는 z-score |
| Microbiome abundance | 희소(sparse), 합계=1 (relative) | CLR transform 또는 log(x+1) |
| Genotype PCs | PLINK `--pca`로 추출한 주성분 | 그대로 사용, 집단 구조 보정 겸 활용 |
| Multi-trait PRS | 표현형별 PRS를 행(feature)으로 쌓은 matrix | 그대로 사용 |

`log_transform=True` 옵션으로 DataImporter가 log1p를 자동 적용할 수 있음.

Raw genotype(VCF/PLINK)은 아래와 같이 요약된 형태로 변환해서 사용하는게 좋을 듯.

```
# genotype_pc.csv (PLINK --pca 결과 변환)
              Subject_001  Subject_002  Subject_003
PC1           0.23         -0.11         0.08
PC2           0.05          0.31        -0.14
...
PC20          0.12         -0.04         0.21

# prs.csv (trait별 PRS)
              Subject_001  Subject_002  Subject_003
PRS_T2D       0.87          1.23         0.54
PRS_BMI      -0.32          0.91        -0.10
PRS_CAD       1.05         -0.23         0.77
```

---

## Train/Test Split

flexynesis는 train/test 폴더를 직접 받으므로 미리 분할해야 함.

```python
import pandas as pd
from sklearn.model_selection import train_test_split

clin = pd.read_csv('clin.csv', index_col=0)
train_idx, test_idx = train_test_split(
    clin.index, test_size=0.2, random_state=42,
    stratify=clin['DISEASE']   # 분류 태스크면 stratify 권장
)
```

---

## Variable Type Detection

DataImporter는 clin.csv 컬럼을 자동으로 타입 분류.

| 조건 | 인식 타입 | 처리 |
|------|-----------|------|
| 문자열 또는 unique 값 적음 | categorical | 정수 인코딩 |
| 숫자형, unique 값 많음 | numerical | 그대로 사용 |

분류 태스크 변수는 문자열이거나 카테고리 수가 적어야 함.

---

## Minimum Sample Size

| 모델 | 최소 권장 train 샘플 |
|------|---------------------|
| DirectPred | 50+ |
| supervised_vae | 50+ |
| MultiTripletNetwork | 클래스당 **2개 이상** 필수 |
| GNN | 50+ |
| CrossModalPred | 50+ |

MultiTripletNetwork는 `target_variables[0]` 기준으로 triplet을 구성하므로 **모든 클래스에 최소 2개** 샘플이 있어야 함.

---

## Single Omics

단일 데이터 레이어에서도 동일하게 사용 가능. `data_types`에 하나만 지정.

```python
data_importer = flexynesis.data.DataImporter(
    path=PATH,
    data_types=['protein'],   # 단일 레이어
    ...
)
```

임베딩 추출도 동일하게 작동:

```python
E = model.transform(test_dataset)   # shape: (n_samples, latent_dim)
```

임베딩 활용 예시:
- UMAP/PCA로 샘플 분포 시각화
- 다운스트림 클러스터링 (Louvain, k-means 등)
- 다른 모델의 feature로 활용

> GNN은 단일 레이어(주로 gex)만 지원. CrossModalPred는 `input_layers`와 `output_layers`가 달라야 하므로 최소 2개 레이어가 필요.

---

## Checklist

- [ ] train/test 폴더 분리
- [ ] omics 파일: 행=feature, 열=sample
- [ ] clin.csv: 행=sample, sample ID가 omics 열 이름과 일치
- [ ] 분류 변수: 문자열 또는 소수 카테고리
- [ ] MultiTripletNetwork 사용 시: 모든 클래스 2개 이상
- [ ] `data_types`에 실제 파일명 지정 (확장자 제외)
