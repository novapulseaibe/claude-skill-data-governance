# Data Governance Skill — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a `/data-governance` Claude Code skill that pre-audits or audits any AI-generated project for NIS2, SOC2, and ISO 27001 compliance across 5 governance pillars.

**Architecture:** The skill lives globally at `~/.claude/skills/data-governance/`. It consists of a `SKILL.md` with agent instructions, Handlebars templates for each generated artifact, JSON checklists for compliance frameworks, and per-project-type detection configs. The agent reads these files at runtime to ask questions, fill templates, and score audits.

**Tech Stack:** Markdown (SKILL.md), Handlebars templates (.hbs), JSON (checklists + project-type configs)

---

## File Map

| File | Responsibility |
|------|---------------|
| `~/.claude/skills/data-governance/SKILL.md` | Slash command definition + full agent instructions for both modes |
| `~/.claude/skills/data-governance/templates/GOVERNANCE.md.hbs` | Pre-audit charter filled from questionnaire answers |
| `~/.claude/skills/data-governance/templates/GOVERNANCE-REPORT.md.hbs` | Audit report with scores, gaps, compliance mappings |
| `~/.claude/skills/data-governance/templates/README.md.hbs` | Project README scaffold |
| `~/.claude/skills/data-governance/templates/SECURITY.md.hbs` | Security policy + NIS2/SOC2/ISO27001 checklist |
| `~/.claude/skills/data-governance/templates/DATA-MANAGEMENT.md.hbs` | Data inventory, retention policy, GDPR notes |
| `~/.claude/skills/data-governance/templates/CONTRIBUTING.md.hbs` | Contribution rules, branch strategy, PR template |
| `~/.claude/skills/data-governance/templates/ONBOARDING.md.hbs` | Team onboarding guide |
| `~/.claude/skills/data-governance/checklists/nis2.json` | NIS2 Art.21/23 controls mapped to the 5 pillars |
| `~/.claude/skills/data-governance/checklists/soc2.json` | SOC2 Trust Service Criteria mapped to pillars |
| `~/.claude/skills/data-governance/checklists/iso27001.json` | ISO 27001 Annex A controls mapped to pillars |
| `~/.claude/skills/data-governance/project-types/web-app.json` | Extra checks for web apps |
| `~/.claude/skills/data-governance/project-types/api.json` | Extra checks for APIs |
| `~/.claude/skills/data-governance/project-types/service.json` | Extra checks for backend services |
| `~/.claude/skills/data-governance/project-types/cli.json` | Extra checks for CLIs |
| `~/.claude/skills/data-governance/project-types/library.json` | Extra checks for libraries/SDKs |
| `~/.claude/skills/data-governance/project-types/data-pipeline.json` | Extra checks for data pipelines |
| `~/.claude/skills/data-governance/project-types/ml-model.json` | Extra checks for ML models |

---

## Task 1: Create directory structure

**Files:**
- Create: `~/.claude/skills/data-governance/` (and all subdirectories)

- [ ] **Step 1: Create directories**

```bash
mkdir -p ~/.claude/skills/data-governance/templates
mkdir -p ~/.claude/skills/data-governance/checklists
mkdir -p ~/.claude/skills/data-governance/project-types
```

- [ ] **Step 2: Verify**

```bash
ls -R ~/.claude/skills/data-governance/
```
Expected output:
```
~/.claude/skills/data-governance/:
checklists  project-types  templates
```

- [ ] **Step 3: Commit**

```bash
cd ~/.claude/skills/data-governance
git -C ~/.claude add skills/data-governance
git -C ~/.claude commit -m "feat: scaffold data-governance skill directory"
```

---

## Task 2: Write NIS2 compliance checklist

**Files:**
- Create: `~/.claude/skills/data-governance/checklists/nis2.json`

The checklist maps NIS2 Directive 2022/2555 Articles 21 & 23 controls to the 5 pillars. Each control has an `id`, `control` description, `severity` (critical/major/minor), and `scan` hint (what the agent looks for in the project).

- [ ] **Step 1: Write nis2.json**

```json
{
  "framework": "NIS2",
  "version": "Directive 2022/2555",
  "pillars": {
    "gouvernance_organisationnelle": [
      {
        "id": "NIS2-ART21-1",
        "control": "Risk management policy documented",
        "severity": "critical",
        "scan": "GOVERNANCE.md contains risk management section"
      },
      {
        "id": "NIS2-ART21-2",
        "control": "Incident handling procedures defined",
        "severity": "critical",
        "scan": "SECURITY.md contains incident response section"
      },
      {
        "id": "NIS2-ART21-3",
        "control": "Supply chain security policy defined",
        "severity": "major",
        "scan": "SECURITY.md mentions dependency/supply chain policy"
      },
      {
        "id": "NIS2-ART21-4",
        "control": "Security roles and responsibilities assigned",
        "severity": "critical",
        "scan": "GOVERNANCE.md defines security owner"
      },
      {
        "id": "NIS2-ART23-1",
        "control": "Significant incident reporting procedure documented",
        "severity": "critical",
        "scan": "SECURITY.md contains reporting contact and SLA"
      }
    ],
    "structuration_documentaire": [
      {
        "id": "NIS2-ART21-5",
        "control": "Business continuity and crisis management documented",
        "severity": "major",
        "scan": "README.md or GOVERNANCE.md contains continuity section"
      },
      {
        "id": "NIS2-ART21-6",
        "control": "Security policies reviewed and approved",
        "severity": "major",
        "scan": "GOVERNANCE.md contains policy approval record"
      }
    ],
    "gestion_des_donnees": [
      {
        "id": "NIS2-ART21-7",
        "control": "Data classification applied",
        "severity": "critical",
        "scan": "GOVERNANCE.md defines data classification level"
      },
      {
        "id": "NIS2-ART21-8",
        "control": "Encryption of data in transit and at rest documented",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md or SECURITY.md describes encryption"
      },
      {
        "id": "NIS2-ART21-9",
        "control": "Data retention and disposal policy defined",
        "severity": "major",
        "scan": "DATA-MANAGEMENT.md defines retention period"
      }
    ],
    "securite_et_acces": [
      {
        "id": "NIS2-ART21-10",
        "control": "Access control and authentication policy documented",
        "severity": "critical",
        "scan": "SECURITY.md defines authentication mechanism"
      },
      {
        "id": "NIS2-ART21-11",
        "control": "No hardcoded secrets in source code",
        "severity": "critical",
        "scan": "No API keys, passwords, or tokens in source files; .env.example present"
      },
      {
        "id": "NIS2-ART21-12",
        "control": "Vulnerability management process documented",
        "severity": "major",
        "scan": "SECURITY.md describes patch/vulnerability management"
      },
      {
        "id": "NIS2-ART21-13",
        "control": "Multi-factor authentication used where applicable",
        "severity": "major",
        "scan": "SECURITY.md mentions MFA policy"
      }
    ],
    "culture_et_formation": [
      {
        "id": "NIS2-ART21-14",
        "control": "Security awareness training documented",
        "severity": "minor",
        "scan": "ONBOARDING.md references security training"
      },
      {
        "id": "NIS2-ART21-15",
        "control": "Human resources security procedures defined",
        "severity": "minor",
        "scan": "ONBOARDING.md defines onboarding security steps"
      }
    ]
  }
}
```

