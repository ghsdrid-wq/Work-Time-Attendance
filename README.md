# Work Time Attendance

<img width="848" height="732" alt="image" src="https://github.com/user-attachments/assets/c9d0ee46-e3dc-4335-84f9-52041c8a09fe" />

Python desktop application สำหรับประมวลผลข้อมูลเวลาเข้างาน-ออกงานจากไฟล์ Scan Attendance พร้อมระบบจับคู่ IN/OUT อัตโนมัติ คำนวณชั่วโมงทำงาน ตรวจสอบกะงาน และ Export รายงาน Excel

โปรแกรมถูกออกแบบสำหรับ warehouse operation และ attendance verification workflow เพื่อช่วยลดเวลาการตรวจสอบเวลาทำงานของพนักงาน

รองรับ:

- Attendance Pairing
- Shift Classification
- OT Calculation
- Incomplete IN/OUT Detection
- Excel Report Export
- GUI Monitoring
- Config Persistence
- Styled Excel Output

---

# Features

- Auto pairing IN/OUT
- Attendance validation
- Shift classification
- Work hour calculation
- OT calculation
- Incomplete scan detection
- Configurable shift window
- Export DATA sheet option
- Progress tracking
- Real-time log viewer
- Remember last path
- Styled Excel export
- Auto column sizing
- Thai font support
- GUI desktop application
- Windows EXE compatible

---

# Application Overview

โปรแกรมนี้ใช้สำหรับ:

1. อ่านข้อมูล Scan Attendance
2. ตรวจจับ IN / OUT
3. จับคู่เวลาเข้าออกงาน
4. คำนวณชั่วโมงทำงาน
5. แยกประเภทกะงาน
6. คำนวณ OT
7. Export รายงาน Excel

เหมาะสำหรับ:

- Warehouse Operation
- HR Attendance Verification
- Shift Management
- Logistics Operation
- Workforce Monitoring

---

# Tech Stack

- Python
- Pandas
- XlsxWriter
- ttkbootstrap
- Tkinter
- ConfigParser
- Threading

---

# Project Structure

```text
project/
│
├── app.py
├── worker.py
├── config.ini
│
├── fonts/
│   └── IBMPlexSansThai-Regular.ttf
│
└── output/
```

---

# Attendance Workflow

```text
Load Excel File
    ↓
Map Attendance Columns
    ↓
Detect IN / OUT
    ↓
Pair Employee Scan
    ↓
Calculate Work Hours
    ↓
Classify Shift
    ↓
Calculate OT
    ↓
Validate Attendance
    ↓
Export Excel Report
```

---

# IN / OUT Detection

ระบบจะ map device code:

| Device | Result |
|---|---|
| ADMIN99900401 | IN |
| ADMIN99900403 | IN |
| ADMIN99900402 | OUT |
| ADMIN99900404 | OUT |

---

# Pairing Logic

ระบบจะ:

1. Group ตามพนักงาน
2. Sort ตามเวลา
3. หา OUT ตัวแรกหลัง IN
4. จำกัด pairing ภายใน 24 ชั่วโมง
5. ป้องกันการ reuse OUT record

---

# Unpaired Detection

รองรับ:

- ไม่พบเวลาเข้า
- ไม่พบเวลาออก

โดยสามารถเลือก:

```text
เก็บรายชื่อ IN/OUT ที่ไม่สมบูรณ์
```

ผ่าน GUI ได้

---

# Shift Classification

ระบบรองรับ:

## กะบ่าย

Default:

```text
16:00 ± 3 ชั่วโมง
```

Result:

```text
13:00 - 19:00
```

---

## กะดึก

Default:

```text
00:00 ± 3 ชั่วโมง
```

Result:

```text
21:00 - 03:00
```

รองรับกรณีข้ามวันอัตโนมัติ

---

# Work Hour Calculation

ระบบจะคำนวณ:

```text
time_out - time_in
```

และ:

- convert เป็นชั่วโมง
- floor ค่าเวลา
- validate ชั่วโมงทำงาน

---

# OT Calculation

สูตร:

```text
OT = Work Hours - 9
```

Rules:

| Condition | Result |
|---|---|
| ต่ำกว่า 0 | 0 |
| สูงกว่า 5 | 5 |
| ปกติ | Actual OT |

