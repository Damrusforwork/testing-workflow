---
name: create-checklist
description: สร้าง Checklist จาก source code รับ parameter file path ที่ต้องการวิเคราะห์
inputs:
  - name: target
    description: "ไฟล์หรือโมดูลที่ต้องการวิเคราะห์ (เช่น menu, approval, sms)"
    required: true
  - name: source-dir
    description: "directory ที่เก็บ source code (default: src/)"
    required: false
  - name: output-dir
    description: "directory ที่จะบันทึก checklist (default: plan/test-flow/)"
    required: false
  - name: patterns
    description: "patterns ที่ต้องการค้นหา (comma-separated, default: permission,approval,feature,frontend)"
    required: false
outputs:
  - name: checklist
    description: "checklist.md — ไฟล์ checklist ที่สร้าง"
    path: "{{output-dir}}/{ddMMyyyy}-{target}-checklist.md"
---

# Create Checklist Skill

## วิธีเรียกใช้

```
ใช้ skill create-checklist กับ file menu
ใช้ skill create-checklist กับ file src/cpc-gateway.service.ts source-dir=src/ output-dir=plan/test/
```

**input:**

- `{{target}}` — ชื่อหรือ path ที่ต้องการสร้าง checklist (required)
- `{{source-dir}}` — directory ที่เก็บ source code (optional, default: src/)
- `{{output-dir}}` — directory ที่จะบันทึก checklist (optional, default: plan/test-flow/)
- `{{patterns}}` — patterns ที่ต้องการค้นหา (optional, comma-separated)
  **output:** `{{output-dir}}/{ddMMyyyy}-{target}-checklist.md`

## Workflow

### 1. วิเคราะห์ Structure

- ค้นหาไฟล์ใน `{{source-dir}}` ตามประเภท (controller, service, model, component)
- โฟกัสที่ `{{target}}` ถ้าระบุมา

### 2. วิเคราะห์ Logic ตาม Patterns ที่กำหนด

- ค้นหา patterns ที่ผู้ใช้ระบุ (หรือใช้ default patterns)
- บันทึกผลการค้นหา

### 3. สร้าง Checklist

- จัดรูปแบบเป็น markdown ตาม template ด้านล่าง

## Output Format

```markdown
# {target} — Test Checklist

## N. {section}

| #   | Case | Status | File:Line | Finding |
| --- | ---- | ------ | --------- | ------- |

## Test Summary

| Section | Total | Passed | Failed |
| ------- | ----- | ------ | ------ |
```

## Naming

`{{output-dir}}/{ddMMyyyy}-{target}-checklist.md`
