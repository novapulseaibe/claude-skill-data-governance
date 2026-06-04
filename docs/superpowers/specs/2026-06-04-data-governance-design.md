# Data Governance Skill — Design Spec

**Date:** 2026-06-04  
**Status:** Approved  
**Author:** Alexis Vervondel

---

## Overview

A Claude Code skill (`/data-governance`) that enforces data governance best practices on any AI-generated project. It operates in two modes: **pre-audit** (before coding starts) and **audit** (scan an existing project). It always enforces NIS2, SOC2, and ISO 27001 compliance.

The skill is installed globally at `~/.claude/skills/data-governance/` so it is available in every project.

---

## Modes

### Mode Detection

The skill auto-detects the mode on invocation:

- **Empty directory / no source files** → pre-audit mode
- **Source files present** → audit mode
- **Manual override:** `/data-governance --pre-audit` or `/data-governance --audit`

---

## Pre-Audit Mode

### Purpose

Run before any code is written. Establishes governance foundations to avoid rework.

### Step 1 — Project Type Detection

The skill inspects for manifest files to classify the project:

| Signal | Project type |
|--------|-------------|
| `package.json` + `src/` with JSX/TSX | Web app |
| `package.json` / `go.mod` / `pyproject.toml` with routes | API / Service |
| `package.json` with `bin` field | CLI |
| `package.json` with `main`/`exports`, no app entry | Library / SDK |
| `*.ipynb` / `requirements.txt` with sklearn/torch/tf | ML model |
| Dagster / Airflow / dbt config | Data pipeline |

If ambiguous, the skill asks once.

### Step 2 — Governance Questionnaire

Questions are asked in pillar-priority order:

**Pillar 1 — Gouvernance organisationnelle (30%)**
- Who is the project owner? (name + email)
- What team owns this project?
- Who approves production changes?
- Data classification: public / internal / confidential / restricted?
- Applicable regulations beyond NIS2/SOC2/ISO27001?

**Pillar 2 — Structuration documentaire (20%)**
- Describe the project in one sentence.
- Who are the target users?
- Primary language: FR / EN / both?

**Pillar 3 — Gestion des données (20%)**
- Does the project process personal data (GDPR scope)?
- What data does it store or transmit?
- Data retention period?

**Pillar 4 — Sécurité et accès (20%)**
- Authentication mechanism (OAuth2 / API key / JWT / none)?
- Is this externally facing (internet-exposed)?
- Deployment environments: dev / staging / prod?

**Pillar 5 — Culture et formation (10%)**
- Link to team coding standards (if any)?
- Onboarding doc exists? (yes / no / generate)

### Step 3 — Generated Artifacts

| File | Description |
|------|-------------|
| `GOVERNANCE.md` | Full governance charter (ownership, compliance scope, data classification, contacts, decision log) |
| `README.md` | Project description, setup instructions, architecture overview |
| `SECURITY.md` | Security policy, responsible disclosure, NIS2/SOC2/ISO 27001 checklist |
| `DATA-MANAGEMENT.md` | Data inventory, retention policy, GDPR/privacy notes |
| `CONTRIBUTING.md` | Contribution rules, branch strategy, PR template |
| `ONBOARDING.md` | Team onboarding guide |

All files are generated from Handlebars templates filled with questionnaire answers.

---

## Audit Mode

### Purpose

Scan an existing project and report compliance gaps with actionable fixes.

### Step 1 — Project Scan

The skill scans for:
- Presence/absence of governance files
- Hardcoded secrets, missing auth patterns, exposed endpoints, no `.env.example`
- Documentation quality (empty READMEs, missing sections, no CHANGELOG)
- Data handling (unencrypted storage, missing GDPR notices, no retention policy)
- CI/CD (pipeline presence, security scanning, dependency auditing steps)

### Step 2 — Pillar Scoring

Each pillar is scored 0–100 via weighted checklist mapped to NIS2 / SOC2 / ISO 27001 controls:

| Pillar | Weight | Example controls |
|--------|--------|-----------------|
| Gouvernance organisationnelle | 30% | Owner defined, data classification set, GOVERNANCE.md present |
| Structuration documentaire | 20% | README complete, CHANGELOG exists, ADRs present |
| Gestion des données | 20% | Data inventory documented, GDPR compliance noted, retention policy defined |
| Sécurité et accès | 20% | No hardcoded secrets, auth documented, SECURITY.md present, pentest completed |
| Culture et formation | 10% | CONTRIBUTING.md present, ONBOARDING.md present |

Global score = weighted average of all pillar scores.

### Step 3 — GOVERNANCE-REPORT.md

The report contains:
- Global compliance score (0–100, weighted average)
- Per-pillar score + status: ✅ compliant (≥80) / ⚠️ partial (50–79) / ❌ missing (<50)
- Gaps listed with severity: **critical** / **major** / **minor**
- Actionable fix per gap (exact file to create or section to add)
- NIS2 / SOC2 / ISO 27001 control mapping per finding

---

## Skill File Structure

```
~/.claude/skills/data-governance/
  SKILL.md                        ← slash command definition + agent instructions
  templates/
    GOVERNANCE.md.hbs
    GOVERNANCE-REPORT.md.hbs
    README.md.hbs
    SECURITY.md.hbs
    DATA-MANAGEMENT.md.hbs
    CONTRIBUTING.md.hbs
    ONBOARDING.md.hbs
  checklists/
    nis2.json                     ← NIS2 controls mapped to pillars
    soc2.json                     ← SOC2 Trust Service Criteria
    iso27001.json                 ← ISO 27001 Annex A controls
  project-types/
    web-app.json                  ← extra checks for web apps
    api.json
    service.json
    cli.json
    library.json
    data-pipeline.json
    ml-model.json
```

### SKILL.md Frontmatter

```yaml
name: data-governance
description: >
  Pre-audit or audit a project for data governance compliance across
  organisational governance, documentation, data management, security,
  and culture pillars. Enforces NIS2, SOC2, ISO 27001. Run before
  coding starts or on an existing project.
```

---

## Compliance Mapping

NIS2, SOC2, and ISO 27001 are always enforced. They are embedded as JSON checklists per pillar:

- **NIS2** — Articles 21 & 23: risk management, incident reporting, supply chain security, access control, encryption
- **SOC2** — Trust Service Criteria: Security (CC), Availability (A), Confidentiality (C), Processing Integrity (PI), Privacy (P)
- **ISO 27001** — Annex A controls relevant per project type (A.5 org controls, A.8 tech controls, A.9 access, A.12 ops)

Each gap in the audit report cites the specific control(s) it violates.

---

## Out of Scope

- Automatic code fixes (the skill reports and scaffolds docs; it does not patch source code)
- CI/CD integration (on-demand only; pipeline wiring is a separate concern)
- Real-time monitoring (point-in-time audit only)
