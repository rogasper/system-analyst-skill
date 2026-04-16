# Developer Task Cards (task.md)

Tasks are **Markdown** (copy to Monday, Jira, Confluence). One card per meaningful unit of work (endpoint, migration slice, atau screen FE).

## Story Points

**1 SP = 4 hours**

| SP | Hours | Criteria |
|----|-------|----------|
| 1 SP | 4h | Single simple CRUD endpoint, no dependency, existing pattern |
| 2 SP | 8h | 1 endpoint + medium logic (validation, 1-2 join), or standard FE page |
| 3 SP | 12h | Multi-related endpoints, medium business logic, light integration |
| 5 SP | 20h | Full feature (BE + DB + seeding), multi-table, approval flow, complex FE |
| 8 SP | 32h | New module, third-party integration, complex state, many edge cases |
| 13 SP | 52h | Epic: cross-module impact, large migration, architecture decision |

## Flow logic vs SQL

- **`### Flow logic (step by step)`** — **wajib**: urutan kerja bernomor (sumber utama dev/QA); jangan diganti hanya oleh potongan SQL.
- **Blok `sql`** — **base query contoh** saja; boleh diadaptasi ke ORM, pagination, dan standar DB project. Beri judul seperti *"base query contoh"* agar tidak dibaca sebagai satu-satunya sumber kebenaran.

## Format untuk copy-paste ke Jira / Monday

Gunakan **fenced code blocks** dengan bahasa eksplisit supaya satu klik copy:

| Bagian | Fence | Alasan |
|--------|-------|--------|
| Query DB | ` ```sql ` | Dev/DBA paste ke tool SQL |
| Request / response API | ` ```json ` | Paste ke deskripsi ticket, contoh Postman, atau comment |
| HTTP mentah (opsional) | ` ```http ` | Dokumentasi method + path + header |

**Jangan** pakai `mailto:` di dalam JSON — email polos saja agar JSON valid saat di-copy.

**Frontend tasks:** bisa sertakan JSON ringkas untuk **urutan API** atau **mapping menu** agar QA/dev mudah tempel ke ticket. Untuk task FE lengkap, lihat `references/frontend_task_format.md`.

## Ringkasan task (tabel)

Tetap boleh pakai tabel ringkas di awal setiap task:

| Field | Detail |
|-------|--------|
| Service | Path atau N/A |
| Method | POST, GET, SQL, N/A |
| Status | New / Update |
| Purpose | Satu kalimat |
| Story Point | 1 / 2 / 3 / 5 / 8 / 13 |
| Duration | Auto = SP × 4h |
| Developer | Assigned developer name |
| Depends On | Task ID(s) atau "—" |
| Blocks | Task ID(s) atau "—" |
| Critical Path | Yes / No |
| Risk Level | Low / Medium / High |

Lalu isi detail dengan heading + code blocks (bukan `Request Body |` satu baris).

## Struktur disarankan per task

Urutan isi tiap task:

1. Heading `## Task N: …` + tabel ringkas (Service, Method, Status, Purpose, SP, Duration, Developer, Depends On, Blocks, Critical Path, Risk).
2. `### Flow logic (step by step)` — langkah bernomor **lengkap** (implementasi mengikuti ini).
3. `### SQL — base query contoh` — blok `sql` (ilustrasi; opsional jika task murni FE).
4. `### Request` — blok `json` (jika API).
5. `### Response (200)` — blok `json`; tambahkan `### Response (4xx)` jika perlu.
6. `### Notes` — bullet.

(Lihat contoh lengkap di `output/task/task_fsd_role_tsl.md`.)

## Template minimal

**Judul:** `## Task: {short title}`

**Isi:** **Flow logic** step-by-step → **SQL base contoh** di `sql` → **Request** / **Response** di `json` → **Notes**.

## Dependency example

```markdown
## Task 1: DB Migration - Users Table

| Depends On | — | Blocks | T2, T3 | Critical | Yes |

## Task 2: POST /api/v1/users

| Depends On | T1 | Blocks | T5 | Critical | Yes |

## Task 3: POST /api/v1/auth/login

| Depends On | T1 | Blocks | T4 | Critical | No |

## Task 4: FE Login Page

| Depends On | T3 | Blocks | — | Critical | No |
```

## Quality checklist

- Setiap endpoint di spec punya task (atau pengelompokan eksplisit).
- Migrasi DB terpisah dari task controller jika rollout berisiko.
- Auth / role disebut di Flow atau Notes.
- Contoh JSON **valid** (koma, quote ganda).
- Semua task punya **Story Point**.
- Semua task punya **Depends On** dan **Blocks** field.
- **Critical path** tasks ditandai.
- Developer assignment terisi (untuk timeline estimation).

## Contoh pola

Struktur task yang baik mengikuti pola: SQL base query di `sql` fence, Request/Response di `json` fence, siap copy ke Jira/Monday. Setiap task punya SP, dependency, dan critical path flag untuk timeline generation.
