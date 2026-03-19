# HyperparameterTuning Reference

Bayesian optimization(skopt)으로 hyperparameter search space를 탐색해 최적 파라미터를 찾는 클래스.

```python
tuner = flexynesis.main.HyperparameterTuning(
    dataset=train_dataset,
    model_class=flexynesis.models.DirectPred,
    target_variables=['CLAUDIN_SUBTYPE'],
    config_name='DirectPred',
    n_iter=10,
)
model, best_params = tuner.perform_tuning()
```

## Parameters

| 파라미터 | 기본값 | 필수 | 설명 |
|----------|--------|:----:|------|
| `dataset` | — | ✅ | `DataImporter.import_data()`로 얻은 train dataset |
| `model_class` | — | ✅ | 사용할 모델 클래스 (`flexynesis.models.DirectPred` 등) |
| `target_variables` | — | ✅ | 예측할 변수 리스트 (clin.csv 컬럼명, 예: `['CLAUDIN_SUBTYPE']`) |
| `config_name` | — | ✅ | HPO search space 이름 — model_class 이름과 동일하게 지정 |
| `n_iter` | `10` | | Bayesian HPO 반복 횟수. 높을수록 더 많은 조합 탐색 (실제 분석: 50+) |
| `config_path` | `None` | | 커스텀 YAML HPO 설정 파일 경로. `None`이면 내장 search space 사용 |
| `val_size` | `0.2` | | validation 비율 |
| `use_cv` | `False` | | `True`=K-fold CV로 HPO / `False`=단일 train-val split |
| `cv_splits` | `5` | | `use_cv=True`일 때 fold 수 |
| `use_loss_weighting` | `True` | | Kendall uncertainty weighting으로 멀티태스크 loss 자동 조정 |
| `early_stop_patience` | `-1` | | validation loss 개선 없을 때 조기 종료 epoch 수. `-1`=비활성화 |
| `plot_losses` | `False` | | 학습 중 loss 실시간 시각화 (Jupyter 인터랙티브 환경에서 유용) |
| `device_type` | `None` | | `None`=자동 감지 / `'gpu'` / `'cpu'` |
| `num_workers` | `2` | | DataLoader 병렬 worker 수. Jupyter에서 MultiTripletNetwork 사용 시 `0` 권장 |
| `surv_event_var` | `None` | | 생존 event 변수명 (예: `'OS_STATUS'`) |
| `surv_time_var` | `None` | | 생존 time 변수명 (예: `'OS_MONTHS'`) |
| `batch_variables` | `None` | | batch effect correction용 변수 리스트 |
| `gnn_conv_type` | `None` | | GNN conv 타입: `'GC'`(기본), `'GCN'`, `'GAT'`, `'SAGE'` |
| `input_layers` | `None` | | CrossModalPred 입력 omics 레이어 (예: `['gex']`) |
| `output_layers` | `None` | | CrossModalPred 출력 omics 레이어 (예: `['cna']`) |

## Return Values

```python
model, best_params = tuner.perform_tuning()
```

| 반환값 | 설명 |
|--------|------|
| `model` | 최적 파라미터로 학습된 모델 객체 |
| `best_params` | 최적 hyperparameter dict |

## Save / Load Model

```python
# 저장
torch.save(model, './models/DirectPred.pth')

# 불러오기
model = torch.load('./models/DirectPred.pth')
```

## Search Space

각 모델의 기본 search space는 `latent_dim`, `hidden_dim_factor`, `learning_rate`, `epochs`, `batch_size` 등을 포함. `config_path`로 YAML 파일을 지정하면 커스텀 search space를 사용할 수 있음.

```yaml
# custom_config.yaml 예시
DirectPred:
  latent_dim:
    type: Integer
    low: 16
    high: 128
  learning_rate:
    type: Real
    low: 0.0001
    high: 0.01
    prior: log-uniform
```
