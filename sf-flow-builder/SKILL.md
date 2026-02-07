---
name: sf-flow-builder
description: "Build and modify Salesforce Flows via browser automation in Flow Builder. Use when creating, editing, or debugging flows in a Salesforce org — covers all element types (Assignment, Decision, Get/Create/Update/Delete Records, Screen, Action), variable and resource creation, field pickers, connector wiring, cut/paste operations, activation, and agent action flow patterns. Handles Shadow DOM navigation, Lightning component quirks, and error recovery."
---

# SF Flow Builder

Comprehensive guide to building Salesforce Flows via browser automation.

## Opening Flow Builder

### Create New Flow
```
1. Navigate to /lightning/setup/Flows/home
2. Click "New Flow"
3. Select flow type:
   - "Start From Scratch" tab → select type
   - Types: Screen Flow, Record-Triggered Flow, Autolaunched Flow, Schedule-Triggered Flow
4. Click "Create"
```

For agent actions: always select **Autolaunched Flow (No Trigger)**.

### Edit Existing Flow
```
1. Navigate to /lightning/setup/Flows/home
2. Find flow in list (search or scroll)
3. Click flow name → opens version list
4. Click version to open in Flow Builder
```

### Direct URL (version-specific, for recovery)
```
/builder_platform_interaction/flowBuilder.app?flowId={flowVersionId}
```
Use this when a flow won't open normally after a bad save.

## Flow Builder Layout

```
┌─────────────────────────────────────────────┐
│ [Save] [Save As] [Debug] [Activate]   (top) │
├──────────┬──────────────────────────────────┤
│ Toolbox  │         Canvas                   │
│ (left)   │   [Start] → [+] → [Element] ... │
│          │                                  │
│ Elements │   Click elements to open         │
│ Manager  │   configuration panel (right)    │
│ Resources│                                  │
├──────────┴──────────────────────────────────┤
│ Status bar (errors, warnings)               │
└─────────────────────────────────────────────┘
```

## Creating Variables & Resources

### New Variable
```
1. In Toolbox (left panel), click "New Resource"
   - Or: Toolbox → Manager tab → "New Resource" button
2. Resource Type: select "Variable"
3. API Name: descriptive name (e.g., Contact_ID, varIsVerified)
4. Data Type: Text / Number / Boolean / Record / Date / DateTime / Currency
5. For Record type: select the Object
6. "Allow multiple values (collection)": check for collections
7. Availability Outside the Flow:
   - "Available for input": check for input variables
   - "Available for output": check for output variables
8. Click "Done"
```

### New Formula
```
1. New Resource → Resource Type: "Formula"
2. API Name, Data Type (usually Text, Number, Boolean, or Date)
3. Enter formula expression
4. Click "Done"
```

### New Constant / Text Template
Same pattern — New Resource → select type → fill fields → Done.

### Common Variables for Agent Flows
| Name | Type | Input | Output | Purpose |
|---|---|---|---|---|
| `Contact_ID` | Text | ✅ | ❌ | Identifier from agent conversation |
| `Error_Message_Output` | Text | ❌ | ✅ | Error message for fault handling |
| `varIsVerified` | Boolean | ❌ | ✅ | Verification result |
| `recordContact` | Record (Contact) | ❌ | ❌ | Holds queried record |

## Adding Elements

### The "+" Button
Every connector between elements (and after the Start element) has a "+" button. Click it to add an element.

```
1. Click "+" at desired position
2. "Add Element" menu appears with options:
   - Assignment, Decision, Get Records, Create Records, Update Records,
     Delete Records, Screen, Action, Subflow, Loop, Collection Sort,
     Collection Filter, Wait, Custom Error
3. Click the element type
4. Configuration panel opens on the right
5. Fill in fields (see element-specific sections below)
6. Click "Done" (bottom of panel) to save element
```

### Element: Assignment
```
Label: {descriptive name}
API Name: {auto-generated from label, or customize}

Set Variable Values:
  Row 1: {variable} | {operator: Equals/Add/Subtract} | {value}
  Row 2: ... (click "Add Assignment" for more rows)
```

**UI pattern for adding rows:**
```
1. Click the variable field → opens resource picker
2. Select variable from list (or search)
3. Operator defaults to "Equals"
4. Click value field → enter literal or select resource
5. Click "+ Add Assignment" for additional rows
```

