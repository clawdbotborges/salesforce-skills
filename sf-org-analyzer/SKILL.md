---
name: sf-org-analyzer
description: "Analyze a Salesforce org's current configuration to inventory objects, fields, flows, agents, permission sets, and identify gaps. Use when needing to understand what exists in an org before implementing changes, comparing current state vs target design, or auditing org configuration for conflicts and issues."
---

# SF Org Analyzer

Inventory and analyze a Salesforce org's configuration via Setup UI or Metadata API.

## Workflow

1. Log into the target org (browser or API)
2. Inventory relevant components (based on what the design requires)
3. Compare current state vs target design
4. Report gaps, conflicts, and recommendations

## Inventory Methods

### Via Setup UI (Browser)
Navigate to these Setup pages to inventory components:

| Component | Setup Path | Quick Find Search |
|---|---|---|
| Custom Objects | Setup → Object Manager | "Object Manager" |
| Fields | Object Manager → {Object} → Fields & Relationships | — |
| Flows | Setup → Flows | "Flows" |
| Apex Classes | Setup → Apex Classes | "Apex Classes" |
| Permission Sets | Setup → Permission Sets | "Permission Sets" |
| Agents | Setup → Agents | "Agents" or "Agent Studio" |
| Named Credentials | Setup → Named Credentials | "Named Credentials" |
| Custom Metadata | Setup → Custom Metadata Types | "Custom Metadata" |
| Sharing Rules | Setup → Sharing Settings | "Sharing" |
| Connected Apps | Setup → Connected Apps | "Connected Apps" |

### Via Salesforce CLI (sfdx)
If `sf` CLI is available and authenticated:

```bash
# List custom objects
sf sobject list --sobject-type custom

# Describe an object's fields
sf sobject describe --sobject-name Account --json | jq '.result.fields[] | {name, type, label}'

# List flows
sf data query --query "SELECT Id, Label, ProcessType, Status FROM FlowDefinitionView" --json

# List permission sets
sf data query --query "SELECT Id, Label, Name FROM PermissionSet WHERE IsCustom = true" --json

# List active agents
sf data query --query "SELECT Id, MasterLabel, Status FROM BotDefinition" --json
```

### Via Tooling API
For detailed metadata inspection:

```bash
# Query flow versions
sf api request rest '/tooling/query?q=SELECT+Id,MasterLabel,Status,VersionNumber+FROM+Flow' --json

# Query custom fields on an object
sf api request rest '/tooling/query?q=SELECT+Id,DeveloperName,TableEnumOrId+FROM+CustomField+WHERE+TableEnumOrId=%27Contact%27' --json
```

## Gap Analysis Output

```markdown
## Gap Analysis: {Project Name}

### ✅ Already Exists
| Component | Current State | Notes |
|---|---|---|
| {name} | {status/config} | {any differences from target} |

### ❌ Missing — Needs Creation
| Component | Type | Design Reference |
|---|---|---|
| {name} | {object/field/flow/etc} | {link to design section} |

### ⚠️ Conflicts
| Component | Issue | Resolution |
|---|---|---|
| {name} | {what conflicts} | {recommended fix} |

### 📋 Recommendations
- {ordering suggestion}
- {potential issues to watch}
```

## Common Conflicts to Check

- **Duplicate API names:** Flow/field names that already exist (Salesforce appends suffixes like `_v2`)
- **Inactive flows with same name:** Must delete or rename before creating new
- **Permission set overlap:** Multiple PS granting same permissions
- **Field type mismatch:** Existing field is Text but design says Number
- **Validation rules:** May block automation; check for conflicts
- **Record types:** May affect picklist value availability
- **Agent name conflicts:** Can't have two agents with same name
- **Sharing model:** OWD may need adjustment for new objects

## Pre-Implementation Checklist

Before executing any tasks, verify:
- [ ] Logged into correct org (check URL and username)
- [ ] User has System Administrator profile or equivalent
- [ ] No deployment/change set in progress
- [ ] Identify any managed packages that affect target objects
- [ ] Note org edition (limits vary: Developer, Enterprise, Unlimited)
