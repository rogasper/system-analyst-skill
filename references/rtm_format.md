# Requirement Traceability Matrix (RTM)

Run **RTM generation** when the user asks to trace **business requirements** down to the technical side (design solution + test case), e.g. "Generate RTM", "bikin traceability matrix", "trace requirement ke design dan test case".

## Inputs

Read artifacts from the project root (no need to @-mention them):

1. **`input/fsd/*.md`** — source FSD(s) that define business requirements
2. **`output/spec/*.md`** — generated API specs (design solutions)
3. **`output/erd/*.md` / `.dbml`** — generated ERD (design solutions)
4. **`output/task/*.md`** — generated task cards (design solutions + test case hints)
5. **`MASTER_SPEC_API.md`** + **`MASTER_ERD.md`** at project root, if present

Respect a total prompt budget (~48KB): read up to ~8 files per folder, cap each file's content (FSD 5KB, spec 4KB, ERD 4KB, task 2.5KB, master files 4KB). Stop adding artifacts once the budget is spent.

## Output

Write **EXACTLY ONE file** to **`output/rtm/RTM.md`** — the dashboard watches this path and auto-refreshes after you finish.

## Structure

Indonesian for titles/descriptions, English for technical terms. Use this exact structure:

```markdown
# Requirement Traceability Matrix

## Business Requirements
| ID | Title | Description |
|----|-------|-------------|
| BR-001 | Login & Autentikasi | Pengguna dapat masuk ke aplikasi |

## Design Solutions
| ID | Title | Source | Description |
|----|-------|--------|-------------|
| DS-001 | AuthService.login | API Spec /auth/login | Endpoint login dengan validasi kredensial |

## Test Cases
| ID | Title | Steps | Expected |
|----|-------|-------|----------|
| TC-001 | Login berhasil | 1. Buka halaman login 2. Input email & password benar 3. Klik Login | Masuk ke dashboard |

## Functional Requirements
| ID | BR | Title | Description | Design Solution | Test Case |
|----|----|-------|-------------|-----------------|-----------|
| FR-001 | BR-001 | Login | Sistem memvalidasi kredensial pengguna | DS-001 | TC-001 |
```

## Rules

- Every functional requirement **MUST** reference a business requirement (BR column).
- Design Solution / Test Case cells reference the codes defined above (semicolon-separated if multiple).
- Use sequential IDs: BR-001, FR-001, DS-001, TC-001, ...
- Each FR should be traced to **at least one design solution**; add test cases where derivable from the artifacts.
- If a requirement has no design or no test yet, **leave the cell empty** — that gap is what the dashboard highlights.
- Only write `output/rtm/RTM.md`. Do NOT modify any other file.
