# สรุปผล Nano (N) บน CCTV600

## สรุปใน 1 นาที

- ทดสอบ 3 pretrained checkpoints บน 600 ภาพชุดเดียวกันและ GT เข้าเกณฑ์ 769 คน
- Input 640×640, batch 1, FP32 บน Tesla T4 ไม่มี fine-tuning ใน benchmark รอบนี้
- คุณภาพครบ 600 IDs ต่อโมเดล และเวลาครบ 3 clean rounds ต่อโมเดล
- Box AP สูงสุดเป็น yolo26n-seg.pt ที่ 40.817%
- Adapter time ต่ำสุดเป็น yolo11n-seg.pt ที่ 63.587 ms/ภาพ
- Allocated VRAM ต่ำสุดเป็น yolo11n-seg.pt ที่ 804.799 MiB

## ผลลัพธ์หลัก

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26n-seg.pt | 40.817 | 45.176 | 51.105 | 13.818 | 69.345 | 14.421 | 1344.222 |
| yolo11n-seg.pt | 39.493 | 44.019 | 48.375 | 11.861 | 63.587 | 15.727 | 804.799 |
| yolov8n-seg.pt | 39.903 | 44.693 | 52.796 | 9.923 | 79.317 | 12.608 | 1154.993 |

Box AP/P/R/F1 แสดงเป็นเปอร์เซ็นต์ Forward และ adapter เป็น ms/ภาพ, FPS = 1000 / mean adapter ms และ VRAM เป็น peak allocated MiB

## ผู้ชนะของแต่ละด้าน

| ด้าน | โมเดล | ค่าที่วัด |
| --- | --- | --- |
| Box AP | yolo26n-seg.pt | 40.817 % |
| AP75 | yolo26n-seg.pt | 45.176 % |
| Recall | yolov8n-seg.pt | 52.796 % |
| Forward time | yolov8n-seg.pt | 9.923 ms/ภาพ |
| Adapter time | yolo11n-seg.pt | 63.587 ms/ภาพ |
| Adapter FPS | yolo11n-seg.pt | 15.727 ภาพ/วินาที |
| Allocated VRAM | yolo11n-seg.pt | 804.799 MiB |

## สิ่งที่ตัวเลขบอกเรา

- yolo26n-seg.pt มี AP สูงกว่า yolov8n-seg.pt 0.914 จุดเปอร์เซ็นต์
- ตัวนำ AP มี TP 393, FP 112 และ FN 376 ที่ confidence ≥0.25 / bbox IoU ≥0.50
- Adapter ของ yolo11n-seg.pt เร็วกว่าตัวนำ AP 5.758 ms/ภาพ
- ตัวนำ AP ใช้ allocated VRAM มากกว่าค่าต่ำสุด 539.423 MiB

## บทบาทของแต่ละโมเดล

| โมเดล | จุดเด่นที่วัดได้ | ข้อแลกเปลี่ยน | เงื่อนไขการเลือก |
| --- | --- | --- | --- |
| yolo26n-seg.pt | Box AP สูงสุดในกลุ่ม | Recall ห่างตัวนำ 1.691 pp, adapter เพิ่ม 5.758 ms, VRAM เพิ่ม 539.423 MiB | เน้น Box AP |
| yolo11n-seg.pt | adapter เร็วสุดในกลุ่ม, allocated VRAM ต่ำสุดในกลุ่ม | AP ห่างตัวนำ 1.324 pp, Recall ห่างตัวนำ 4.421 pp | จำกัดเวลา adapter, จำกัด allocated VRAM |
| yolov8n-seg.pt | Precision สูงสุด, Recall สูงสุด, forward เร็วสุดในกลุ่ม | AP ห่างตัวนำ 0.914 pp, adapter เพิ่ม 15.730 ms, VRAM เพิ่ม 350.193 MiB | ลดสัดส่วนกรอบเกิน, เก็บคนให้ครบ |

## ข้อแลกเปลี่ยนหลัก

### คุณภาพกับความเร็ว

ตัวนำ AP มี AP 40.817% ส่วนตัว adapter เร็วสุดมี AP 39.493% ทั้งสองเป้าหมายอาจได้ checkpoint เดียวกัน ให้พิจารณา forward กับ adapter แยกกัน เพราะ native mask postprocessing มีต้นทุนอยู่หลัง model forward

### คุณภาพกับหน่วยความจำ

ตัว allocated VRAM ต่ำสุดมี AP 39.493% และใช้ 804.799 MiB ค่า allocator peak นี้ครอบคลุม resident model ใน benchmark หลัง warmup ส่วน memory รวมในระบบให้ตรวจ CUDA context และบริการที่รันร่วมด้วย

## ข้อควรระวังในการตีความ

GT ใช้ visible bbox ของคนด้านยาว ≥50 px รวม 769 คน การรับรอง person inventory อิงการตรวจรับของผู้จัดทำ dataset ยังไม่มี independent inventory review ครบชุด ขนาดโมเดลและข้อมูล pretraining ต่างกัน การเปรียบเทียบนี้ยังไม่แยกสาเหตุทาง architecture ไม่มี uncertainty analysis และ Person Mask AP ต้องมี GT silhouette เพิ่ม Adapter time ครอบคลุม preprocessing/H2D, forward และ postprocessing/native-mask transfer โดย input I/O, RLE, writing, model loading และ queue time มีขอบเขตแยก

## อ่านต่อ

[รายงานเทคนิค](REPORT.md) · [การวิเคราะห์ภาพและข้อผิดพลาด](PRESENTATION_SUMMARY_TH.md) · [ตารางเต็ม](metrics/TIER_RESULTS.csv) · [สูตรตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md)
