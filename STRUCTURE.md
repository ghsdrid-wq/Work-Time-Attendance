# STRUCTURE — Work-Time-Attendance

> ⚠️ **กฎการดูแลไฟล์นี้ (สำคัญ)**
> ทุกครั้งที่แก้ไขโค้ดใน repo นี้ — เพิ่ม/ลบ/ย้ายไฟล์, เปลี่ยน logic การจับคู่ IN/OUT, เปลี่ยนคอลัมน์ที่อ้างอิง (index), เปลี่ยน rule กะ/ชั่วโมง/OT, หรือเปลี่ยน device map — **ต้องอัปเดต STRUCTURE.md นี้ให้ตรงกับโค้ดเสมอ**

## ภาพรวม
แอป GUI (**ttkbootstrap**) คำนวณ **เวลาเข้า-ออกงาน (Attendance)** จากไฟล์ Excel log การสแกน → จับคู่ IN/OUT รายคน → คำนวณชั่วโมงทำงาน + OT + ประเภทกะ → export ไฟล์สรุป Excel

## วิธีรัน / Entry point
- รัน: `python app.py` → `ttk.Window(themename="flatly")` + คลาส `WorkTimeProcessor`
- ต้องมีฟอนต์ที่ `fonts/IBMPlexSansThai-Regular.ttf`

## โครงสร้างไฟล์
| ไฟล์ | หน้าที่ |
|------|---------|
| `app.py` | **UI ทั้งหมด** — เลือกไฟล์, ตั้งค่ากะ (base/offset), progress/log, เรียก `worker.process()` ใน thread, save ไฟล์ผล |
| `worker.py` | **core logic (ไม่มี GUI)** — `process()` จับคู่ IN/OUT, คำนวณชั่วโมง/OT/กะ, จัดคอลัมน์ภาษาไทย, export ด้วย `xlsxwriter` |
| `fonts/` (`.gitkeep`) | โฟลเดอร์ฟอนต์ฝัง (ต้องวางไฟล์ .ttf เอง) |

## Logic ใน worker.process() (สำคัญ — แก้เมื่อ requirement เปลี่ยน)
- **คอลัมน์อ้างอิงด้วย index:** supplier=7, emp_code=8, emp_name=9, datetime=15, device=17
- **Device map** (`DEVICE_SCAN_MAP`): `ADMIN99900401/03` = IN, `ADMIN99900402/04` = OUT
- จับคู่: group ตาม `emp_code`, จับ IN กับ OUT ตัวถัดไปภายใน 24 ชม.; `keep_unpaired` = เก็บแถวที่ไม่มีคู่
- กะ: `classify_shift` เทียบเวลากับ base กะบ่าย (16:00 ±offset) / กะดึก (00:00 ±offset)
- ชั่วโมงทำงาน floor; OT = work_hours − 9 (clamp 0–5); work_hours ปกติช่วง 9–16 ไม่งั้น "ผิดปกติ"
- supplier ว่าง → "พนักงานบริษัท NuiB (Part-Time)"

## Config (`config.ini`)
- `[PATH]`: `last_import`, `last_export`, `last_import_dir`, `last_export_dir`
- `[SETTINGS]`: `base_day` (16:00), `offset_day` (3), `base_night` (00:00), `offset_night` (3), `keep_unpaired`, `include_data`

## Dependencies
- `ttkbootstrap`, `pandas`, `xlsxwriter`, `openpyxl` (อ่าน xls/xlsx), `tkinter`

## ข้อควรระวัง
- อ้างอิงคอลัมน์ด้วย index → ฟอร์แมตไฟล์สแกนต้องตรงตำแหน่ง
- ไฟล์ผลเขียนเป็น temp `output_<timestamp>.xlsx` ก่อน แล้ว copy ไป path ที่ผู้ใช้เลือก จากนั้นลบ temp