- [ ] **Step 2: Validate JSON parses**

```bash
python3 -c "import json; json.load(open(expanduser('~/.claude/skills/data-governance/checklists/nis2.json')))" \
  || node -e "JSON.parse(require('fs').readFileSync(require('os').homedir()+'/.claude/skills/data-governance/checklists/nis2.json','utf8'));console.log('OK')"
```
Expected: `OK` (or no error)

- [ ] **Step 3: Commit**

```bash
git -C ~/.claude add skills/data-governance/checklists/nis2.json
git -C ~/.claude commit -m "feat: add NIS2 compliance checklist"
```

---

## Task 3: Write SOC2 compliance checklist

**Files:**
- Create: `~/.claude/skills/data-governance/checklists/soc2.json`

- [ ] **Step 1: Write soc2.json**

```json
{
  "framework": "SOC2",
  "version": "AICPA 2017 Trust Service Criteria",
  "pillars": {
    "gouvernance_organisationnelle": [
      {
        "id": "CC1.1",
        "control": "Commitment to integrity and ethical values documented",
        "severity": "critical",
        "scan": "GOVERNANCE.md contains code of conduct or ethics statement"
      },
      {
        "id": "CC1.2",
        "control": "Board/management oversight of internal controls defined",
        "severity": "major",
        "scan": "GOVERNANCE.md defines change approval process"
      },
      {
        "id": "CC1.3",
        "control": "Organizational structure with reporting lines documented",
        "severity": "major",
        "scan": "GOVERNANCE.md defines team owner and escalation path"
      },
      {
        "id": "CC2.1",
        "control": "Internal communication of control responsibilities documented",
        "severity": "minor",
        "scan": "CONTRIBUTING.md defines responsibilities"
      }
    ],
    "structuration_documentaire": [
      {
        "id": "CC2.2",
        "control": "External communication policies documented",
        "severity": "minor",
        "scan": "README.md describes public-facing documentation policy"
      },
      {
        "id": "CC4.1",
        "control": "Monitoring activities to evaluate control effectiveness documented",
        "severity": "major",
        "scan": "GOVERNANCE.md or README.md describes monitoring/alerting"
      }
    ],
    "gestion_des_donnees": [
      {
        "id": "P1.1",
        "control": "Privacy notice communicated to data subjects",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md describes privacy notice"
      },
      {
        "id": "P3.1",
        "control": "Personal information collected only for stated purposes",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md lists data collected and purpose"
      },
      {
        "id": "P4.1",
        "control": "Personal information used only for stated purposes",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md defines use limitations"
      },
      {
        "id": "P7.1",
        "control": "Data quality and accuracy maintained",
        "severity": "major",
        "scan": "DATA-MANAGEMENT.md describes data quality measures"
      }
    ],
    "securite_et_acces": [
      {
        "id": "CC6.1",
        "control": "Logical access controls implemented and documented",
        "severity": "critical",
        "scan": "SECURITY.md defines access control policy"
      },
      {
        "id": "CC6.2",
        "control": "Access provisioning and removal process documented",
        "severity": "major",
        "scan": "SECURITY.md describes access management lifecycle"
      },
      {
        "id": "CC6.3",
        "control": "Role-based access documented",
        "severity": "major",
        "scan": "SECURITY.md defines roles and permissions"
      },
      {
        "id": "CC7.1",
        "control": "Vulnerability management process documented",
        "severity": "critical",
        "scan": "SECURITY.md mentions dependency scanning or patch process"
      },
      {
        "id": "CC8.1",
        "control": "Change management process documented",
        "severity": "major",
        "scan": "CONTRIBUTING.md defines PR and review process"
      }
    ],
    "culture_et_formation": [
      {
        "id": "CC1.4",
        "control": "Competency expectations documented",
        "severity": "minor",
        "scan": "ONBOARDING.md defines skill expectations"
      },
      {
        "id": "CC1.5",
        "control": "Accountability for controls enforced",
        "severity": "minor",
        "scan": "CONTRIBUTING.md defines code review requirements"
      }
    ]
  }
}
```

- [ ] **Step 2: Validate JSON parses**

```bash
node -e "JSON.parse(require('fs').readFileSync(require('os').homedir()+'/.claude/skills/data-governance/checklists/soc2.json','utf8'));console.log('OK')"
```
Expected: `OK`

- [ ] **Step 3: Commit**

```bash
git -C ~/.claude add skills/data-governance/checklists/soc2.json
git -C ~/.claude commit -m "feat: add SOC2 compliance checklist"
```

---

## Task 4: Write ISO 27001 compliance checklist

**Files:**
- Create: `~/.claude/skills/data-governance/checklists/iso27001.json`

- [ ] **Step 1: Write iso27001.json**

