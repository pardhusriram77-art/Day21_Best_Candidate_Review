# Day 21 — Best Candidate Checkpoint

## Scope
Review of forecasting baselines (Day 16), candidate comparison (Day 17), controlled Random Forest tuning (Day 19), and feature/validation robustness study (Day 20). Metrics below are transcribed from the corresponding public GitHub CSV/JSON artifacts.

## Current evidence
- Day 16 test: Linear Regression MAE 1.4852 / RMSE 1.8990; Random Forest 1.5097 / 1.9079; Persistence 1.9949 / 2.6342.
- Day 17 test: Random Forest MAE 1.4973 / RMSE 1.9100; Gradient Boosting MAE 1.5007 / RMSE 1.9200. Gradient Boosting was slightly better on validation (RMSE 1.6011 vs 1.6116) but slightly worse on test (1.9200 vs 1.9100), suggesting the validation advantage did not carry over.
- Day 19: selected RandomForestRegressor configuration was 150 trees, max_depth=12, min_samples_leaf=4, max_features=0.8. Validation MAE 1.2927 / RMSE 1.6091. Against the Day 17 RF reference in the same Day 19 experiment log (RMSE 1.6236), the reduction is about 0.0146 (0.90%). Test was reserved and not used for tuning.
- Day 20: pH-only variant achieved validation MAE 1.2821 / RMSE 1.5940, compared with all nine sensor features MAE 1.2944 / RMSE 1.6170. RMSE improved by 0.0230 (1.42%) in this ablation. This is evidence that adding all available columns did not help this particular synthetic next-row pH task.
- Day 20 validation windows were not equally easy: RMSE 1.4613, 1.7070, and 1.6042 across windows 1–3. Window 2 is the representative difficult period.

## Candidate checkpoint — provisional, not final approval
Keep Random Forest as the primary candidate family because it is competitive, the tuning process is documented, and the model is saved. Carry two feature variants into Week 4: (A) pH-only RF and (B) full-feature RF. Re-run them on the same fixed chronological split and same preprocessing before making a final choice. Do not claim pH-only is globally superior based on one synthetic validation split.

## Representative successes and failures
**Success:** pH-only RF had the lowest Day 20 validation RMSE (1.5940) and lower fit time (0.328 s) than the full-feature variant (0.752 s).
**Failure / warning:** Gradient Boosting's validation RMSE advantage over RF did not persist on test; its test RMSE was 1.9200 vs RF 1.9100. Day 20 window 2 was materially harder than window 1 (RMSE 1.7070 vs 1.4613).
**Anomaly evaluation limitation:** The dataset has no labelled anomaly ground truth, so anomaly precision/recall/F1 and verified false-positive/false-negative examples cannot be claimed. Treat anomaly outputs as exploratory flags only.

## Remaining weaknesses
1. The source is a static water-potability table; timestamps were synthetically assigned.
2. Synthetic chronology does not prove temporal dependence or real underwater forecasting ability.
3. The source lacks dissolved oxygen and pressure, important underwater monitoring variables.
4. Day 19 and Day 20 validation row counts/splits differ (411 vs 416); their metrics are not a strict head-to-head comparison.
5. The Day 20 best pH-only variant should be re-evaluated against the full feature model using identical split boundaries and pipeline.
6. No labelled anomalies; no defensible anomaly detection classification metrics.

## Proposed Week 4 direction — request mentor approval
1. Freeze the current work as a reproducible exploratory pipeline checkpoint.
2. Obtain or approve a genuine time-stamped underwater/water-quality sensor dataset with pH, temperature, dissolved oxygen, pressure and sensor-quality metadata.
3. Confirm target and forecast horizon with mentor; retain next-row pH only as a demonstration target until then.
4. On one fixed chronological train/validation/test split, compare persistence, linear regression and tuned RF; use validation for selection and test once at the end.
5. For anomaly detection, keep statistical rules + Isolation Forest as exploratory methods until labelled events or expert-reviewed anomaly windows are available.
6. Do not add LSTM/Transformer/other sequence complexity unless genuine, sufficiently long and regular time-series data justify it.

## Mentor decisions requested
- Approve RF as the primary candidate family, with pH-only and full-feature variants retained pending same-split comparison?
- Approve acquiring genuine sensor time-series before sequence-model work?
- Confirm the sensor variables, target, forecast horizon, and anomaly validation protocol?

## Reproducibility note
Metric sources:
- Day 16 `forecast_metrics.csv`
- Day 17 `advanced_model_metrics.csv`, `candidate_model_summary.csv`
- Day 19 `Day19_Experiment_Log.csv`, `Day19_Best_Configuration.json`
- Day 20 `Day20_Ablation_Results.csv`, `Day20_Research_Insight.md`

All numeric values are exploratory results on the synthetic temporal workflow, not field-validated sensor performance.
