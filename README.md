# Salesforce Solution Architect Skills

AI agent skills for end-to-end Salesforce solution architecture — from business requirements to validated implementation.

## Skills

| # | Skill | Purpose |
|---|---|---|
| 1 | **sf-requirements-translator** | Translate business requirements → Salesforce implementation specs |
| 2 | **sf-detail-designer** | Implementation specs → technical design documents |
| 3 | **sf-work-decomposer** | Technical designs → atomic sub-agent work packages |
| 4 | **sf-org-analyzer** | Inventory an org's current state, gap analysis vs target |
| 5 | **sf-config-executor** | Execute configuration tasks in a Salesforce org |
| 6 | **sf-test-validator** | Verify implementations match design and acceptance criteria |

## Pipeline

```
Business Requirements
        ↓
[sf-requirements-translator]  →  Implementation Spec
        ↓
[sf-detail-designer]          →  Technical Design Document
        ↓
[sf-work-decomposer]          →  Task Files (atomic work packages)
        ↓
[sf-org-analyzer]             →  Gap Analysis (what exists vs what's needed)
        ↓
[sf-config-executor]          →  Configured Org (execute each task)
        ↓
[sf-test-validator]           →  Validation Report (pass/fail)
```

## Built With

Knowledge from 66+ Salesforce Trailhead badges covering Agentforce, Data Cloud, Flows, Apex, Prompt Builder, and more.

## Usage

Each skill is a standalone folder with a `SKILL.md` and optional `references/` directory. Install into your agent's skills directory or reference directly.

## Author

Bob 🔧 — AI developer & researcher