```json
{
  "framework": "ISO27001",
  "version": "ISO/IEC 27001:2022",
  "pillars": {
    "gouvernance_organisationnelle": [
      {
        "id": "A.5.1",
        "control": "Information security policies defined, approved, and communicated",
        "severity": "critical",
        "scan": "GOVERNANCE.md and SECURITY.md exist and are non-empty"
      },
      {
        "id": "A.5.2",
        "control": "Information security roles and responsibilities assigned",
        "severity": "critical",
        "scan": "GOVERNANCE.md defines security owner"
      },
      {
        "id": "A.5.3",
        "control": "Segregation of duties considered",
        "severity": "major",
        "scan": "CONTRIBUTING.md requires code review by someone other than author"
      },
      {
        "id": "A.5.4",
        "control": "Management responsibilities for information security documented",
        "severity": "major",
        "scan": "GOVERNANCE.md defines change approver"
      }
    ],
    "structuration_documentaire": [
      {
        "id": "A.5.37",
        "control": "Documented operating procedures available",
        "severity": "major",
        "scan": "README.md contains setup and operation instructions"
      },
      {
        "id": "A.5.29",
        "control": "Information security during disruption planned",
        "severity": "major",
        "scan": "GOVERNANCE.md or README.md addresses continuity"
      }
    ],
    "gestion_des_donnees": [
      {
        "id": "A.5.12",
        "control": "Information classified according to security needs",
        "severity": "critical",
        "scan": "GOVERNANCE.md defines data classification"
      },
      {
        "id": "A.5.13",
        "control": "Information labelled according to classification",
        "severity": "major",
        "scan": "DATA-MANAGEMENT.md labels data assets by classification"
      },
      {
        "id": "A.5.14",
        "control": "Information transfer rules documented",
        "severity": "major",
        "scan": "DATA-MANAGEMENT.md describes data transmission policy"
      },
      {
        "id": "A.8.24",
        "control": "Cryptography rules and key management documented",
        "severity": "critical",
        "scan": "SECURITY.md describes encryption approach"
      }
    ],
    "securite_et_acces": [
      {
        "id": "A.8.2",
        "control": "Privileged access rights managed and documented",
        "severity": "critical",
        "scan": "SECURITY.md defines privileged access policy"
      },
      {
        "id": "A.8.3",
        "control": "Access to information restricted per access control policy",
        "severity": "critical",
        "scan": "SECURITY.md defines role-based access"
      },
      {
        "id": "A.8.4",
        "control": "Source code access restricted",
        "severity": "major",
        "scan": "CONTRIBUTING.md defines repository access policy"
      },
      {
        "id": "A.8.7",
        "control": "Protection against malware documented",
        "severity": "major",
        "scan": "SECURITY.md mentions dependency scanning or SAST"
      },
      {
        "id": "A.8.9",
        "control": "Configuration management documented",
        "severity": "major",
        "scan": ".env.example present; no hardcoded secrets"
      },
      {
        "id": "A.8.25",
        "control": "Secure development lifecycle documented",
        "severity": "critical",
        "scan": "CONTRIBUTING.md defines secure coding standards"
      },
      {
        "id": "A.8.29",
        "control": "Security testing in development and acceptance",
        "severity": "critical",
        "scan": "CONTRIBUTING.md or README.md mentions security testing / pentest"
      }
    ],
    "culture_et_formation": [
      {
        "id": "A.6.3",
        "control": "Information security awareness, education, and training provided",
        "severity": "minor",
        "scan": "ONBOARDING.md references security training or resources"
      },
      {
        "id": "A.6.1",
        "control": "Security responsibilities included in employment terms",
        "severity": "minor",
        "scan": "ONBOARDING.md references security responsibilities"
      }
    ]
  }
}
```

- [ ] **Step 2: Validate JSON parses**

```bash
node -e "JSON.parse(require('fs').readFileSync(require('os').homedir()+'/.claude/skills/data-governance/checklists/iso27001.json','utf8'));console.log('OK')"
```
Expected: `OK`

- [ ] **Step 3: Commit**

```bash
git -C ~/.claude add skills/data-governance/checklists/iso27001.json
git -C ~/.claude commit -m "feat: add ISO 27001 compliance checklist"
```

---

## Task 5: Write project-type detection configs

**Files:**
- Create: `~/.claude/skills/data-governance/project-types/web-app.json`
- Create: `~/.claude/skills/data-governance/project-types/api.json`
- Create: `~/.claude/skills/data-governance/project-types/service.json`
- Create: `~/.claude/skills/data-governance/project-types/cli.json`
- Create: `~/.claude/skills/data-governance/project-types/library.json`
- Create: `~/.claude/skills/data-governance/project-types/data-pipeline.json`
- Create: `~/.claude/skills/data-governance/project-types/ml-model.json`

Each file declares: detection signals and extra pillar checks specific to that project type.

- [ ] **Step 1: Write web-app.json**

```json
{
  "type": "web-app",
  "detection_signals": [
    "package.json with react/vue/angular/svelte/next/nuxt dependency",
    "src/ directory with .jsx/.tsx/.vue files",
    "index.html at root or public/"
  ],
  "extra_checks": {
    "securite_et_acces": [
      {
        "id": "WEBAPP-SEC-1",
        "control": "Content Security Policy (CSP) configured",
        "severity": "critical",
        "scan": "CSP header or meta tag present in HTML or server config"
      },
      {
        "id": "WEBAPP-SEC-2",
        "control": "XSS protection documented",
        "severity": "critical",
        "scan": "SECURITY.md mentions XSS prevention"
      },
      {
        "id": "WEBAPP-SEC-3",
        "control": "HTTPS enforced",
        "severity": "critical",
        "scan": "README.md or deployment config enforces HTTPS"
      },
      {
        "id": "WEBAPP-SEC-4",
        "control": "CSRF protection documented",
        "severity": "major",
        "scan": "SECURITY.md or README.md mentions CSRF protection"
      }
    ]
  }
}
```

- [ ] **Step 2: Write api.json**

```json
{
  "type": "api",
  "detection_signals": [
    "package.json with express/fastify/hono/koa/nestjs dependency",
    "go.mod with gin/echo/fiber",
    "pyproject.toml/requirements.txt with fastapi/flask/django",
    "routes/ or controllers/ directory",
    "openapi.yaml or swagger.json present"
  ],
  "extra_checks": {
    "securite_et_acces": [
      {
        "id": "API-SEC-1",
        "control": "API authentication documented (JWT/OAuth2/API key)",
        "severity": "critical",
        "scan": "SECURITY.md defines authentication scheme"
      },
      {
        "id": "API-SEC-2",
        "control": "Rate limiting documented",
        "severity": "major",
        "scan": "README.md or SECURITY.md mentions rate limiting"
      },
      {
        "id": "API-SEC-3",
        "control": "Input validation documented",
        "severity": "critical",
        "scan": "SECURITY.md or CONTRIBUTING.md mentions input validation"
      },
      {
        "id": "API-SEC-4",
        "control": "API versioning strategy documented",
        "severity": "minor",
        "scan": "README.md describes versioning"
      }
    ],
    "gestion_des_donnees": [
      {
        "id": "API-DATA-1",
        "control": "Response data minimisation documented",
        "severity": "major",
        "scan": "DATA-MANAGEMENT.md describes data returned by API"
      }
    ]
  }
}
```

