---
name: fix-from-report
description: แก้ไขบักจาก test report ที่กำหนด วิเคราะห์ root cause → แก้ → build → verify
inputs:
  - name: report
    description: "test report file"
    required: true
  - name: build-cmd
    description: "คำสั่ง build project (default: npm run build)"
    required: false
  - name: project-dir
    description: "directory ของ project (default: ./)"
    required: false
  - name: output-dir
    description: "directory ที่จะบันทึก fix report (default: ./)"
    required: false
outputs:
  - name: fix-report
    description: "fix-report.md — สรุปการแก้ไข"
    path: "{{output-dir}}/fix-report-{target}.md"
---

# Fix From Report Skill

## วิธีเรียกใช้
```
ใช้ fix-from-report กับ file test-report-menu.md
ใช้ fix-from-report กับ file test-report-menu.md build-cmd="bun run build" project-dir=./backend/ output-dir=reports/
```

**input:**
- `{{report}}` — test report (required)
- `{{build-cmd}}` — คำสั่ง build project (optional, default: npm run build)
- `{{project-dir}}` — directory ของ project (optional, default: ./)
- `{{output-dir}}` — directory ที่จะบันทึก fix report (optional, default: ./)
**output:** `{{output-dir}}/fix-report-{target}.md` (หรือตามที่ user สั่ง)

## Workflow

### 1. อ่าน Test Report
เปิด `{{report}}`
- โฟกัส **Critical Bugs Summary** + **FAIL cases**
- จัดลำดับ: CRITICAL → MEDIUM → LOW

### 2. หา Root Cause (ทีละ FAIL)
1. เปิดไฟล์ที่ `File:Line`
2. อ่าน context ±20 บรรทัด
3. วิเคราะห์: logic bug? missing condition? wrong assumption?
4. บันทึก root cause

### 3. แก้ไข
- **แก้เท่าที่จำเป็น** — ใช้ pattern เดียวกับ code ข้างเคียง

### 4. Build
```bash
cd {{project-dir}}
{{build-cmd}}
```
ถ้า fail → แก้จนผ่าน

### 5. Verify
- รันเฉพาะส่วนที่เกี่ยวข้อง
- ไล่ trace logic อีกครั้ง
- ตรวจสอบ edge cases

### 6. Loop ถ้ายัง FAIL
```bash
while มี FAIL remaining:
   root_cause() → fix() → build() → verify()
```

### 7. สร้าง Fix Report

**output file:** ใช้ชื่อจาก user หรือ default เป็น `{{output-dir}}/fix-report-{target}.md`

```markdown
# Fix Report — {target}

## Summary
| Total Bugs | Fixed | Remaining | Iterations |
|------------|-------|-----------|------------|

## Fix Details
| # | Bug | Root Cause | Fix | Files Changed | Result |
```

## ข้อควรระวัง
- **อย่า global replace** — แก้เฉพาะที่
- **Build ต้องผ่าน** ก่อน verify
