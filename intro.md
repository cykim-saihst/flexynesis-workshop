# Flexynesis Workshop

[flexynesis](https://github.com/BIMSBbioinfo/flexynesis)는 multi-omics 데이터를 위한 딥러닝 통합 프레임워크입니다. 여러 omics 레이어를 동시에 학습해 분류, 생존분석, 비지도 학습 등 다양한 태스크를 수행할 수 있습니다.

---

## 데이터: BRCA METABRIC

**METABRIC** (Molecular Taxonomy of Breast Cancer International Consortium)은 유방암 환자의 분자 분류를 위한 대규모 코호트 데이터셋입니다.

| 항목 | 내용 |
|------|------|
| 환자 수 | ~1,900명 |
| 데이터 분할 | train 1,306 / test 562 |
| Omics | gex (gene expression), cna (copy number alteration), mut (mutation) |
| 임상 정보 | CLAUDIN_SUBTYPE, OS_STATUS, OS_MONTHS 등 |

### Omics 레이어

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

### 임상 레이블

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

### 미니 데이터셋

전체 데이터는 학습에 수 시간이 걸리므로 워크샵용 미니 데이터셋을 제공합니다.

- **Stratified sampling** — CLAUDIN_SUBTYPE 기준으로 비율 유지
- train 200 / test 80
- 경로: `/data01/storage/flexynesis_workshop/brca_mini/`