- [ ] **Step 3: Write service.json**

```json
{
  "type": "service",
  "detection_signals": [
    "Dockerfile present",
    "docker-compose.yml present",
    "kubernetes/ or k8s/ directory",
    "package.json with no frontend framework, server entry point"
  ],
  "extra_checks": {
    "securite_et_acces": [
      {
        "id": "SVC-SEC-1",
        "control": "Container security baseline documented",
        "severity": "major",
        "scan": "Dockerfile uses non-root user; SECURITY.md mentions container hardening"
      },
      {
        "id": "SVC-SEC-2",
        "control": "Secret injection via environment (not hardcoded)",
        "severity": "critical",
        "scan": ".env.example present; no secrets in Dockerfile or docker-compose.yml"
      },
      {
        "id": "SVC-SEC-3",
        "control": "Health check endpoint documented",
        "severity": "minor",
        "scan": "README.md describes health check endpoint"
      }
    ]
  }
}
```

- [ ] **Step 4: Write cli.json**

```json
{
  "type": "cli",
  "detection_signals": [
    "package.json with bin field",
    "go.mod with cobra/urfave-cli",
    "pyproject.toml with click/typer",
    "main.go or main.py as entry point"
  ],
  "extra_checks": {
    "securite_et_acces": [
      {
        "id": "CLI-SEC-1",
        "control": "Credentials not stored in plaintext by CLI",
        "severity": "critical",
        "scan": "SECURITY.md describes credential storage approach"
      },
      {
        "id": "CLI-SEC-2",
        "control": "Shell injection prevention documented",
        "severity": "major",
        "scan": "SECURITY.md or CONTRIBUTING.md mentions safe subprocess handling"
      }
    ],
    "structuration_documentaire": [
      {
        "id": "CLI-DOC-1",
        "control": "Man page or --help output documented",
        "severity": "minor",
        "scan": "README.md includes usage examples"
      }
    ]
  }
}
```

- [ ] **Step 5: Write library.json**

```json
{
  "type": "library",
  "detection_signals": [
    "package.json with main/exports field and no app entry point",
    "No Dockerfile or server entry",
    "go.mod without main package",
    "pyproject.toml with build-system but no scripts.start"
  ],
  "extra_checks": {
    "structuration_documentaire": [
      {
        "id": "LIB-DOC-1",
        "control": "Public API documented",
        "severity": "critical",
        "scan": "README.md contains API reference or links to docs"
      },
      {
        "id": "LIB-DOC-2",
        "control": "Versioning and changelog maintained",
        "severity": "major",
        "scan": "CHANGELOG.md present and non-empty"
      },
      {
        "id": "LIB-DOC-3",
        "control": "Breaking change policy documented",
        "severity": "minor",
        "scan": "CONTRIBUTING.md or README.md defines semver policy"
      }
    ],
    "securite_et_acces": [
      {
        "id": "LIB-SEC-1",
        "control": "Dependency vulnerability scanning documented",
        "severity": "major",
        "scan": "CONTRIBUTING.md or README.md mentions npm audit / snyk / dependabot"
      }
    ]
  }
}
```

- [ ] **Step 6: Write data-pipeline.json**

```json
{
  "type": "data-pipeline",
  "detection_signals": [
    "Airflow DAGs directory",
    "Dagster definitions",
    "dbt project.yml",
    "Prefect flow definitions",
    "*.pipeline.py or pipeline/ directory"
  ],
  "extra_checks": {
    "gestion_des_donnees": [
      {
        "id": "PIPE-DATA-1",
        "control": "Data lineage documented",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md describes data sources and transformations"
      },
      {
        "id": "PIPE-DATA-2",
        "control": "PII handling in pipeline documented",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md describes PII masking or exclusion"
      },
      {
        "id": "PIPE-DATA-3",
        "control": "Data quality checks documented",
        "severity": "major",
        "scan": "README.md or DATA-MANAGEMENT.md describes validation rules"
      }
    ],
    "securite_et_acces": [
      {
        "id": "PIPE-SEC-1",
        "control": "Source and destination credentials managed via secrets manager",
        "severity": "critical",
        "scan": ".env.example present; no credentials in pipeline config files"
      }
    ]
  }
}
```

- [ ] **Step 7: Write ml-model.json**

```json
{
  "type": "ml-model",
  "detection_signals": [
    "requirements.txt with torch/tensorflow/sklearn/transformers",
    "*.ipynb files",
    "model/ or models/ directory",
    "train.py or inference.py"
  ],
  "extra_checks": {
    "gestion_des_donnees": [
      {
        "id": "ML-DATA-1",
        "control": "Training data provenance documented",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md describes training dataset origin and licence"
      },
      {
        "id": "ML-DATA-2",
        "control": "PII in training data addressed",
        "severity": "critical",
        "scan": "DATA-MANAGEMENT.md describes PII removal or pseudonymisation"
      },
      {
        "id": "ML-DATA-3",
        "control": "Model card or bias assessment documented",
        "severity": "major",
        "scan": "README.md or MODEL-CARD.md describes model limitations and bias"
      }
    ],
    "securite_et_acces": [
      {
        "id": "ML-SEC-1",
        "control": "Model artefacts stored securely (not in git)",
        "severity": "critical",
        "scan": ".gitignore excludes *.pt, *.pkl, *.h5, *.onnx model files"
      }
    ]
  }
}
```

- [ ] **Step 8: Validate all project-type JSONs**

```bash
for f in ~/.claude/skills/data-governance/project-types/*.json; do
  node -e "JSON.parse(require('fs').readFileSync('$f','utf8'));console.log('OK: $f')"
done
```
Expected: `OK: ...` for all 7 files

- [ ] **Step 9: Commit**

```bash
git -C ~/.claude add skills/data-governance/project-types/
git -C ~/.claude commit -m "feat: add project-type detection configs"
```

---

## Task 6: Write GOVERNANCE.md template

**Files:**
- Create: `~/.claude/skills/data-governance/templates/GOVERNANCE.md.hbs`

