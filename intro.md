# Flexynesis Workshop

[flexynesis](https://github.com/BIMSBbioinfo/flexynesis)는 multi-omics 데이터를 위한 딥러닝 통합 프레임워크. 여러 omics 레이어를 동시에 학습해 분류, 생존분석, 비지도 학습 등 다양한 태스크를 수행.

---

## Dataset: BRCA METABRIC

**METABRIC** (Molecular Taxonomy of Breast Cancer International Consortium): 유방암 환자의 분자 분류를 위한 대규모 코호트 데이터셋.

| 항목 | 내용 |
|------|------|
| 환자 수 | ~1,900명 |
| 데이터 분할 | train 1,306 / test 562 |
| Omics | gex (gene expression), cna (copy number alteration), mut (mutation) |
| 임상 정보 | CLAUDIN_SUBTYPE, OS_STATUS, OS_MONTHS 등 |

### Omics Layers

**gex** (Gene Expression)
- RNA-seq 기반 유전자 발현량
- ~20,000 genes
- 세포 상태, 신호전달 경로 반영

**cna** (Copy Number Alteration)
- 염색체 특정 구간의 증폭/결실
- ~20,000 loci
- 암 드라이버 유전자 변화 반영

**mut** (Somatic Mutation)
- 체세포 돌연변이 정보 (0/1 binary)
- ~7,000 genes
- 희소(sparse)한 특성

### Clinical Labels

**CLAUDIN_SUBTYPE** (분류 태스크)

| 서브타입 | 특징 |
|----------|------|
| LumA | Luminal A — ER+, 저등급, 예후 양호 |
| LumB | Luminal B — ER+, 고등급, 예후 중간 |
| Her2 | HER2 과발현, 표적치료 가능 |
| Basal | Triple-negative, 예후 불량 |
| claudin-low | 간엽 특성, 면역 침윤 높음 |
| Normal | Normal-like |
| NC | 미분류 |

**OS_STATUS / OS_MONTHS** (생존분석 태스크)
- OS_STATUS: 사망 여부 (0=생존, 1=사망)
- OS_MONTHS: 추적관찰 기간 (개월)

### Mini Dataset

전체 데이터는 학습에 수 시간이 걸리므로 워크샵용 미니 데이터셋을 제공.

- **Stratified sampling** — CLAUDIN_SUBTYPE 기준으로 비율 유지
- train 200 / test 80
- 경로: `/data01/storage/flexynesis_workshop/brca_mini/`

---

## About This Workshop

이 워크샵은 **하나의 데이터셋(BRCA METABRIC)으로 flexynesis의 모든 모델 아키텍처를 빠르게 체험**하기 위해 구성한 튜토리얼.

실제 연구에서는 더 다양한 데이터와 설정으로 활용할 수 있음:

| 확장 가능한 방향 | 예시 |
|-----------------|------|
| **다양한 암종** | TCGA pan-cancer, GEO 공개 데이터 |
| **다양한 omics 조합** | gex + methylation, proteomics + transcriptomics |
| **다양한 태스크** | 약물 반응 예측, 세포 타입 분류, 멀티-레이블 예측 |
| **자체 데이터** | 연구실 보유 cohort — [Using Your Own Data](custom_data.md) 참고 |
| **HPO 본격 탐색** | `n_iter=50+`, `use_cv=True` |
| **Feature importance** | Integrated Gradients로 핵심 유전자 발굴 |

더 다양한 사용 예시는 공식 튜토리얼을 참고:

| 튜토리얼 | 내용 |
|----------|------|
| [Modeling Breast Cancer Subtypes](https://github.com/BIMSBbioinfo/flexynesis/blob/main/examples/tutorials/brca_subtypes.ipynb) | BRCA 전체 데이터로 서브타입 분류 (본 워크샵 기반 데이터) |
| [Survival Markers in LGG/GBM](https://github.com/BIMSBbioinfo/flexynesis/blob/main/examples/tutorials/survival_subtypes_LGG_GBM.ipynb) | 뇌종양 생존 마커 발굴 |
| [GNN with Custom Networks](https://github.com/BIMSBbioinfo/flexynesis/blob/main/examples/tutorials/gnn_custom_networks.ipynb) | 커스텀 gene co-expression network로 GNN 학습 |
| [Unsupervised Single Cell](https://github.com/BIMSBbioinfo/flexynesis/blob/main/examples/tutorials/unsupervised_analysis_single_cell.ipynb) | 단일세포 데이터 비지도 학습 |
| [cBioPortal Data](https://github.com/BIMSBbioinfo/flexynesis/blob/main/examples/tutorials/cbioportal.ipynb) | cBioPortal 공개 데이터 직접 활용 |