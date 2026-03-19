# 모델 설명

flexynesis에서 제공하는 5가지 모델 아키텍처를 소개합니다. 모든 모델은 multi-omics 입력을 받아 각 레이어별로 개별 인코더를 통해 latent representation을 학습합니다.

---

## DirectPred

**용도**: 분류 / 회귀 / 생존분석

```
[gex encoder] ─┐
[cna encoder] ─┼─→ concat → fusion linear → supervisor MLP → prediction
[mut encoder] ─┘
```

가장 단순한 구조로, 각 omics 레이어를 MLP로 인코딩한 뒤 concat하여 예측합니다.

- **분류**: Cross-entropy loss
- **생존분석**: Cox Proportional Hazards loss → C-index로 평가
- 빠른 학습, 해석이 용이
- Integrated Gradients로 feature importance 계산 가능

---

## supervised_vae

**용도**: 분류 / 비지도 학습

```
[gex encoder] ─┐                    ┌→ [gex decoder]
[cna encoder] ─┼─→ FC_mean      ─→ z ┼→ [cna decoder]
[mut encoder] ─┘   FC_log_var        └→ supervisor MLP → prediction
```

VAE(Variational Autoencoder) 기반으로 latent space를 정규분포로 제약하면서 예측을 동시에 학습합니다.

- **Loss**: MMD (latent regularization) + NLL (reconstruction) + CE (supervisor)
- `target_variables=[]` 로 설정하면 순수 비지도 모드 — latent space에서 클러스터링 가능
- 비지도 결과는 Louvain clustering + UMAP으로 시각화

---

## MultiTripletNetwork

**용도**: Metric learning (임베딩 학습)

```
anchor  ─→ encoder ─→ embedding_a ─┐
positive ─→ encoder ─→ embedding_p ─┼─→ Triplet loss
negative ─→ encoder ─→ embedding_n ─┘
                                    └─→ supervisor MLP → classification
```

같은 클래스 샘플(positive)과 다른 클래스 샘플(negative)을 triplet으로 구성해 latent space를 discriminative하게 학습합니다.

- **Loss**: Triplet loss + CE (supervisor head)
- 학습 후 UMAP으로 시각화하면 클래스별 클러스터가 명확하게 분리됨
- `target_variables[0]`이 반드시 categorical이어야 함 (triplet 구성 기준)
- 데이터로더가 배치당 anchor/positive/negative 3개 샘플을 로드하므로 다른 모델보다 학습이 느림

---

## GNN

**용도**: 분류 / 회귀 (그래프 기반)

```
STRING DB PPI network
      ↓
gene nodes + expression features → GCN layers → pooling → supervisor MLP
```

STRING DB의 단백질 상호작용 네트워크(PPI)를 그래프로 활용해 유전자 간 관계를 학습에 반영합니다.

- gex 단일 모달리티만 사용 (gene → protein node로 매핑)
- **Graph convolution 옵션**: `GC` (기본), `GCN`, `GAT`, `SAGE`
- STRING DB는 첫 실행 시 자동 다운로드, 이후 `~/.cache/flexynesis/STRING/`에 캐시
- 유전자 발현뿐 아니라 pathway/network 구조 정보를 활용하고 싶을 때 유용

---

## CrossModalPred

**용도**: Cross-modal prediction (한 omics → 다른 omics 예측)

```
input_layers  → VAE encoders → shared latent z → decoders → output_layers
                                                → supervisor MLP → classification
```

특정 omics 레이어를 입력으로 다른 omics 레이어를 재구성(imputation)하면서 동시에 예측 태스크를 수행합니다.

- `input_layers`: 학습 시 제공되는 omics (예: `['gex']`)
- `output_layers`: 예측/재구성 대상 omics (예: `['cna']`)
- 데이터가 누락된 omics를 imputation하거나, omics 간 관계를 학습하는 데 유용
- 추론 시 `input_layers`만 있어도 `output_layers` 예측 가능

---

## 비교 요약

| 모델 | 구조 | 강점 | 학습 속도 |
|------|------|------|----------|
| DirectPred | MLP | 단순, 빠름 | ★★★★★ |
| supervised_vae | VAE | latent space 활용, 비지도 가능 | ★★★★ |
| MultiTripletNetwork | Triplet + MLP | 클러스터 분리 우수 | ★★ |
| GNN | Graph conv | 네트워크 구조 반영 | ★★★ |
| CrossModalPred | VAE + cross-modal | omics imputation | ★★★ |
