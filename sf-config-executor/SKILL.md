---
name: sf-config-executor
description: "Execute Salesforce configuration tasks in an org via browser automation or CLI. Use when implementing work packages from sf-work-decomposer — creating objects, fields, flows, agents, permission sets, and other org configurations. Handles Salesforce Lightning UI patterns including Shadow DOM navigation, Setup menus, and Flow Builder interactions."
---

# SF Config Executor

Execute Salesforce configuration tasks in a target org. Handles Lightning UI automation patterns.

## Workflow

1. Read the task file (from sf-work-decomposer output)
2. Read checkpoint.json — skip if already complete
3. Log into the org if needed
4. Execute the configuration steps
5. Verify acceptance criteria
6. Update checkpoint.json

## Login Pattern

```
1. Navigate to {org_url}
2. Enter username (type slowly for validation)
3. Enter password (type slowly — `slowly: true` is critical)
4. Handle MFA/verification if prompted
5. Verify login success (check for Setup or Home page)
```

**Critical:** Always use `slowly: true` for password fields — Salesforce client-side validation drops characters otherwise.

## Setup Navigation

### Quick Find (preferred)
```
1. Navigate to /lightning/setup/SetupOneHome/home
2. Click Quick Find search box (usually in left sidebar)
3. Type the feature name
4. Click the matching result
```

### Direct URL patterns
| Feature | URL Pattern |
|---|---|
| Object Manager | `/lightning/setup/ObjectManager/home` |
| Specific Object | `/lightning/setup/ObjectManager/{ObjectName}/FieldsAndRelationships/view` |
| Flows | `/lightning/setup/Flows/home` |
| Permission Sets | `/lightning/setup/PermSets/home` |
| Agents / Agent Studio | `/lightning/setup/AgentStudio/home` (may 404 — use Quick Find) |
| Sharing Settings | `/lightning/setup/SecuritySharing/home` |

### Fallback Navigation
If Quick Find or direct URL fails:
1. Click Setup gear icon
2. Use the left sidebar tree: Platform Tools → Process Automation → Flows
3. Or search "Take Me There" banners on Setup Home

## Shadow DOM Patterns

Salesforce Lightning uses nested Shadow DOM. To interact with elements inside shadow roots:

### JavaScript Evaluation (most reliable)
```javascript
// Toggle switches
document.querySelector('records-record-layout-event-broker')
  .shadowRoot.querySelector('records-lwc-record-layout')
  .shadowRoot.querySelector('lightning-toggle')
  .shadowRoot.querySelector('input').click();

// Input fields
document.querySelector('lightning-input')
  .shadowRoot.querySelector('input').value = 'new value';
```

### Browser Tool Pattern
```
1. Take snapshot with refs="aria" for stable references
2. Use act with ref to interact
3. If element not found: try evaluate with JS shadow root traversal
4. If still stuck: Tab navigation (Tab + Space for checkboxes, Tab + ArrowDown for dropdowns)
```

## Flow Builder Interactions

Flow Builder is the most complex UI. Key patterns:

### Adding Elements
```
1. Click "+" button between elements
2. Select element type from menu (e.g., "Assignment", "Decision", "Update Records")
3. Fill in Label (API Name auto-generates)
4. Configure element-specific fields
5. Click "Done" or close panel
```

### Field Pickers (dropdowns)
```
1. Click the field picker input
2. Type to search (if searchable)
3. Select from dropdown — may need to scroll
4. If field not found: verify object context, check spelling, ensure field exists
```

### Flow Resources
```
1. Click "New Resource" in Toolbox (left panel)
2. Select resource type (Variable, Formula, Constant, etc.)
3. Fill in details
4. Click "Done"
```

### Cut/Paste Elements
```
1. Click element on canvas to select
2. Click three-dots (⋮) "Actions" menu
3. Select "Cut Element"
4. Click the "Paste element" button at desired location
```

### Save and Activate
```
1. Click "Save" (top right) — resolves validation errors
2. Fix any errors shown
3. Click "Activate" when ready
```

## Common Pitfalls

1. **Duplicate API names:** Salesforce silently appends `_v2`, `_020526`, etc. Delete duplicates and recreate with exact name.
2. **Flow saves with invalid references:** Can break the flow editor. Use version URL (`/builder_platform_interaction/flowBuilder.app?flowId={versionId}`) to recover.
3. **Field picker shows wrong fields:** Ensure the correct object is selected in the resource/element configuration.
4. **Radio buttons in Setup:** May need Tab + Arrow keys instead of direct click.
5. **Page doesn't load after navigation:** Wait for Lightning spinner to finish. Use `act: wait` with `textGone` for spinner text.
6. **MFA prompts:** Check email for verification codes.

## Checkpoint Update

After each task completes:

```json
// tasks/checkpoint.json
{
  "task": 1,
  "status": "complete",
  "timestamp": "2026-02-07T09:00:00Z",
  "notes": "Created Last_Customer_Verification__c field on Contact"
}
```

On failure:
```json
{
  "task": 1,
  "status": "failed",
  "timestamp": "2026-02-07T09:00:00Z",
  "error": "Field picker could not find Custom_Field__c — field may not exist yet"
}
```
