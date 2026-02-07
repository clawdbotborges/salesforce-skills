---
name: sf-test-validator
description: "Validate Salesforce implementations by verifying configurations exist and work correctly. Use after sf-config-executor to check acceptance criteria — verify objects, fields, flows, agents, and permissions are properly configured. Runs agent conversations, checks Setup pages, and reports pass/fail results with evidence."
---

# SF Test Validator

Verify that Salesforce configurations match the technical design and acceptance criteria.

## Workflow

1. Read the task file and its acceptance criteria
2. For each criterion, navigate to the relevant Setup page or run the relevant test
3. Capture evidence (screenshot or element verification)
4. Report pass/fail with details

## Verification Methods

### Object/Field Existence
```
1. Navigate to: /lightning/setup/ObjectManager/{ObjectApiName}/FieldsAndRelationships/view
2. Search for field name
3. Verify: field exists, correct type, correct properties
```

Or via CLI:
```bash
sf sobject describe --sobject-name {Object} --json | jq '.result.fields[] | select(.name == "{FieldApiName}")'
```

### Flow Verification
```
1. Navigate to: /lightning/setup/Flows/home
2. Search for flow name
3. Click to open — verify:
   - Status (Active/Inactive)
   - Element count and types match design
   - Variables match expected inputs/outputs
   - Connectors follow expected paths
```

### Flow Debug Run
```
1. Open flow in Flow Builder
2. Click "Debug" button
3. Set input values per test scenario
4. Run and verify:
   - Path taken matches expected
   - Output values are correct
   - No errors
```

### Agent Verification
```
1. Navigate to Agent Studio (Quick Find → "Agents")
2. Open the agent
3. Verify:
   - Topics exist with correct names
   - Actions are assigned to topics
   - Instructions match design
   - Agent status (Active)
```

### Agent Conversation Test
```
1. Open the agent in Agent Builder
2. Click "Test" or use the conversation panel
3. Send test messages per scenario:
   - Happy path: valid inputs → expected response
   - Error path: invalid inputs → appropriate handling
   - Edge case: boundary values
4. Verify agent selects correct topic and actions
```

### Permission Set Verification
```
1. Navigate to: /lightning/setup/PermSets/home
2. Open the permission set
3. Verify:
   - Object permissions match design
   - Field-level security matches design
   - Assigned to correct users
```

## Test Report Format

```markdown
# Validation Report: {Project/Task Name}

**Org:** {org URL}
**Date:** {date}
**Overall:** {PASS | FAIL | PARTIAL}

## Results

| # | Criterion | Status | Evidence |
|---|---|---|---|
| 1 | {acceptance criterion} | ✅ PASS | {what was verified} |
| 2 | {acceptance criterion} | ❌ FAIL | {what was wrong} |
| 3 | {acceptance criterion} | ⚠️ SKIP | {why skipped} |

## Issues Found
- {issue description + recommended fix}

## Test Scenarios Executed
| Scenario | Input | Expected | Actual | Status |
|---|---|---|---|---|
| {name} | {test data} | {expected result} | {actual result} | ✅/❌ |
```

## Common Validation Checks

### Field Created Correctly
- Label matches design
- API name matches exactly (no suffixes)
- Data type is correct
- Required/unique/external ID flags match
- Default value if specified

### Flow Works Correctly
- Activates without errors
- Debug run follows expected path
- Variables populated correctly
- DML operations succeed
- Error/fault handling works

### Agent Works Correctly
- Recognizes topic from user message
- Selects correct action
- Collects required input from user
- Returns expected output
- Handles unknown queries gracefully (escalation or fallback)

### Permission Set Grants Access
- Users with PS can access the objects/fields
- Users without PS cannot access (negative test)
- Agent user has PS assigned and can execute flows
