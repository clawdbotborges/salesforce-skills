---
name: sf-detail-designer
description: "Create detailed Salesforce technical design documents from implementation specs. Use when translating high-level Salesforce requirements into precise technical designs including object/field definitions with API names, flow element-by-element specifications, Agentforce agent/topic/action configurations, permission set definitions, and integration specs. Outputs implementation-ready markdown."
---

# SF Detail Designer

Transform implementation specs into technical design documents ready for execution.

## Workflow

1. Read the implementation spec (from sf-requirements-translator or manual input)
2. For each component, produce a detailed design (see templates below)
3. Validate cross-references (fields referenced in flows exist, actions reference valid objects)
4. Output a single design document organized by component type

## Output Structure

```markdown
# Technical Design: {Project Name}

## 1. Data Model
{Object and field definitions}

## 2. Automations
{Flow and Apex designs}

## 3. Agents
{Agentforce configurations}

## 4. Security
{Permission sets, sharing rules}

## 5. Integration
{External service configs}

## 6. Dependency Order
{Build sequence}
```

## Component Templates

### Object/Field Definition

For each custom object or field, specify:

```
**Object:** {Label} ({API_Name__c})
- Record Name: {Auto Number format or Text}
- Sharing: {Private | ReadWrite | Controlled by Parent}
- Deployment: {Metadata API | Setup UI}

| Field Label | API Name | Type | Details |
|---|---|---|---|
| {label} | {API__c} | {type} | {length, picklist values, formula, required, unique, external ID, default} |
```

Rules for API names:
- Custom objects end in `__c`
- Custom fields end in `__c`
- Use PascalCase with underscores: `Last_Customer_Verification__c`
- Junction objects: `{Parent1}{Parent2}__c` or descriptive name
- Max 40 chars for object API name, 40 chars for field API name

### Flow Design

For each flow, specify:

```
**Flow:** {Label}
- API Name: {API_Name}
- Type: {Screen | Record-Triggered | Autolaunched | Scheduled}
- Run As: {System | User}
- Trigger: {Object, when created/updated, conditions}

**Variables:**
| Name | Type | Available for Input/Output |
|---|---|---|

**Elements (in order):**
1. {Type}: {Label} ({API_Name})
   - {configuration details}
   - {conditions, formulas, assignments}
2. ...

**Connectors:**
- {Element} → {Element} (condition: {label})
```

Element types: Assignment, Decision, Get Records, Create Records, Update Records, Delete Records, Screen, Action, Subflow, Loop, Collection Sort/Filter.

### Agentforce Agent Design

```
**Agent:** {Name}
- Description: {purpose}
- User: {which user runs the agent}
- Channel: {Web, Slack, Mobile, API}

**Topic:** {Name}
- Scope: {natural language description}
- Instructions: |
    {multi-line natural language instructions}

**Actions:**
| Label | Type | Input Params | Output Params |
|---|---|---|---|
| {name} | {Flow/Apex/External} | {param: collect/derive} | {param: show/internal} |
```

### Permission Set Design

```
**Permission Set:** {Label} ({API_Name})
- Description: {purpose}
- Object Permissions:
  | Object | Read | Create | Edit | Delete | View All | Modify All |
  |---|---|---|---|---|---|---|
- Field-Level Security:
  | Object.Field | Read | Edit |
  |---|---|---|
- System Permissions: {list any}
- Tab Settings: {list any}
```

## Validation Checklist

Before finalizing, verify:
- [ ] All fields referenced in flows exist in data model section
- [ ] All objects referenced in agent actions exist
- [ ] Permission sets grant access to all objects/fields used by flows and agents
- [ ] API names follow naming conventions and are unique
- [ ] Flow variables match action input/output types
- [ ] No circular dependencies in build order

## References

- Consult `references/naming-conventions.md` for API name standards
- Consult `references/flow-patterns.md` for common flow element patterns
