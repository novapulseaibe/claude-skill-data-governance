# `/data-governance` — Claude Code Skill

A Claude Code skill that enforces data governance best practices on any AI-generated project. Run it **before you write a single line of code** to establish governance foundations, or **on an existing project** to score compliance and surface gaps.

Compliance frameworks enforced automatically: **NIS2** (Directive 2022/2555), **SOC2** (AICPA Trust Service Criteria), **ISO/IEC 27001:2022**.

---

## Quick Start

Open Claude Code in any project directory and type:

```
/data-governance
```

That's it. The skill detects whether you're starting fresh or auditing an existing project, and takes it from there.

---

## Two Modes

### Pre-audit mode — start here, before coding

**Triggers automatically** when the directory is empty or contains no source files.

The skill asks you 16 short questions (one at a time) covering ownership, data classification, authentication, compliance scope, and team contacts. Based on your answers, it generates **6 governance documents** and commits them to git.

```
/data-governance           # auto-detects empty directory → pre-audit
/data-governance --pre-audit   # force pre-audit on an existing directory
```

**What you answer:**

| # | Question | Example answer |
|---|----------|---------------|
| 1 | Project name | `billing-api` |
| 2 | One-sentence description | `REST API that handles invoice generation and payment processing` |
| 3 | Owner name & email | `Jane Doe / jane@cluepoints.com` |
| 4 | Team | `Platform` |
| 5 | Change approver | `Tech Lead` |
| 6 | Data classification | `confidential` |
| 7 | Processes personal data (GDPR)? | `yes` |
| 8 | What personal data? | `customer names, email addresses, payment metadata` |
| 9 | Retention period | `7 years (legal requirement)` |
| 10 | Authentication mechanism | `JWT` |
| 11 | Externally facing? | `yes` |
| 12 | Environments | `dev, staging, prod` |
| 13 | Primary language | `EN` |
| 14 | Coding standards link | *(skip)* |
| 15 | Additional regulations | `PCI-DSS` |

---

**What gets generated:**

| File | Contents |
|------|---------|
| `GOVERNANCE.md` | Full governance charter — ownership table, escalation path, data classification, compliance scope, risk register, decision log |
| `SECURITY.md` | Security policy — authentication, access control, secrets management, encryption, incident response procedure, NIS2/SOC2/ISO 27001 checklists |
| `DATA-MANAGEMENT.md` | Data inventory, GDPR data subject rights, retention & disposal table, encryption policy, backup policy |
| `CONTRIBUTING.md` | Branch strategy, development workflow (TDD), commit convention, PR checklist, security requirements |
| `ONBOARDING.md` | Day-one guide — access setup, local setup, key documents table, compliance responsibilities |
| `README.md` | Project README (only if none exists) — description, setup, environments table, links to governance docs |
| `.env.example` | Secrets template with placeholder keys (created if absent) |
| `.gitignore` | `.env` added if not already excluded |

Everything is committed in a single `chore: add governance artifacts (NIS2/SOC2/ISO27001)` commit.

---

### Audit mode — scan an existing project

**Triggers automatically** when the directory contains source files.

The skill scans your project, scores each of the 5 governance pillars against NIS2, SOC2, and ISO 27001 controls, and produces a compliance report.

```
/data-governance           # auto-detects source files → audit
/data-governance --audit   # force audit mode
```

**What gets scanned:**

