# Nano (N) YOLO Segmentation Benchmark — CCTV600

## 1. Experiment Status

COMPLETE for accuracy and dedicated clean timing. Each of 3 models has 600 accepted unique image packets. Evaluation includes 769 eligible person boxes. Dataset-quality and interpretation limitations remain documented below.

## 2. Models Tested

| Family | Checkpoint | Parameters | GFLOPs estimate | Weights MB |
| --- | --- | --- | --- | --- |
| YOLO26 | yolo26n-seg.pt | 3,126,280 | 10.630 | 6.720 |
| YOLO11 | yolo11n-seg.pt | 2,876,848 | 9.959 | 6.183 |
| YOLOv8 | yolov8n-seg.pt | 3,409,968 | 12.141 | 7.072 |

## 3. Protocol Compatibility

| Item | Status and evidence |
| --- | --- |
| Dataset | PASS · 600 exact IDs; dataset membership SHA in frozen config |
| Evaluator | PASS · GT-first bbox matching and shared ignore union |
| Preprocessing | PASS · frozen runtime source binding |
| Input size | 1×3×640×640, centered letterbox |
| Precision | FP32; no AMP/TF32/extra TTA |
| Thresholds | AP floor .001, fixed conf .25, match IoU .50, NMS IoU .70 |
| maxDet | Model 1000 / evaluator 200; cap parity PASS |
| Timing | 3×100 clean observations, warmup 10 each round; sample order verified |
| Environment | Python 3.12.3 / torch 2.14.0+cu130 / Ultralytics 8.4.160 / Tesla T4 |

Compatibility applies within this CCTV600 experiment. MOTS20 supplies the reporting structure and reused detector runtime. CCTV600 uses visible box GT, its ≥50 px eligibility and coarse rectangle-ignore policy. MOTS20 uses person silhouettes and mask matching.

## 4. Overall Results

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 40.817 | 45.176 | 51.105 | 13.818 | 69.345 | 14.421 | 1344.222 |
| yolo11n-seg.pt | 39.493 | 44.019 | 48.375 | 11.861 | 63.587 | 15.727 | 804.799 |
| yolov8n-seg.pt | 39.903 | 44.693 | 52.796 | 9.923 | 79.317 | 12.608 | 1154.993 |

| Model | Precision % | Recall % | F1 % | TP | FP | FN | Ignored | TP-only box IoU |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 77.822 | 51.105 | 61.695 | 393 | 112 | 376 | 2 | 0.841390 |
| yolo11n-seg.pt | 77.339 | 48.375 | 59.520 | 372 | 109 | 397 | 1 | 0.842342 |
| yolov8n-seg.pt | 78.835 | 52.796 | 63.240 | 406 | 109 | 363 | 4 | 0.831892 |

AP/P/R/F1 columns use percentages. TP-only box IoU is a fraction. Counts use confidence ≥0.25 and box IoU ≥0.50. AP uses 10 IoU thresholds, 101 recall points and maxDet 200. Source-precision values remain in CSV.

## 5. Tier Winners

| ด้าน | โมเดล | ค่าที่วัด |
| --- | --- | --- |
| Box AP | yolo26n-seg.pt | 40.817 % |
| AP75 | yolo26n-seg.pt | 45.176 % |
| Recall | yolov8n-seg.pt | 52.796 % |
| Forward time | yolov8n-seg.pt | 9.923 ms/ภาพ |
| Adapter time | yolo11n-seg.pt | 63.587 ms/ภาพ |
| Adapter FPS | yolo11n-seg.pt | 15.727 ภาพ/วินาที |
| Allocated VRAM | yolo11n-seg.pt | 804.799 MiB |

## 6. Key Findings

The highest Box AP is yolo26n-seg.pt (40.817%), 0.914 percentage points above the runner-up. The shortest adapter mean belongs to yolo11n-seg.pt (63.587 ms/image). Differences are descriptive; no significance or architecture-causality claim is made.

## 7. Per-group Observations

| Model | Day AP % | Night AP % | R 50–99 px % | R 100–199 px % | R ≥200 px % |
| --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 30.775 | 48.934 | 17.978 | 48.246 | 78.715 |
| yolo11n-seg.pt | 28.288 | 48.634 | 14.045 | 46.784 | 75.100 |
| yolov8n-seg.pt | 29.323 | 48.163 | 15.169 | 53.216 | 79.116 |

Day/night use existing metadata and contain different scenes and people. Pooled AP is read directly from the canonical full-population result. Size-band recall conditions on GT size and the original matching; precision/AP by size were not computed under a new policy.

## 8. Efficiency and Resource Observations

| Model | Pre mean ms | Post mean ms | Adapter P50 ms | Adapter P95 ms | Adapter std ms | Reserved MiB |
| --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 1.953 | 53.574 | 54.262 | 150.075 | 56.420 | 1562.000 |
| yolo11n-seg.pt | 1.962 | 49.764 | 51.701 | 161.851 | 44.890 | 1016.000 |
| yolov8n-seg.pt | 2.069 | 67.324 | 65.365 | 197.979 | 57.091 | 1616.000 |

Means, median, P95 and population standard deviation use all 300 accepted observations. Adapter FPS excludes disk/decode, RLE, packet writing and load/warmup. Native postprocessing substage timers are inclusive subsets. Allocated/reserved peaks include the resident model and take the maximum across the three accepted rounds.

## 9. Warnings and Anomalies

The original study ended with incomplete Nano timing. A linked segment supplied four accepted clean rounds, retaining the original contaminated YOLO11n round and original completion unchanged. Accuracy was not rerun. Detailed accepted round paths and hashes are in the common result index. The owned web worker was restored after authorized temporary suspension.

## 10. Limitations

GT inventory relies on the dataset creator's 600-image acceptance. Independent all-person inventory adjudication remains unavailable. No full-person silhouette GT, mask AP, tracking or parsing/color score is reported here. TP-only IoU excludes missed people. Different pretrained capacities and training histories confound architecture attribution. Related frames and absent uncertainty analysis limit generalization.

## 11. Reproducibility and Source Artifacts

[Canonical tier table](metrics/TIER_RESULTS.csv), [timing statistics](metrics/TIMING_SUMMARY.csv), [shared source/protocol/checkpoint bindings](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/RESULT_INDEX.json), [closed-run checks](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json), [metric definitions](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md) and [presentation revision evidence](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/PRESENTATION_REVISION.json).

## 12. Relation to Full Scaling Study

[Largest (X/E)](https://github.com/folklazy/YOLO_Large_Seg_CCTV600_Benchmark) · [Second-largest (L/C)](https://github.com/folklazy/YOLO_Second_Largest_Seg_CCTV600_Benchmark) · [Medium (M)](https://github.com/folklazy/YOLO_Medium_Seg_CCTV600_Benchmark) · [Small (S)](https://github.com/folklazy/YOLO_Small_Seg_CCTV600_Benchmark) · [Nano (N)](https://github.com/folklazy/YOLO_Nano_Seg_CCTV600_Benchmark) · [Master](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study)

## Qualitative Analysis

[PRESENTATION_SUMMARY_TH.md](PRESENTATION_SUMMARY_TH.md) discusses four diagnostic cases from the original fixed16. Full frames, identical ROIs and per-GT matching are retained in the private local gallery. Only aggregate charts and de-identified qualitative observations are published.
