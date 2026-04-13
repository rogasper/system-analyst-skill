# Developer Task Cards (task.md)

Tasks are **Markdown** (copy to Monday, Jira, Confluence). One card per meaningful unit of work (endpoint, migration slice, atau screen FE).

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

**Frontend tasks:** bisa sertakan JSON ringkas untuk **urutan API** atau **mapping menu** agar QA/dev mudah tempel ke ticket.

## Ringkasan task (tabel)

Tetap boleh pakai tabel ringkas di awal setiap task:

| Field | Detail |
|-------|--------|
| Service | Path atau N/A |
| Method | POST, GET, SQL, N/A |
| Status | New / Update |
| Purpose | Satu kalimat |

Lalu isi detail dengan heading + code blocks (bukan `Request Body \|` satu baris).

## Struktur disarankan per task

Urutan isi tiap task:

1. Heading `## Task N: …` + tabel ringkas (Service, Method, Status, Purpose).
2. `### Flow logic (step by step)` — langkah bernomor **lengkap** (implementasi mengikuti ini).
3. `### SQL — base query contoh` — blok `sql` (ilustrasi; opsional jika task murni FE).
4. `### Request` — blok `json` (jika API).
5. `### Response (200)` — blok `json`; tambahkan `### Response (4xx)` jika perlu.
6. `### Notes` — bullet.

(Lihat contoh lengkap di `output/task/task_fsd_role_tsl.md`.)

## Template minimal

**Judul:** `## Task: {short title}`

**Isi:** **Flow logic** step-by-step → **SQL base contoh** di `sql` → **Request** / **Response** di `json` → **Notes**.

## Quality checklist

- Setiap endpoint di spec punya task (atau pengelompokan eksplisit).
- Migrasi DB terpisah dari task controller jika rollout berisiko.
- Auth / role disebut di Flow atau Notes.
- Contoh JSON **valid** (koma, quote ganda).

## Contoh pola

Struktur task yang baik mengikuti pola: SQL base query di `sql` fence, Request/Response di `json` fence, siap copy ke Jira/Monday.
