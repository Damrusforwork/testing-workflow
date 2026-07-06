# Generic Testing Workflow

A reusable 4-skill testing workflow that can be used with any project!

## Workflow Pipeline

```
01-create-checklist → 02-test-checklist → 03-fix-from-report → 04-regression-test
```

## Skills Overview

### 1. 01-create-checklist

Create a test checklist from your source code.

**Inputs:**

- `target` (required): File or module to analyze
- `source-dir` (optional): Source code directory (default: src/)
- `output-dir` (optional): Directory to save checklist (default: plan/test-flow/)
- `patterns` (optional): Patterns to search for (comma-separated)

### 2. 02-test-checklist

Test according to the checklist and create a test report.

**Inputs:**

- `checklist` (required): Path to checklist file
- `output-dir` (optional): Directory to save test report (default: ./)

### 3. 03-fix-from-report

Fix bugs from the test report.

**Inputs:**

- `report` (required): Path to test report file
- `build-cmd` (optional): Project build command (default: npm run build)
- `project-dir` (optional): Project directory (default: ./)
- `output-dir` (optional): Directory to save fix report (default: ./)

### 4. 04-regression-test

Perform regression testing after fixes.

**Inputs:**

- `checklist` (required): Path to checklist file
- `original-report` (required): Path to original test report
- `fix-report` (required): Path to fix report
- `output-dir` (optional): Directory to save regression report (default: ./)

## Usage Example

1. Create a checklist:

```
ใช้ skill 01-create-checklist กับ file menu source-dir=src/
```

2. Test the checklist:

```
ใช้ skill 02-test-checklist กับ file plan/test-flow/03072026-menu-checklist.md
```

3. Fix from the test report:

```
ใช้ skill 03-fix-from-report กับ file test-report-menu.md
```

4. Regression test:

```
ใช้ skill 04-regression-test กับ file plan/test-flow/03072026-menu-checklist.md, test-report-menu.md, fix-report-menu.md
```

## License

MIT