### Element: Decision
```
Label: {descriptive name}
API Name: {auto-generated}

Outcomes:
  Outcome 1 (rename from "New Outcome"):
    Label: {e.g., "Codes Match"}
    Condition Requirements: All/Any/Custom
    Conditions:
      Resource: {variable/field} | Operator: {Equals/Contains/etc.} | Value: {literal/resource}

  Default Outcome:
    Label: {e.g., "Default - No Match"} (always exists, cannot delete)
```

**Adding outcomes:** Click "+ Add Outcome" at bottom of outcomes list.

**Wiring connectors:** After saving, each outcome creates a path on the canvas. Click "+" on each path to add elements.

### Element: Get Records
```
Label: {descriptive name}
API Name: {auto-generated}

Object: {click to select from dropdown — type to search}

Filter Conditions:
  Row 1: {Field} | {Operator} | {Value}
  Row 2: ...
  Condition Requirements: All Conditions Are Met (AND) / Any (OR)

Sort Order: Ascending/Descending
Sort By: {field}

How Many Records: First Record Only / All Records
How to Store:
  - "Automatically store all fields" (simple but gives agent too much data)
  - "Choose fields and assign variables (advanced)" (preferred for agent flows)
    → Select fields to store
    → Assign to record variable or record collection variable
```

### Element: Create Records
```
Label: {descriptive name}
How Many Records: One / Multiple (from collection)

One record:
  Object: {select}
  Set Field Values:
    Field: {field name} | Value: {literal or resource}

Multiple:
  Record Collection: {collection variable}
```

### Element: Update Records
```
Label: {descriptive name}

How to Find Records:
  - "Use the IDs and all field values from a record or record collection variable"
    → Record or Record Collection: {variable}
  - "Specify conditions to identify records, and set fields individually"
    → Object: {select}
    → Filter Conditions: {field} | {operator} | {value}
    → Set Field Values: {field} | {value}
```

### Element: Screen (Screen Flows only)
```
Label: {descriptive name}
Components: drag from left panel
  - Text, Number, Date, Email, Phone inputs
  - Display Text, Display Image
  - Picklist, Radio Buttons, Checkbox
  - Section, Column layout
```

## Field Picker Navigation

The field picker is a dropdown that appears when selecting fields in conditions, assignments, and record operations. This is the **hardest UI to automate**.

### Standard Pattern
```
1. Click the field picker input/combobox
2. Dropdown opens showing available fields
3. Type to search/filter
4. Click the matching field
```

### When Field Doesn't Appear
- Verify the correct **object context** (e.g., if updating Contact, fields must be Contact fields)
- Check spelling exactly — field labels are case-sensitive in search
- The field must exist in the org — create it first if custom
- Try clearing the search and scrolling the dropdown
- Some fields only appear after selecting the parent object

### Resource Picker (for variables)
```
1. Click the value/resource input
2. Dropdown shows: flow variables, system variables, global constants
3. Type to filter
4. For nested resources: select parent first (e.g., "Running Flow Interview" → "CurrentDate")
```

### System Resources
| Resource | Path | Use For |
|---|---|---|
| Today's date | `{!$Flow.CurrentDate}` | Date comparisons |
| Current date/time | `{!$Flow.CurrentDateTime}` | DateTime comparisons |
| True/False | `{!$GlobalConstant.True}` / `{!$GlobalConstant.False}` | Boolean assignments |
| Current user | `{!$Flow.CurrentRecord}` | In record-triggered context |

In the UI, these appear under:
- **Running Flow Interview** → CurrentDate, CurrentDateTime
- **Global Constants** → True, False, EmptyString

## Cut / Paste / Delete Elements

```
1. Click element on canvas to select it
2. Click "⋮" (three-dots) — labeled "Actions" in the element header
3. Menu shows: Copy Element, Cut Element, Delete Element
4. "Cut Element": element shows dashed border, blue banner says "Select where to paste"
5. All "+" buttons become "Paste element" buttons
6. Click the "Paste element" button at desired position
7. Element moves to new position, connectors rewire automatically
```

**Important:** After cutting, you MUST paste or press Escape. The flow is in a modal state until you do.

