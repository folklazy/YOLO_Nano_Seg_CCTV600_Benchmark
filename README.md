# Nano (N) YOLO Segmentation Benchmark — CCTV600

## ภาพรวม

ประเมิน pretrained segmentation checkpoints กลุ่ม Nano (N) บน CCTV Human Parsing 600 ด้วยคะแนนกรอบบุคคล ทุกรุ่นใช้ input 640, FP32, batch 1 และ population เดียวกันบน Tesla T4

## สถานะ

**COMPLETE** คุณภาพครบ 3/3 โมเดล ตัวละ 600 ภาพ เวลาครบ 3 clean rounds ต่อโมเดล อ่านแหล่งผลและประวัติ timing continuation จาก [สถานะรวม](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json)

## ผลหลัก

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 40.817 | 45.176 | 51.105 | 13.818 | 69.345 | 14.421 | 1344.222 |
| yolo11n-seg.pt | 39.493 | 44.019 | 48.375 | 11.861 | 63.587 | 15.727 | 804.799 |
| yolov8n-seg.pt | 39.903 | 44.693 | 52.796 | 9.923 | 79.317 | 12.608 | 1154.993 |

Box AP/P/R/F1 แสดงเป็นเปอร์เซ็นต์ Forward และ adapter เป็น ms/ภาพ, FPS = 1000 / mean adapter ms และ VRAM เป็น peak allocated MiB

## รายงาน

| เอกสาร | เนื้อหา |
| --- | --- |
| [RESULTS_SUMMARY_TH.md](RESULTS_SUMMARY_TH.md) | คะแนน ผู้ชนะรายด้าน บทบาทและข้อแลกเปลี่ยนของแต่ละโมเดล |
| [PRESENTATION_SUMMARY_TH.md](PRESENTATION_SUMMARY_TH.md) | กราฟและหลักฐานข้อผิดพลาดจาก fixed sample เดิม |
| [REPORT.md](REPORT.md) | Models, compatibility, ผลเต็ม, timing, source และข้อจำกัด |
| [EXPERIMENT_PROTOCOL.md](EXPERIMENT_PROTOCOL.md) | เงื่อนไขที่ใช้รัน |

## ภาพรวมคุณภาพ

![Box AP](metrics/plots/01_box_ap50_95.png)

## การนำทางทั้ง study

[Largest (X/E)](https://github.com/folklazy/YOLO_Large_Seg_CCTV600_Benchmark) · [Second-largest (L/C)](https://github.com/folklazy/YOLO_Second_Largest_Seg_CCTV600_Benchmark) · [Medium (M)](https://github.com/folklazy/YOLO_Medium_Seg_CCTV600_Benchmark) · [Small (S)](https://github.com/folklazy/YOLO_Small_Seg_CCTV600_Benchmark) · [Nano (N)](https://github.com/folklazy/YOLO_Nano_Seg_CCTV600_Benchmark) · [Master](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study)

## ตรวจย้อนกลับ

[ผลและ timings](metrics/) · [Config](configs/benchmark.json) · [Bindings](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/RESULT_INDEX.json) · [คู่มือตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md)

ภาพ CCTV และ per-frame records เก็บ private ชุดหลักฐานภาพของรอบตรวจเอกสารอยู่ใน Master ที่ `outputs/presentation-20261116T094620918214Z/cases/nano/index.html` ใช้คู่กับรายงานภาพ โดยเก็บภาพเต็มและ ROI พิกัดเดียวกันครบทุกโมเดล

คำสั่งจาก workspace สำหรับตรวจสถานะปิดรอบ

```bash
.venv/bin/python YOLO_Instance_Segmentation_CCTV600_Scaling_Study/analysis/closeout.py --status
```