Template variables (injected by the agent from questionnaire answers):
`projectName`, `projectDescription`, `owner.name`, `owner.email`, `team`, `approver`, `dataClassification`, `additionalRegulations`, `targetUsers`, `processesPII`, `dataStored`, `retentionPeriod`, `authMechanism`, `externalFacing`, `environments`, `codingStandardsLink`, `projectType`, `date`, `language`

- [ ] **Step 1: Write GOVERNANCE.md.hbs**

```handlebars
# Governance Charter — {{projectName}}

> **Classification:** {{dataClassification}}  
> **Date:** {{date}}  
> **Project type:** {{projectType}}

---

## 1. Ownership & Responsibilities

| Role | Name | Contact |
|------|------|---------|
| Project owner | {{owner.name}} | {{owner.email}} |
| Team | {{team}} | — |
| Change approver | {{approver}} | — |

**Escalation path:** Developer → {{approver}} → CISO

---

## 2. Project Description

{{projectDescription}}

**Target users:** {{targetUsers}}  
**Primary language:** {{language}}  
**External-facing:** {{#if externalFacing}}Yes{{else}}No{{/if}}  
**Environments:** {{environments}}

---

## 3. Data Classification

**Level:** {{dataClassification}}

| Classification | Description |
|----------------|-------------|
| Public | Freely shareable |
| Internal | For internal employees only |
| Confidential | Need-to-know basis |
| Restricted | Regulatory or contractual protection required |

---

## 4. Compliance Scope

This project is subject to:

- [x] NIS2 Directive 2022/2555
- [x] SOC2 Trust Service Criteria
- [x] ISO/IEC 27001:2022
{{#if processesPII}}- [x] GDPR (processes personal data){{/if}}
{{#each additionalRegulations}}- [x] {{this}}
{{/each}}

---

## 5. Data Inventory

**Processes personal data:** {{#if processesPII}}Yes{{else}}No{{/if}}  
**Data stored/transmitted:** {{dataStored}}  
**Retention period:** {{retentionPeriod}}

---

## 6. Security Posture

**Authentication:** {{authMechanism}}  
**Secrets management:** Environment variables (`.env`); never committed to git  

---

## 7. Risk Management

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Data breach | Medium | High | Encryption at rest and in transit; access controls |
| Unauthorised access | Medium | High | {{authMechanism}}; principle of least privilege |
| Supply chain attack | Low | High | Dependency scanning; pinned versions |

*Review this table at each major release.*

---

## 8. Decision Log

| Date | Decision | Rationale | Approver |
|------|----------|-----------|---------|
| {{date}} | Initial governance charter created | Project setup | {{approver}} |

---

## 9. Coding Standards

{{#if codingStandardsLink}}Reference: {{codingStandardsLink}}{{else}}Team standards are defined in CONTRIBUTING.md.{{/if}}

---

*This document must be reviewed and updated at each major release and whenever ownership or compliance scope changes.*
```

- [ ] **Step 2: Commit**

```bash
git -C ~/.claude add skills/data-governance/templates/GOVERNANCE.md.hbs
git -C ~/.claude commit -m "feat: add GOVERNANCE.md template"
```

---

## Task 7: Write GOVERNANCE-REPORT.md template

**Files:**
- Create: `~/.claude/skills/data-governance/templates/GOVERNANCE-REPORT.md.hbs`

Template variables: `projectName`, `date`, `projectType`, `globalScore`, `pillars` (array of `{name, weight, score, status, gaps}`), each gap has `{id, control, severity, framework, fix}`.

- [ ] **Step 1: Write GOVERNANCE-REPORT.md.hbs**

```handlebars
# Governance Audit Report — {{projectName}}

> **Date:** {{date}}  
> **Project type:** {{projectType}}  
> **Global compliance score:** {{globalScore}}/100

---

## Executive Summary

{{#if (gte globalScore 80)}}✅ **COMPLIANT** — Project meets baseline governance requirements.{{/if}}
{{#if (and (gte globalScore 50) (lt globalScore 80))}}⚠️ **PARTIAL** — Project requires remediation before going to production.{{/if}}
{{#if (lt globalScore 50)}}❌ **NON-COMPLIANT** — Project has critical governance gaps. Do not deploy until resolved.{{/if}}

---

## Pillar Scores

| Pillar | Weight | Score | Status |
|--------|--------|-------|--------|
{{#each pillars}}| {{name}} | {{weight}}% | {{score}}/100 | {{status}} |
{{/each}}

---

## Gaps by Pillar

{{#each pillars}}
### {{name}} — {{score}}/100 {{status}}

{{#if gaps}}
| Severity | Control ID | Control | Framework | Fix |
|----------|-----------|---------|-----------|-----|
{{#each gaps}}| **{{severity}}** | {{id}} | {{control}} | {{framework}} | {{fix}} |
{{/each}}
{{else}}
No gaps found.
{{/if}}

{{/each}}

---

## Critical Gaps Summary

The following critical gaps must be resolved before production deployment:

{{#each pillars}}{{#each gaps}}{{#if (eq severity "critical")}}
- **[{{id}}]** {{control}} — *Fix:* {{fix}}
{{/if}}{{/each}}{{/each}}

---

## Compliance Framework Coverage

| Framework | Controls checked | Gaps found |
|-----------|-----------------|------------|
| NIS2 | — | — |
| SOC2 | — | — |
| ISO 27001 | — | — |

*Counts filled in by the agent at audit time.*

---

## Next Steps

1. Address all **critical** gaps immediately.
2. Address **major** gaps before next release.
3. Schedule **minor** gap remediation in the next sprint.
4. Re-run `/data-governance --audit` after remediation.
```

- [ ] **Step 2: Commit**

```bash
git -C ~/.claude add skills/data-governance/templates/GOVERNANCE-REPORT.md.hbs
git -C ~/.claude commit -m "feat: add GOVERNANCE-REPORT.md template"
```

---

## Task 8: Write remaining artifact templates

**Files:**
- Create: `~/.claude/skills/data-governance/templates/README.md.hbs`
- Create: `~/.claude/skills/data-governance/templates/SECURITY.md.hbs`
- Create: `~/.claude/skills/data-governance/templates/DATA-MANAGEMENT.md.hbs`
- Create: `~/.claude/skills/data-governance/templates/CONTRIBUTING.md.hbs`
- Create: `~/.claude/skills/data-governance/templates/ONBOARDING.md.hbs`

