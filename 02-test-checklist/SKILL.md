---
name: test-checklist
description: ทดสอบตาม checklist file ที่กำหนด สร้าง test report พร้อม FAIL cases
inputs:
  - name: checklist
    description: "path ไปยัง checklist file"
    required: true
  - name: output-dir
    description: "directory ที่จะบันทึก report (default: ./)"
    required: false
outputs:
  - name: report
    description: "test-report.md — ผลการทดสอบ"
    path: "{{output-dir}}/test-report-{target}.md"
---

# Test Checklist Skill

## วิธีเรียกใช้
```
ใช้ test-checklist กับ file checklist-menu.md
ใช้ test-checklist กับ file plan/test-flow/03072026-menu-checklist.md output-dir=reports/
```

**input:**
- `{{checklist}}` — path checklist (required)
- `{{output-dir}}` — directory ที่จะบันทึก report (optional, default: ./)
**output:** `{{output-dir}}/test-report-{target}.md` (หรือตามที่ user สั่ง)

## Workflow

### 1. อ่าน Checklist
เปิด `{{checklist}}` เพื่อรู้:
- จำนวน test cases
- แต่ละ section
- Expected behavior

### 2. รัน Test ทุก case (ห้ามหยุดเมื่อเจอ FAIL)
สำหรับแต่ละ section → แต่ละ case:
1. **เปิด source code** ที่ `File:Line` (ถ้ามี)
2. **วิเคราะห์** ว่า code ตรง expected หรือไม่
3. **บันทึก**: `PASS` / `FAIL` + เหตุผล / `UNCERTAIN`

### 3. เก็บ Evidence (เฉพาะ FAIL)
- Screenshot → `screenshots/{section}-{case}.png`
- Console/Network/API error → `logs/`

### 4. สรุป Critical Bugs
เรียง severity: CRITICAL > MEDIUM > LOW

### 5. สร้าง Report

**output file:** ใช้ชื่อจาก user หรือ default เป็น `{{output-dir}}/test-report-{target}.md`

```markdown
# Test Report — {target}

## Summary
| Section | Total | PASS | FAIL | UNCERTAIN |
|---------|-------|------|------|-----------|

## {section} ({N} cases)
| # | Case | Status | File:Line | Finding |

## Critical Bugs
| # | Bug | Severity | File |
```

## ข้อควรระวัง
- **ห้ามหยุดเมื่อเจอ FAIL** — continue จนครบ
- **UNCERTAIN** = frontend-only ที่ต้อง manual test
- **File:Line** ต้องเจาะจง (service.ts:123)