- Presence and content of `GOVERNANCE.md`, `README.md`, `SECURITY.md`, `DATA-MANAGEMENT.md`, `CONTRIBUTING.md`, `ONBOARDING.md`, `CHANGELOG.md`, `.env.example`, `.gitignore`
- Source files for hardcoded secrets (`password =`, `api_key =`, `token =`, etc.)
- `.github/workflows/` for CI/CD presence and security scanning steps (`audit`, `snyk`, `trivy`, `semgrep`)
- Project-type-specific checks (see [Supported Project Types](#supported-project-types))

**What gets generated:**

`GOVERNANCE-REPORT.md` — a full compliance report containing:

```
# Governance Audit Report — billing-api

Global compliance score: 62/100

## Pillar Scores
| Pillar                        | Weight | Score  | Status     |
|-------------------------------|--------|--------|------------|
| Gouvernance organisationnelle | 30%    | 45/100 | ❌ non-compliant |
| Structuration documentaire    | 20%    | 80/100 | ✅ compliant     |
| Gestion des données           | 20%    | 60/100 | ⚠️ partial       |
| Sécurité et accès             | 20%    | 55/100 | ⚠️ partial       |
| Culture et formation          | 10%    | 90/100 | ✅ compliant     |

## Gaps by Pillar
### Gouvernance organisationnelle — 45/100 ❌
| Severity | Control ID    | Control                              | Framework | Fix                                              |
|----------|--------------|--------------------------------------|-----------|--------------------------------------------------|
| critical | NIS2-ART21-1 | Risk management policy documented    | NIS2      | Add a Risk Management section to GOVERNANCE.md   |
| critical | A.5.1        | Information security policies defined| ISO 27001 | Create GOVERNANCE.md and SECURITY.md             |
| major    | CC1.2        | Management oversight defined         | SOC2      | Add change approval process to GOVERNANCE.md     |

## Critical Gaps Summary
- [NIS2-ART21-1] Risk management policy documented — Fix: Add Risk Management section to GOVERNANCE.md
- [A.5.1] Information security policies defined — Fix: Create GOVERNANCE.md and SECURITY.md
...
```

The report is committed as `docs: add governance audit report`.

**Console summary:**
```
📊 Audit complete — global score: 62/100
Critical gaps: 4 | Major: 7 | Minor: 2
Full report: GOVERNANCE-REPORT.md
```

---

## Pillar Scoring

The global score is a weighted average of 5 pillars:

| Pillar | Weight | What it checks |
|--------|--------|---------------|
| **Gouvernance organisationnelle** | 30% | Owner defined, escalation path, risk management, data classification, incident reporting |
| **Structuration documentaire** | 20% | README complete, CHANGELOG present, operating procedures documented |
| **Gestion des données** | 20% | Data inventory, GDPR compliance, retention policy, encryption documented |
| **Sécurité et accès** | 20% | No hardcoded secrets, auth documented, access control, SAST/dependency scanning |
| **Culture et formation** | 10% | CONTRIBUTING.md, ONBOARDING.md, security training referenced |

**Score thresholds:**
- ✅ **≥ 80** — Compliant
- ⚠️ **50–79** — Partial — remediation required before production
- ❌ **< 50** — Non-compliant — do not deploy

---

## Supported Project Types

The skill auto-detects project type from manifest files and applies additional checks:

| Type | Detection signals | Extra checks |
|------|------------------|--------------|
| `web-app` | React/Vue/Angular/Svelte/Next/Nuxt in `package.json` | CSP, XSS, HTTPS, CSRF |
| `api` | Express/FastAPI/Gin/NestJS, `routes/` or `openapi.yaml` | Auth, rate limiting, input validation, versioning |
| `service` | `Dockerfile`, `docker-compose.yml`, k8s dir | Container hardening, secret injection, health check |
| `cli` | `bin` field in `package.json`, Cobra/Click/Typer | Credential storage, shell injection |
| `library` | `main`/`exports` in `package.json`, no server entry | API docs, CHANGELOG, semver policy |
| `data-pipeline` | Airflow/Dagster/dbt/Prefect configs | Data lineage, PII in pipeline, data quality |
| `ml-model` | `*.ipynb`, torch/sklearn/transformers in requirements | Training data provenance, PII removal, model card, artefact exclusion from git |

---

## Compliance Coverage

Every audit maps findings to specific controls:

**NIS2** (Directive 2022/2555, Art. 21 & 23)
- Risk management, incident handling, supply chain security, access control, encryption, MFA, vulnerability management, incident reporting SLA

**SOC2** (AICPA 2017 Trust Service Criteria)
- CC1–CC8 (Security), P1–P7 (Privacy): integrity, oversight, logical access, vulnerability management, change management, data purpose limitation

**ISO/IEC 27001:2022** (Annex A)
- A.5 Organisational controls, A.6 HR controls, A.8 Technological controls: classification, cryptography, access management, secure SDLC, security testing, configuration management

---

## Workflow Integration

**Recommended workflow for new AI-generated projects:**

```
1. AI generates your project scaffold
2. /data-governance          ← pre-audit: governance foundations
3. Start coding
4. /data-governance --audit  ← before first production deploy
5. Fix critical gaps
6. /data-governance --audit  ← verify remediation
```

**Re-running audit:** Safe to run multiple times. Each run produces a fresh `GOVERNANCE-REPORT.md` reflecting the current state.

---

## Skill Location

The skill is installed globally and available in every project:

```
~/.claude/skills/data-governance/
  SKILL.md                    ← agent instructions
  templates/                  ← Handlebars templates for generated files
  checklists/                 ← NIS2, SOC2, ISO 27001 control definitions
  project-types/              ← Per-type extra checks
```

---

## Files in This Repository

```
Skill - Data Governance/
  README.md                                        ← this file
  docs/
    superpowers/
      specs/2026-06-04-data-governance-design.md   ← design specification
      plans/2026-06-04-data-governance-skill.md    ← implementation plan
```