- [ ] **Step 1: Write README.md.hbs**

```handlebars
# {{projectName}}

> **Classification:** {{dataClassification}} | **Team:** {{team}} | **Owner:** {{owner.name}}

{{projectDescription}}

---

## Getting Started

### Prerequisites

- Node.js ≥ 18 / Python ≥ 3.11 / Go ≥ 1.21 *(update for actual runtime)*
- Copy `.env.example` to `.env` and fill in values

### Setup

```bash
# Clone and install
git clone <repo-url>
cd {{projectName}}
cp .env.example .env
# install dependencies (npm install / pip install / go mod download)
```

### Run

```bash
# Development
# <add start command>

# Tests
# <add test command>
```

---

## Architecture

*Describe key components and their interactions here.*

---

## Environments

| Environment | URL | Access |
|-------------|-----|--------|
{{#each environments}}| {{this}} | — | {{../team}} |
{{/each}}

---

## Security

See [SECURITY.md](SECURITY.md) for the security policy and responsible disclosure.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## Data & Privacy

See [DATA-MANAGEMENT.md](DATA-MANAGEMENT.md).

---

*Maintained by {{team}} — contact {{owner.email}}*
```

- [ ] **Step 2: Write SECURITY.md.hbs**

```handlebars
# Security Policy — {{projectName}}

> **Owner:** {{owner.name}} ({{owner.email}})  
> **Classification:** {{dataClassification}}  
> **Last reviewed:** {{date}}

---

## Supported Versions

| Version | Supported |
|---------|-----------|
| Latest | ✅ |
| Previous | ⚠️ critical fixes only |

---

## Reporting a Vulnerability

**Do not open a public issue for security vulnerabilities.**

Contact: {{owner.email}}  
Response SLA: 48 hours for acknowledgement, 7 days for remediation plan.

---

## Authentication

**Mechanism:** {{authMechanism}}  
**MFA:** Required for production access.  
**Session management:** Tokens expire after [define duration].

---

## Access Control

- Principle of least privilege applied to all roles.
- Access reviewed quarterly.
- Production access restricted to approved personnel.

---

## Secrets Management

- All secrets injected via environment variables.
- `.env` files never committed to git (`.gitignore` enforces this).
- `.env.example` committed with placeholder values only.
- Secrets rotated on suspected compromise.

---

## Encryption

- Data in transit: TLS 1.2+ enforced.
- Data at rest: AES-256 (or database-level encryption).

---

## Vulnerability Management

- Dependencies scanned with `npm audit` / `pip audit` / `govulncheck` on every PR.
- Critical CVEs patched within 24 hours of disclosure.
- Penetration test required before production launch (see GOVERNANCE.md).

---

## Incident Response

1. Detect → alert {{owner.email}} and {{approver}} immediately.
2. Contain → isolate affected systems within 1 hour.
3. Eradicate → patch root cause.
4. Recover → restore from clean backup.
5. Report → notify affected parties per NIS2 Art.23 within 24 hours (initial) / 72 hours (full).

---

## Compliance Checklists

### NIS2 (Art.21)
- [ ] Risk management policy documented
- [ ] Incident handling procedures defined
- [ ] Supply chain security policy defined
- [ ] Access control and authentication documented
- [ ] No hardcoded secrets in source code
- [ ] Encryption of data in transit and at rest

### SOC2
- [ ] Logical access controls implemented
- [ ] Vulnerability management process documented
- [ ] Change management process documented

### ISO 27001
- [ ] Secure development lifecycle documented
- [ ] Security testing in development and acceptance
- [ ] Cryptography rules documented
```

- [ ] **Step 3: Write DATA-MANAGEMENT.md.hbs**

```handlebars
# Data Management Policy — {{projectName}}

> **Classification:** {{dataClassification}}  
> **Owner:** {{owner.name}} ({{owner.email}})  
> **Last reviewed:** {{date}}

---

## Data Inventory

| Data type | Description | Classification | Storage location | Retention |
|-----------|-------------|----------------|-----------------|-----------|
| {{dataStored}} | — | {{dataClassification}} | — | {{retentionPeriod}} |

*Expand this table for each distinct data type.*

---

## Personal Data (GDPR)

**Processes personal data:** {{#if processesPII}}Yes{{else}}No{{/if}}

{{#if processesPII}}
### Data subjects
*List categories: employees / customers / prospects / etc.*

### Legal basis for processing
*Consent / Legitimate interest / Contract / Legal obligation*

### Privacy notice
*Link to privacy policy shown to data subjects.*

### Data subject rights
- Right to access: contact {{owner.email}}
- Right to erasure: contact {{owner.email}} — processed within 30 days
- Right to portability: data exported in JSON/CSV on request
{{/if}}

---

## Retention & Disposal

| Data type | Retention period | Disposal method |
|-----------|-----------------|-----------------|
| {{dataStored}} | {{retentionPeriod}} | Secure deletion |

---

## Data Transfer

**Transmitted outside EU:** [Yes/No — specify countries]  
**Transfer mechanism:** [Standard Contractual Clauses / Adequacy decision / etc.]

---

## Encryption

- In transit: TLS 1.2+
- At rest: AES-256 or database-level encryption

---

## Backup

- Frequency: [define]
- Retention: [define]
- Tested: [define frequency]
```

- [ ] **Step 4: Write CONTRIBUTING.md.hbs**

```handlebars
# Contributing to {{projectName}}

> **Team:** {{team}} | **Owner:** {{owner.email}}

---

## Branch Strategy

- `main` — production-ready code only. Protected branch.
- `develop` — integration branch.
- Feature branches: `feat/<short-description>`
- Bug fixes: `fix/<short-description>`
- Releases: `release/<version>`

---

## Development Workflow

1. Branch from `develop`.
2. Write failing tests first (TDD).
3. Implement the minimum code to pass.
4. Open a PR against `develop`.
5. Require at least **1 review** (not the author).
6. Merge only after CI passes.

---

## Commit Convention

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add user authentication
fix: correct token expiry calculation
docs: update API reference
chore: bump dependencies
```

---

## Pull Request Checklist

- [ ] Tests written and passing
- [ ] No hardcoded secrets
- [ ] `npm audit` / `pip audit` run — no new critical CVEs
- [ ] CHANGELOG.md updated (for user-facing changes)
- [ ] Documentation updated if API surface changed

---

## Security Requirements