---

# Attendance Validation

ระบบจะ mark:

| Status | Condition |
|---|---|
| ปกติ | ชั่วโมง + กะ ถูกต้อง |
| ผิดปกติ | ชั่วโมงผิดเงื่อนไข |
| ไม่พบเวลาเข้า | ไม่มี IN |
| ไม่พบเวลาออก | ไม่มี OUT |

---

# Supplier Handling

หาก supplier ว่าง:

ระบบจะ replace อัตโนมัติเป็น:

```text
พนักงานบริษัท NuiB (Part-Time)
```

---

# Excel Export

ระบบ export:

## Result Sheet

ประกอบด้วย:

| Column |
|---|
| ชื่อซัพพลายเออร์ |
| รหัสพนักงาน |
| ชื่อพนักงาน |
| สแกนเข้า |
| สแกนออก |
| วันที่ทำงาน |
| เวลาทำงาน |
| วันที่ออกงาน |
| เวลาออกงาน |
| กะงาน |
| ชั่วโมงทำงาน |
| ชั่วโมง OT |
| สถานะ |

---

## Optional DATA Sheet

สามารถเลือก:

```text
Export DATA sheet
```

เพื่อ export raw source data เพิ่มเติม

---

# Excel Styling

ระบบใช้:

```python
xlsxwriter
```

สำหรับ:

- auto filter
- auto column width
- left alignment
- row formatting
- readable layout

---

# GUI Features

โปรแกรมใช้:

```python
ttkbootstrap + Tkinter
```

ประกอบด้วย:

- Input File Browser
- Progress Bar
- Real-time Log
- Export Settings
- Shift Settings
- Process Button

---

# Thai Font Support

โปรแกรมรองรับฟอนต์ไทยผ่าน:

```text
IBM Plex Sans Thai
```

โดยโหลดจาก:

```text
fonts/IBMPlexSansThai-Regular.ttf
```

---

# Config System

ใช้:

```text
config.ini
```

เก็บ:

- last import path
- last export path
- shift settings
- export settings
- checkbox settings

---

# Example Config

```ini
[SETTINGS]
base_day=16:00
offset_day=3
base_night=00:00
offset_night=3
keep_unpaired=False
include_data=False
```

---

# Progress Workflow

GUI แสดง:

```text
Step 1: Load Excel
Step 2: Pair IN/OUT
Step 3: Calculate Work Hours
Step 4: Export Result
```

พร้อม:

- progress bar
- real-time log
- process status

---

# Threading Design

โปรแกรมใช้:

```python
threading.Thread()
```

เพื่อ:

- ป้องกัน GUI freeze
- รองรับ large Excel file
- background processing
- smooth UI update

---

# Temporary File Workflow

ระบบจะ:

1. สร้าง output ชั่วคราว
2. ให้ user เลือก save location
3. copy ไฟล์ไป destination
4. ลบ temp file อัตโนมัติ

---

# Installation

## 1. Clone Repository

```bash
git clone https://github.com/yourname/work-time-attendance.git
```

---

## 2. Install Dependencies

```bash
pip install pandas xlsxwriter ttkbootstrap openpyxl
```

---

## 3. Run Application

```bash
python app.py
```

---

# Build EXE

ใช้ PyInstaller:

```bash
pyinstaller --onefile --windowed app.py
```

หรือ:

```bash
pyinstaller --noconsole --onefile app.py
```

---

# Error Handling

ระบบรองรับ:

- Invalid Excel file
- Missing attendance pair
- Invalid datetime
- Export failure
- Save cancelled
- Missing supplier
- Invalid shift data
- Temporary file cleanup

---

# User Experience Features

- Auto remember last path
- Auto save settings
- Real-time process log
- Styled Excel output
- Shift tooltip helper
- Embedded Thai font
- Export DATA option
- Keep unpaired option

---

# Future Improvements

- Multi-shift support
- Attendance dashboard
- SQLite attendance cache
- Employee summary report
- Monthly attendance report
- PDF export
- Email report system
- Barcode attendance import
- Database integration
- Drag & drop Excel support

---

# License

MIT License

---

# Author

Developed for attendance verification and warehouse workforce management workflow.


