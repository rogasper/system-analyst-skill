# FSD Analyzer

Transform **Functional Specification Documents (FSD)** into **Markdown-first** technical artifacts — API specs, ERD schemas, UML diagrams, and developer task cards — using AI-powered coding assistants like Cursor, Claude Code, OpenCode, or any agent that supports custom skills.

## What It Does

FSD Analyzer is a **skill/plugin** for AI coding assistants that acts as a Senior System Analyst. It reads your FSD or business requirements and produces:

| Output | Purpose |
|--------|---------|
| **spec_api.md** | REST API contract (endpoints, auth, validation, errors, examples) |
| **erd.md** + **DBML** | Database schema + paste-ready for [dbdiagram.io](https://dbdiagram.io) |
| **UML diagrams** | PlantUML — sequence, class, activity, state, component, use case diagrams |
| **task.md** | Developer task cards (copy to Monday, Jira, Confluence) |
| **Gap Report** | Structured diff: FSD vs existing ERD/API |
| **Consistency Report** | Cross-check: ERD ↔ API spec ↔ tasks |

## Features

- **Generate artifacts** — From FSD to full API spec, ERD, UML diagrams, and task cards
- **UML diagrams** — PlantUML code blocks for [PlantUML](https://plantuml.com) / [PlantText](https://www.planttext.com/): sequence, class, activity, state, component, and use case diagrams
- **Gap analysis** — Compare new FSD against existing database schema and API specs
- **Consistency checks** — Validate alignment across ERD, API spec, and task cards
- **Master files** — Rolling `MASTER_ERD.md` and `MASTER_SPEC_API.md` for incremental FSD-by-section work
- **Copy-paste friendly** — Markdown tables, fenced `sql`/`json`/`plantuml` blocks ready for spreadsheets, Jira, Monday, dbdiagram.io, or PlantUML renderers
- **Optional Python scripts** — Validate DBML, check spec structure, extract entity hints, compare artifacts (no external dependencies)
- **Optional Streamlit UI** — Browser-based interface for the validation scripts

## Quick Start

### 1. Use with Cursor / Claude Code / OpenCode

Point your AI assistant to this repo as a skill. For example in **OpenCode**, add to your project's `.agents/skills/` directory or reference the `SKILL.md` directly.

### 2. In your chat, @-reference your FSD and existing artifacts

```
Analisis FSD ini dan generate ERD, API spec, task cards
@fsd_user_management.md
```

Or with existing artifacts:

```
Gap analysis: @fsd_new_feature.md vs @erd_now.md @spec_api.md
```

Or using master files for incremental work:

```
@MASTER_ERD.md @MASTER_SPEC_API.md @fsd_section_3.md — merge changes into master
```

## Project Structure

```
fsd-analyzer/
├── SKILL.md                    # Agent instructions (main skill definition)
├── references/                 # Format templates & procedures
│   ├── spec_api_format.md      # API spec structure
│   ├── erd_format.md           # ERD tables + DBML format
│   ├── uml_format.md           # UML diagrams (PlantUML)
│   ├── task_format.md          # Developer task card format
│   ├── gap_analysis.md         # Gap analysis procedure + report template
│   ├── consistency_check.md    # Consistency check procedure + report template
│   └── master_artifacts.md     # MASTER_ERD + MASTER_SPEC workflow
├── scripts/                    # Optional local validation (Python, stdlib only)
│   ├── validate_erd.py         # DBML table + ref validation
│   ├── validate_spec.py        # Spec markdown structure heuristics
│   ├── extract_entities.py     # Extract table/entity hints from FSD
│   └── compare_artifacts.py    # Compare FSD table mentions vs ERD tables
├── evals/                      # Evaluation prompts & sample data
│   ├── evals.json              # Test prompts and expected outputs
│   ├── sample_fsd.md           # Sample Functional Specification Document
│   └── sample_dbml.dbml        # Sample DBML for script smoke tests
├── optional_web/               # Streamlit UI for running scripts
│   ├── app.py
│   ├── requirements.txt
│   └── .env.example
└── assets/templates/           # Project-specific snippet placeholders
```

## Usage Examples

### Generate Artifacts from FSD

```
Here is our FSD for the user management module. Generate spec_api, erd, UML diagrams, and task cards.
@fsd_user_management.md
```

### Generate UML Diagrams

```
Generate PlantUML sequence and class diagrams from this FSD.
@fsd_user_management.md
```

Or specifically:

```
Bikin sequence diagram untuk endpoint user registration dan login
@fsd_user_management.md
```

### Gap Analysis

```
Compare this new FSD with our existing ERD and API spec. Produce a Gap Report.
@fsd_new_feature.md @erd_current.md @spec_api_current.md
```

### Consistency Check

```
Check consistency between the ERD, API spec, and task cards. List errors and warnings.
@erd.md @spec_api.md @task.md
```

### Discussion Mode

```
We're still scoping this feature with the BA. List open questions — don't write full spec yet.
@fsd_draft.md
```

## Running the Scripts

All scripts use Python standard library only (no pip install needed for the scripts themselves).

```bash
# Extract entity/table hints from an FSD
python scripts/extract_entities.py path/to/fsd.md

# Validate DBML structure (tables + foreign key refs)
python scripts/validate_erd.py path/to/schema.dbml

# Check API spec markdown structure
python scripts/validate_spec.py path/to/spec_api.md

# Compare FSD table mentions vs ERD tables (heuristic)
python scripts/compare_artifacts.py --fsd path/to/fsd.md --erd path/to/erd.md
```

### Quick smoke test

```bash
python scripts/extract_entities.py evals/sample_fsd.md
python scripts/validate_erd.py evals/sample_dbml.dbml
```

## Optional Web UI

A minimal Streamlit interface to paste FSD/spec/DBML text and run validators in the browser.

```bash
cd optional_web
pip install -r requirements.txt
streamlit run app.py
```

## Workflow: Master Artifacts

For projects where you work **FSD-by-section** (common in large systems):

1. Create `MASTER_ERD.md` and `MASTER_SPEC_API.md` in your project root
2. For each FSD section, `@`-reference the master files + the new FSD slice
3. The agent merges changes into the master files incrementally
4. No need to re-attach every legacy file each time

See [references/master_artifacts.md](references/master_artifacts.md) for the full workflow.

## Quality Gates

Every output is checked against:

- All FSD requirements covered or explicitly flagged
- REST consistency with auth and error documentation
- Normalized schema with justified FKs and indexes
- Cross-artifact alignment (spec ↔ ERD ↔ tasks ↔ UML)
- UML diagram entity names, endpoint paths, and statuses consistent with ERD and spec
- Developer-ready task granularity with QA acceptance criteria
- `sql`/`json` code fences for easy copy-paste to Jira, Monday, or Confluence

## Compatible AI Assistants

Works with any AI coding assistant that supports custom skill instructions:

- [Cursor](https://cursor.sh)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [OpenCode](https://opencode.ai)
- Any agent that can read `SKILL.md` as context

## License

MIT — use freely in your software projects.