## Connector Management

### Default Behavior
- Adding elements auto-creates connectors
- Decision outcomes create labeled paths
- Fault paths appear as dashed red connectors

### Fault Paths
```
1. Hover over a DML element (Get/Create/Update/Delete Records) or Action
2. A small connector point appears at the bottom
3. Drag to create a Fault path
4. Or: after adding the element, the fault connector shows automatically
5. Click "+" on the fault path to add error handling elements
```

## Saving and Activation

### Save
```
1. Click "Save" (top bar)
2. First save: enter Flow Label, Flow API Name, Description
3. Subsequent saves: saves current version
4. If validation errors exist: error panel shows at bottom — fix before saving
```

### Save As New Version
```
1. Click "Save As" → "A New Version"
2. Saves as V(n+1), keeping previous version intact
```

### Activate
```
1. Click "Activate" (only available after saving)
2. Deactivates any previously active version
3. Active flows show green "Active" badge
```

### Debug
```
1. Click "Debug" (top bar)
2. Set input variable values in the debug panel
3. Click "Run"
4. Flow executes step by step — shows path taken and variable values
5. Check output variables for expected results
```

## Agent Action Flow Pattern

The standard pattern for flows used as Agentforce actions:

```
[Start]
  ↓
[Get Records] — lookup by input ID/identifier
  ├── Default → [Decision] — check conditions
  │     ├── Match → [Assignment] — set success outputs
  │     │     ↓
  │     │   [Update Records] — if data changes needed
  │     │     ↓
  │     │   [End]
  │     └── No Match (Default) → [Assignment] — set failure outputs → [End]
  └── Fault → [Assignment: Set Error Message] → [End]
```

Key rules:
- **Autolaunched (No Trigger)** — only type agents can call
- At least 1 input + 1 output variable
- Always include `Error_Message_Output` on fault paths
- Use "Choose fields and assign variables" for Get Records (don't overfeed agent)
- Flow description becomes agent action instructions
- **If you change variables: must delete and recreate the agent action** (snapshot is static)

## Error Recovery

### Flow Won't Open After Bad Save
```
1. Go to /lightning/setup/Flows/home
2. Find the flow, click its name to see versions
3. Find the version ID (starts with "301")
4. Navigate to: /builder_platform_interaction/flowBuilder.app?flowId={versionId}
```

### Duplicate API Name Issues
Salesforce appends `_v2`, `_020526` etc. when duplicates exist.
```
1. Delete ALL versions/duplicates of the element
2. Recreate with the exact API name needed
3. Or: rename the duplicates so the desired name is available
```

### Element Has Invalid Reference
```
1. Open element configuration panel
2. Clear the invalid field reference (shows as null or error)
3. Re-select the correct field/variable
4. Save
```

### Flow Stuck in "Saving..."
```
1. Wait 30 seconds
2. If still stuck: refresh the page
3. Re-open the flow — your last good save should be intact
4. If corrupted: use version URL to open a specific version
```

## Shadow DOM Tips for Automation

### Snapshot Strategy
- Use `refs="aria"` for stable element references in Flow Builder
- Take a fresh snapshot after EVERY action (DOM changes constantly)
- Flow Builder re-renders elements frequently — refs go stale fast

### Common Shadow DOM Paths
```javascript
// Toggle/checkbox in Lightning
document.querySelector('lightning-input')
  .shadowRoot.querySelector('input')

// Radio buttons in flow element config
document.querySelector('lightning-radio-group')
  .shadowRoot.querySelector('input[value="specificValue"]')

// Combobox/dropdown
document.querySelector('lightning-combobox')
  .shadowRoot.querySelector('input')
```

### Keyboard Fallbacks
When click doesn't work on Shadow DOM elements:
- **Radio buttons:** Tab to the group, then ArrowDown/ArrowUp to select
- **Checkboxes:** Tab to the checkbox, then Space to toggle
- **Dropdowns:** Tab to the input, type to filter, ArrowDown to highlight, Enter to select
- **Buttons:** Tab to button, Enter or Space to click

## References

- `references/element-checklist.md` — Quick checklist for each element type
- `references/agent-flow-examples.md` — Complete agent flow specs from real implementations
