# ผลประเมิน YOLO กลุ่ม nano บน CCTV600

Run `cctv600-20261116T073208561699Z` · ตรวจปิดรอบเมื่อ 2026-11-16T16:13:20+07:00 (เวลาไทย)

คุณภาพครบ 3/3 โมเดล ตัวละ 600 ภาพ GT เข้าเกณฑ์ 769 คน คะแนน Box AP สูงสุดในกลุ่มคือ **yolo26n-seg.pt 40.817%**

## คุณภาพกรอบบุคคล

| Model | AP % | AP50 % | AP75 % | P % | R % | F1 % | TP | FP | FN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 40.817 | 61.914 | 45.176 | 77.822 | 51.105 | 61.695 | 393 | 112 | 376 |
| yolo11n-seg.pt | 39.493 | 59.325 | 44.019 | 77.339 | 48.375 | 59.520 | 372 | 109 | 397 |
| yolov8n-seg.pt | 39.903 | 61.634 | 44.693 | 78.835 | 52.796 | 63.240 | 406 | 109 | 363 |

AP ใช้ IoU 0.50–0.95, 101 recall points และ maxDet 200 P/R/F1 ใช้ confidence ≥0.25 และ IoU ≥0.50 จับ valid GT ก่อนพิจารณา ignore union

## ความเร็ว

| Model | Clean rounds | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 3/3 | 13.818 | 69.345 | 14.421 | 1344.222 |
| yolo11n-seg.pt | 3/3 | 11.861 | 63.587 | 15.727 | 804.799 |
| yolov8n-seg.pt | 3/3 | 9.923 | 79.317 | 12.608 | 1154.993 |

เวลาหลักมาจาก 100 ภาพเดิม × 3 clean rounds หลัง warmup 10 calls ต่อรอบ Adapter รวม preprocess/H2D + forward + postprocess/native-mask transfer ส่วน input read/decode, RLE และเขียนไฟล์วัดแยก FPS = 1000 / mean adapter ms

## การเติมรอบเวลาและประวัติเดิม

เวลาหลักครบทั้ง 17 ตัวแล้ว ใช้ 47 clean rounds จาก run เดิม ร่วมกับ 4 clean rounds จาก segment `cctv600-timing-cont-20261116T090209308785Z` ตามการอนุมัติของผู้ใช้ รวม 51 รอบ × 100 ภาพ = 5,100 measured observations และ warmup ที่ตรวจรับ 510 calls

ส่วนที่เติมคือ YOLO26n รอบ 3, YOLO11n รอบ 2 และ 3, YOLOv8n รอบ 3 ใช้ภาพ ลำดับ input, checkpoint, environment, preprocessing, source, batch, FP32, guards และ timer เดิม เปลี่ยนเฉพาะ run ID/output path และคิว 4 รอบที่ขาด เก็บ manifest ระบุ compatibility และ hashes การเติมครั้งนี้ใช้ 400 measured calls กับ warmup 40 calls เพิ่มเติม ไม่มี quality calls

YOLO11n รอบ 2 เดิมเป็น `CONTAMINATED` มี 100 observations และ warmup 10 calls เก็บไว้ครบโดยตัดออกจากสถิติเวลาหลัก ค่าใช้จ่าย model calls ของ run หลักและ continuation รวมคุณภาพ 10,200 + timing ที่ตรวจรับ 5,610 + timing ที่ปนเปื้อน 110 = 15,920 calls

ผู้ใช้อนุมัติพักเฉพาะ worker เว็บ Body Extraction ชั่วคราว ตรวจแล้วไม่มีงาน RUNNING ก่อนพัก API และฐานข้อมูลคงทำงาน หลัง CUDA contexts เดิมคืนทรัพยากรจึงเปิด timing แบบ detached ใน tmux จากนั้นเปิด worker เว็บคืน หลักฐาน restoration เก็บแยก การตรวจ GPU ทุกช่วงที่ timer เดิมเก็บไว้ผ่านเกณฑ์ไม่มี external compute PID

Continuation จบเมื่อ 2026-11-16T16:04:39+07:00 (เวลาไทย) Original `COMPLETION.json` ที่เคยระบุ timing incomplete ยังคงเดิม สถานะรวมหลัง reconciliation อ่านจาก `metrics/CLOSEOUT.json` และ `metrics/RESULT_INDEX.json`

## กราฟและภาพตัวอย่าง

![PR curve](https://raw.githubusercontent.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/main/metrics/plots/nano_pr_curve.png)

![F1-confidence curve](https://raw.githubusercontent.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/main/metrics/plots/nano_f1_curve.png)

เส้น F1 แสดงผลของ threshold ต่อ trade-off โดย main threshold คง 0.25 ภาพเปรียบเทียบ 16 ภาพเดิมเปิดในเครื่องที่ `outputs/cctv600-20261116T073208561699Z/gallery/index.html` ของ repo นี้

## แหล่งผลและข้อจำกัด

[ตารางกลุ่ม](metrics/TIER_RESULTS.csv) และ [canonical summary](metrics/RESULTS.json) มาจาก saved packets ที่ตรวจรับแล้ว Source/scientific bindings คงเดิม อ่าน [รายงานรวม](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/MASTER_RESULTS.md), [สูตรตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md) และ [ผลตรวจปิดรอบ](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json)

งานนี้วัด visible bbox บน CCTV600 ของ pretrained segmentation checkpoints ส่วน native masks เก็บเป็น output diagnostics ยังไม่มี GT silhouette สำหรับ Person Mask AP ไม่มี human parsing/color/tracking score และยังไม่มี uncertainty analysis
