# Evaluation Metrics

## evaluate_wrapper

모델 예측 결과를 평가하는 함수. 변수 타입에 따라 자동으로 적절한 지표를 계산.

```python
metrics = flexynesis.utils.evaluate_wrapper(
    method='DirectPred',
    y_pred_dict=model.predict(test_dataset),
    dataset=test_dataset,
    surv_event_var=None,   # 생존분석일 때만
    surv_time_var=None,    # 생존분석일 때만
)
```

---

## Classification (categorical)

| 지표 | 설명 | 범위 | 좋은 값 |
|------|------|------|---------|
| `balanced_acc` | 클래스 불균형 보정 accuracy | 0 ~ 1 | 높을수록 |
| `f1_score` | Precision/Recall 조화평균 (weighted) | 0 ~ 1 | 높을수록 |
| `kappa` | 우연에 의한 일치를 보정한 agreement 지표 | -1 ~ 1 | 0.6+ 양호 |
| `average_auroc` | 각 클래스별 ROC-AUC의 weighted 평균 | 0 ~ 1 | 0.7+ 양호 |
| `average_aupr` | 각 클래스별 PR-AUC의 weighted 평균 | 0 ~ 1 | 높을수록 (클래스 불균형 시 AUROC보다 informative) |

**언제 어떤 걸 볼까?**
- 클래스가 균형잡혀 있으면 → `balanced_acc`, `f1_score`
- 클래스 불균형 있으면 → `average_aupr` 우선
- 전반적인 discriminative 능력 → `average_auroc`

---

## Regression (numerical)

| 지표 | 설명 | 범위 | 좋은 값 |
|------|------|------|---------|
| `mse` | Mean Squared Error | 0 ~ ∞ | 낮을수록 |
| `r2` | 분산 설명 비율 (R²) | -∞ ~ 1 | 1에 가까울수록 |
| `pearson_corr` | 예측값-실제값 선형 상관계수 | -1 ~ 1 | 높을수록 |

---

## Survival Analysis

| 지표 | 설명 | 범위 | 좋은 값 |
|------|------|------|---------|
| `cindex` | Concordance Index — 사건 발생 순서를 얼마나 잘 예측하는지 | 0 ~ 1 | 0.5=랜덤, 0.7+ 양호 |

`surv_event_var`와 `surv_time_var`를 지정해야 계산됨.

---

## Unsupervised

`supervised_vae`를 `target_variables=[]`로 학습 후 클러스터링 결과를 알려진 레이블과 비교할 때 사용.

| 지표 | 설명 | 범위 | 좋은 값 |
|------|------|------|---------|
| `AMI` | Adjusted Mutual Information — 클러스터와 레이블 간 정보 일치도 | 0 ~ 1 | 높을수록 |
| `ARI` | Adjusted Rand Index — 클러스터 쌍 일치율 (우연 보정) | -1 ~ 1 | 높을수록 |

> AMI/ARI는 `evaluate_wrapper`가 아닌 클러스터링 후 직접 계산.

---

## Model Comparison Tips

여러 모델 결과를 모아 비교할 때:

```python
all_metrics = []
all_metrics.append(metrics_dp)
all_metrics.append(metrics_vae)
# ...

import pandas as pd
df = pd.concat(all_metrics).set_index('method')
df[['balanced_acc', 'average_auroc', 'f1_score']]
```
