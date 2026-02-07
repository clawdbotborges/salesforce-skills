---
name: sf-requirements-translator
description: "Translate business requirements into Salesforce implementation specifications. Use when given user stories, business needs, or feature requests that need to be mapped to specific Salesforce objects, fields, automations, agents, permissions, and integrations. Covers standard objects, custom objects, flows, Apex, Agentforce agents, Data Cloud, permission sets, sharing rules, and integration patterns."
---

# SF Requirements Translator

Translate natural-language business requirements into structured Salesforce implementation specs.

## Workflow

1. Read the requirements (user stories, business needs, feature descriptions)
2. Classify each requirement by Salesforce domain (see references/decision-matrix.md)
3. Map to specific Salesforce features, objects, and configurations
4. Output a structured implementation spec

## Output Format

For each requirement, produce:

```
### REQ-{n}: {requirement summary}

**Category:** {Data Model | Automation | Agent | Security | Integration | UI}
**Salesforce Feature:** {specific feature/product}
**Components:**
- {Object/Field/Flow/Agent/etc}: {name} — {purpose}

**Dependencies:** {list prerequisites}
**Considerations:** {governor limits, licensing, complexity notes}
```

## Decision Logic

When choosing between implementation approaches, consult `references/decision-matrix.md` for the canonical decision tree covering:
- Flow vs Apex vs Agent Action
- Standard vs Custom Objects
- Permission Sets vs Profiles
- Record-Triggered vs Screen Flows vs Autolaunched
- Agentforce Topics vs Actions vs Knowledge

## Classification Rules

- **CRUD on existing data** → Standard/Custom Objects + Fields
- **Automated business logic** → Flows (prefer) or Apex (complex)
- **User-facing AI interaction** → Agentforce Agent with Topics/Actions
- **Batch/scheduled processing** → Scheduled Flows or Batch Apex
- **External system interaction** → Named Credentials + External Services or Custom Integration
- **Data aggregation/analytics** → Reports, Dashboards, or Data Cloud
- **Access control** → Permission Sets + Sharing Rules
- **UI customization** → Lightning App Builder + LWC

## Handling Ambiguity

When requirements are vague:
1. State assumptions explicitly
2. Offer alternatives with tradeoffs
3. Flag questions that need business input
4. Default to declarative (clicks) over programmatic (code)

## References

- `references/decision-matrix.md` — Feature selection decision trees
- `references/object-patterns.md` — Common object/field patterns for standard use cases
- `references/governor-limits.md` — Key limits that affect architecture decisions
