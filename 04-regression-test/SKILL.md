---
name: regression-test
description: ตรวจสอบซ้ำหลังแก้ไข รับ checklist + test report เดิม + fix report เพื่อเปรียบเทียบ
inputs:
  - name: checklist
    description: "checklist file"
    required: true
  - name: original-report
    description: "test report เดิมจากรอบแรก"
    required: true
  - name: fix-report
    description: "fix report"
    required: true
  - name: output-dir
    description: "directory ที่จะบันทึก regression report (default: ./)"
    required: false
outputs:
  - name: regression-report
    description: "regression-report.md — สรุปผล"
    path: "{{output-dir}}/regression-report-{target}.md"
---

# Regression Test Skill

## วิธีเรียกใช้
```
ใช้ regression-test กับ file checklist-menu.md, test-report-menu.md, fix-report-menu.md
ใช้ regression-test กับ file checklist-menu.md, test-report-menu.md, fix-report-menu.md output-dir=reports/
```

**inputs:**
- `{{checklist}}` — checklist file (required)
- `{{original-report}}` — test report เดิม (required)
- `{{fix-report}}` — fix report (required)
- `{{output-dir}}` — directory ที่จะบันทึก regression report (optional, default: ./)
**output:** `{{output-dir}}/regression-report-{target}.md` (หรือตามที่ user สั่ง)

## Workflow

### 1. อ่าน 3 ไฟล์
- `{{checklist}}` — กรณีทดสอบทั้งหมด
- `{{original-report}}` — ผลเดิม (PASS/FAIL)
- `{{fix-report}}` — สิ่งที่แก้ไป

### 2. รัน Checklist ทุกกรณีอีกครั้ง
ใช้ process เดียวกับ `test-checklist` แต่เพิ่ม comparison:

| Original | Now | ความหมาย |
|----------|-----|----------|
| PASS | PASS | ✅ ยังดี |
| FAIL | PASS | ✅ แก้แล้ว |
| FAIL | FAIL | ❌ ยังไม่ผ่าน |
| PASS | FAIL | 🚨 Regression! |

### 3. ตรวจสอบไฟล์ที่แก้
เปิดทุกไฟล์จาก `fix-report` → verify:
- Logic ถูกต้อง
- ไม่มี side effect

### 4. สร้าง Regression Report

**output file:** ใช้ชื่อจาก user หรือ default เป็น `{{output-dir}}/regression-report-{target}.md`

```markdown
# Regression Report — {target}

## Summary
| Metric | Original | Now | Change |
|--------|----------|-----|--------|
| Total PASS | N | N | ±N |
| Total FAIL | N | N | ±N |
| New Bugs | — | N | 🚨 |
| Regressions | — | N | 🚨 |

## Result by Section
| Section | Original | Now | Status |
|---------|----------|-----|--------|

## Conclusion
- [ ] All bugs fixed
- [ ] No regression
- [ ] Ready for release
```

## Exit
| Result | Action |
|--------|--------|
| ✅ All PASS | Done |
| ❌ FAIL remaining | กลับไป fix-from-report |
| 🚨 Regression | Rollback + re-fix |

## ข้อควรระวัง
- **Zero tolerance** สำหรับ regression
- เปรียบเทียบ **section เดียวกัน** (ใช้ section number)
- UNCERTAIN เดิม → ต้อง verify อีกครั้ง
