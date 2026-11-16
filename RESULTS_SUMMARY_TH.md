# ผลประเมิน YOLO บน CCTV600

Run `cctv600-20261116T073208561699Z`

คะแนนหลักเป็น Person Box AP ของ segmentation checkpoints บน visible GT bbox เกณฑ์คนด้านยาว ≥50 px

| Model | Complete | Box AP % | AP50 % | AP75 % | P % | R % | F1 % |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 600/600 | 40.817 | 61.914 | 45.176 | 77.822 | 51.105 | 61.695 |
| yolo11n-seg.pt | 600/600 | 39.493 | 59.325 | 44.019 | 77.339 | 48.375 | 59.520 |
| yolov8n-seg.pt | 600/600 | 39.903 | 61.634 | 44.693 | 78.835 | 52.796 | 63.240 |

| Model | Clean rounds | Forward ms/image | Adapter ms/image | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 2/3 | — | — | — | — |
| yolo11n-seg.pt | 1/3 | — | — | — | — |
| yolov8n-seg.pt | 2/3 | — | — | — | — |

Adapter time รวม preprocessing/H2D, forward, NMS/native masks และ compact transfer ส่วน input I/O, RLE และ output writing เก็บแยก

P/R/F1 ใช้confidence≥0.25 และbboxIoU≥0.50 ส่วนAPใช้IoU0.50:0.05:0.95 และmaxDet200 โดยเก็บpredictionsเหนือconfidencefloor0.001

Timing หลักแสดงเมื่อมีสาม clean rounds ครบ งาน GPU อื่นทำให้รอบเวลาเป็น diagnostics

GT person inventory อ้างคำยืนยันตรวจ600ภาพตามกติกาทุกคน≥50px ข้อมูลบางส่วนเคยใช้พัฒนาannotation helper ส่วนผู้เข้าสอบใช้pretrained checkpointที่ตรึง

ภาพจริงและ per-image diagnostics เก็บ private ใน outputs ของ tier ส่วน results JSON/CSV เก็บสถิติรวม

ผลนี้เป็น checkpoint+application configuration comparison ของกรอบคน และยังไม่มีผล Person Mask AP หรือ tracking