- Never commit secrets, tokens, or passwords.
- Run `npm audit --audit-level=high` before pushing.
- Any security-sensitive change requires sign-off from {{approver}}.

{{#if codingStandardsLink}}
## Coding Standards

Reference: {{codingStandardsLink}}
{{/if}}

---

## Code Review Guidelines

- Review for correctness, security, and readability — in that order.
- Leave constructive, specific comments.
- Approve only what you would ship yourself.
```

- [ ] **Step 5: Write ONBOARDING.md.hbs**

```handlebars
# Onboarding Guide — {{projectName}}

> Welcome to **{{projectName}}**! This guide gets you productive on day one.

---

## Project Overview

{{projectDescription}}

**Team:** {{team}}  
**Owner:** {{owner.name}} ({{owner.email}})  
**Project type:** {{projectType}}

---

## Access Setup

1. Request repository access from {{owner.email}}.
2. Request environment access for: {{environments}}.
3. Copy `.env.example` to `.env` — ask {{owner.name}} for secret values.
4. Enable MFA on your account if not already active.

---

## Local Setup

```bash
git clone <repo-url>
cd {{projectName}}
cp .env.example .env
# Install dependencies:
# npm install / pip install -r requirements.txt / go mod download
```

---

## Key Documents

| Document | Purpose |
|----------|---------|
| [GOVERNANCE.md](GOVERNANCE.md) | Project ownership, compliance scope |
| [SECURITY.md](SECURITY.md) | Security policy and incident response |
| [DATA-MANAGEMENT.md](DATA-MANAGEMENT.md) | Data handling and GDPR |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Development workflow |

---

## Compliance & Security Responsibilities

This project is subject to **NIS2, SOC2, and ISO 27001**. As a contributor you must:

- Never commit secrets or credentials.
- Run security scans before pushing (`npm audit` / `pip audit`).
- Report any suspected security incident to {{owner.email}} immediately.
- Complete your organisation's security awareness training (ask your manager for the link).

---

## Getting Help

- Technical questions: open an issue or message the **{{team}}** Slack channel.
- Security concerns: email {{owner.email}} directly — do not open a public issue.
- Governance questions: contact {{approver}}.
```

- [ ] **Step 6: Commit all templates**

```bash
git -C ~/.claude add skills/data-governance/templates/
git -C ~/.claude commit -m "feat: add governance artifact templates"
```

---

## Task 9: Write SKILL.md — the main agent instructions

**Files:**
- Create: `~/.claude/skills/data-governance/SKILL.md`

This is the most critical file — it gives the agent step-by-step instructions for both modes.

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: data-governance
description: >
  Pre-audit or audit a project for data governance compliance across
  organisational governance, documentation, data management, security,
  and culture pillars. Enforces NIS2, SOC2, ISO 27001. Invoke with
  /data-governance to run, start, audit, or pre-audit a project.
---

# Data Governance Skill

This skill either bootstraps governance artifacts for a new project (pre-audit)
or audits an existing project and produces a compliance report (audit).
NIS2, SOC2, and ISO 27001 are always enforced.

**Skill base directory:** `~/.claude/skills/data-governance/`
**All paths in steps below are relative to this base directory.**

---

## Step 1 — Detect Mode

Check the current working directory:

- If the directory is **empty** or contains only config/dotfiles (no source files):
  → **pre-audit mode**
- If the directory contains source files (`.js`, `.ts`, `.py`, `.go`, `.java`,
  `.rb`, `.rs`, `.cs`, `.html`, `.vue`, `.jsx`, `.tsx`, `.ipynb`, etc.):
  → **audit mode**

Manual overrides:
- `--pre-audit` flag → force pre-audit mode
- `--audit` flag → force audit mode

---

## Step 2 — Detect Project Type

Read the following files if present: `package.json`, `go.mod`, `pyproject.toml`,
`requirements.txt`, `Dockerfile`, `docker-compose.yml`.

Read each file in `project-types/*.json`. Match the `detection_signals` field
(plain English descriptions — use your judgement to match signals to observed files).

Assign **one** project type. If ambiguous, ask the user once:
> "Is this project a web-app, api, service, cli, library, data-pipeline, or ml-model?"

---

## PRE-AUDIT MODE

### Step 3P — Ask Governance Questions

Ask questions **one at a time** in this exact order. Wait for the answer before
asking the next question.

1. "What is the project name?"
2. "Describe the project in one sentence."
3. "Who is the project owner? (name and email)"
4. "Which team owns this project?"
5. "Who approves production changes?"
6. "Data classification: public / internal / confidential / restricted?"
7. "Does this project process personal data subject to GDPR? (yes/no)"
8. (if GDPR yes) "What personal data does it store or transmit?"
9. (if GDPR yes) "What is the data retention period?"
10. (if GDPR no) "What non-personal data does it store or transmit?"
11. "What authentication mechanism will it use? (OAuth2 / JWT / API key / none)"
12. "Is it externally facing (internet-exposed)? (yes/no)"
13. "Which environments? (dev / staging / prod — list all that apply)"
14. "Primary language: FR / EN / both?"
15. "Link to team coding standards? (or press Enter to skip)"
16. "Any additional compliance regulations beyond NIS2/SOC2/ISO27001? (or Enter to skip)"

Store all answers in a context object (call it `answers`).

### Step 4P — Generate Artifacts

For each template in `templates/`, fill it using the `answers` context and write
the result to the current working directory.

Use this mapping:

| Template | Output file |
|----------|------------|
| `templates/GOVERNANCE.md.hbs` | `GOVERNANCE.md` |
| `templates/README.md.hbs` | `README.md` (only if no README exists) |
| `templates/SECURITY.md.hbs` | `SECURITY.md` |
| `templates/DATA-MANAGEMENT.md.hbs` | `DATA-MANAGEMENT.md` |
| `templates/CONTRIBUTING.md.hbs` | `CONTRIBUTING.md` |
| `templates/ONBOARDING.md.hbs` | `ONBOARDING.md` |

Also create `.env.example` with placeholder content if it does not exist:
```
# Copy to .env and fill in values. Never commit .env to git.
# DATABASE_URL=
# SECRET_KEY=
# API_KEY=
```

Also add `.env` to `.gitignore` if not already present.

After generating all files, commit:
```bash
git add GOVERNANCE.md SECURITY.md DATA-MANAGEMENT.md CONTRIBUTING.md ONBOARDING.md .env.example .gitignore
git commit -m "chore: add governance artifacts (NIS2/SOC2/ISO27001)"
```
If README.md was created, include it in the commit.

Report to the user:
> "✅ Pre-audit complete. Generated: GOVERNANCE.md, SECURITY.md, DATA-MANAGEMENT.md,
> CONTRIBUTING.md, ONBOARDING.md, .env.example.
> Next step: fill in the placeholder sections marked with *[define]* in each file."

---

## AUDIT MODE

### Step 3A — Scan Project

Scan the following in the current working directory:

**Governance files presence** (check each exists and is non-empty):
- `GOVERNANCE.md`
- `README.md`
- `SECURITY.md`
- `DATA-MANAGEMENT.md`
- `CONTRIBUTING.md`
- `ONBOARDING.md`
- `CHANGELOG.md`
- `.env.example`
- `.gitignore` (contains `.env`)

**Code scan** (check for):
- Hardcoded secrets: grep for patterns like `password =`, `api_key =`, `secret =`,
  `Bearer `, `token =` in source files (exclude `.env.example`, `*.test.*`, `*.spec.*`)
- Missing `.env.example`: flag if absent

**CI/CD** (check for):
- `.github/workflows/` directory with at least one workflow
- Security scanning step (grep for `audit`, `snyk`, `trivy`, `semgrep` in workflow files)

### Step 4A — Score Each Pillar

For each pillar, load the corresponding controls from all three checklists:
`checklists/nis2.json`, `checklists/soc2.json`, `checklists/iso27001.json`.
Also load extra checks from the detected project type JSON in `project-types/`.

For each control, evaluate the `scan` field description against what you observed
in Step 3A. Mark each control as:
- **PASS** (1 point)
- **FAIL** (0 points)

Pillar score = (passed controls / total controls) × 100, rounded to nearest integer.

Global score = weighted average:
- Gouvernance organisationnelle: 30%
- Structuration documentaire: 20%
- Gestion des données: 20%
- Sécurité et accès: 20%
- Culture et formation: 10%

Status per pillar:
- ≥80 → ✅ compliant
- 50–79 → ⚠️ partial
- <50 → ❌ non-compliant

### Step 5A — Generate Report

Build a context object for `templates/GOVERNANCE-REPORT.md.hbs`:
- `projectName`: from package.json `name` field, or directory name if absent
- `date`: today's date (YYYY-MM-DD)
- `projectType`: detected type
- `globalScore`: computed weighted average
- `pillars`: array with name, weight, score, status, and array of gaps
  - each gap: `{id, control, severity, framework, fix}` where `fix` is
    an actionable instruction (e.g., "Create SECURITY.md with incident response section")

Fill `templates/GOVERNANCE-REPORT.md.hbs` and write to `GOVERNANCE-REPORT.md`.

Commit:
```bash
git add GOVERNANCE-REPORT.md
git commit -m "docs: add governance audit report"
```

Report to the user a summary:
> "📊 Audit complete — global score: {score}/100
> Critical gaps: {count} | Major: {count} | Minor: {count}
> Full report: GOVERNANCE-REPORT.md"

---

## Handlebars Template Rendering

You do not have a Handlebars runtime. Render templates manually:
- Replace `{{variable}}` with the corresponding value from context.
- For `{{#if condition}}...{{/if}}`, include the block only if condition is truthy.
- For `{{#each array}}...{{/each}}`, repeat the block for each item, `{{this}}` = current item.
- For `{{#if (gte a b)}}`, evaluate the comparison inline.
- For `{{#if (and a b)}}`, evaluate both conditions.
- For `{{#if (lt a b)}}`, evaluate less-than.
- Leave any `*[define]*` placeholder text as-is — the user fills those in.
```

- [ ] **Step 2: Commit**

```bash
git -C ~/.claude add skills/data-governance/SKILL.md
git -C ~/.claude commit -m "feat: add data-governance SKILL.md — main agent instructions"
```

---

## Task 10: Validate the skill end-to-end

- [ ] **Step 1: Create a temporary test project**

```bash
mkdir /tmp/test-governance-project
cd /tmp/test-governance-project
git init
```

- [ ] **Step 2: Verify skill is discoverable**

```bash
# In Claude Code, check skill appears
grep -r "name: data-governance" ~/.claude/skills/data-governance/SKILL.md
```
Expected: `name: data-governance`

- [ ] **Step 3: Run pre-audit manually (simulate)**

Open Claude Code in `/tmp/test-governance-project` and run:
```
/data-governance
```

Answer the questionnaire with these test values:
- Project name: `test-governance-project`
- Description: `A test project for validating the data-governance skill`
- Owner name: `Alexis Vervondel`
- Owner email: `owner@example.com`
- Team: `Platform`
- Approver: `Tech Lead`
- Data classification: `internal`
- GDPR: `no`
- Data stored: `application logs`
- Auth: `JWT`
- External facing: `no`
- Environments: `dev, staging, prod`
- Language: `EN`
- Coding standards: *(skip)*
- Additional regulations: *(skip)*

- [ ] **Step 4: Verify generated files exist and are non-empty**

```bash
ls -la /tmp/test-governance-project/
```
Expected: `GOVERNANCE.md`, `SECURITY.md`, `DATA-MANAGEMENT.md`, `CONTRIBUTING.md`, `ONBOARDING.md`, `.env.example` all present.

```bash
wc -l /tmp/test-governance-project/GOVERNANCE.md
```
Expected: ≥30 lines

- [ ] **Step 5: Run audit mode**

Add a source file to the test project to trigger audit mode:
```bash
echo "const x = 1;" > /tmp/test-governance-project/index.js
```

Run `/data-governance --audit` in Claude Code from that directory.

- [ ] **Step 6: Verify GOVERNANCE-REPORT.md is generated**

```bash
ls /tmp/test-governance-project/GOVERNANCE-REPORT.md
head -20 /tmp/test-governance-project/GOVERNANCE-REPORT.md
```
Expected: File exists, contains pillar scores.

- [ ] **Step 7: Clean up test project**

```bash
rm -rf /tmp/test-governance-project
```

- [ ] **Step 8: Commit final state**

```bash
git -C ~/.claude add skills/data-governance/
git -C ~/.claude commit -m "feat: complete data-governance skill — pre-audit, audit, templates, checklists"
```
