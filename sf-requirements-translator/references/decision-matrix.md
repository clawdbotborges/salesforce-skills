# Salesforce Feature Decision Matrix

## Automation: Flow vs Apex vs Agent Action

```
Need automation?
├── User interaction required?
│   ├── Yes → Screen Flow
│   └── No → Is it triggered by a record change?
│       ├── Yes → Record-Triggered Flow
│       │   ├── Before save (field defaults, validation) → Fast Field Update
│       │   └── After save (create related, callout) → After-Save Flow
│       └── No → Is it scheduled/batch?
│           ├── Yes → Scheduled Flow or Batch Apex (>50k records)
│           └── No → Is it called by an agent?
│               ├── Yes → Agent Action (Flow or Apex Invocable)
│               └── No → Autolaunched Flow or Platform Event-Triggered Flow
```

### When Apex is Required
- Complex data transformations across multiple objects in a single transaction
- Callouts with custom retry/error handling beyond HTTP Action capability
- Governor limit optimization (bulk patterns, SOQL for-loops)
- Custom REST/SOAP API endpoints
- Complex business logic with recursion or dynamic behavior
- Invocable methods for Agentforce actions needing complex logic

### When Flow is Preferred
- Simple to moderate CRUD operations
- Decision logic with few branches
- Screen-based user input collection
- Scheduled tasks with <50k records
- Agent actions with straightforward input/output

## Object Selection: Standard vs Custom

```
Does a standard object fit?
├── Yes → Use standard object (Account, Contact, Case, Opportunity, Lead, etc.)
│   └── Need extra fields? → Custom fields on standard object
├── Partial → Junction object or custom fields to extend
└── No → Custom object
    ├── Related to a person? → Consider Person Account or Custom obj + Contact lookup
    ├── Tracking events/logs? → Custom object with Master-Detail to parent
    └── Configuration/settings? → Custom Metadata Type or Custom Setting
```

### Standard Object Quick Reference
| Business Concept | Standard Object | Notes |
|---|---|---|
| Customer/Company | Account | Enable Person Accounts for B2C |
| Person | Contact (B2B), Lead (prospect), Person Account (B2C) | |
| Sales deal | Opportunity + Opportunity Products | |
| Support ticket | Case | Auto-assignment rules available |
| Product catalog | Product2 + PricebookEntry | Multi-currency support |
| Task/Activity | Task, Event | Polymorphic WhoId/WhatId |
| Document/File | ContentDocument + ContentVersion | |
| Knowledge | Knowledge__kav | For agent grounding |

## Security: Permission Sets vs Profiles

```
Always use Permission Sets (PS) and Permission Set Groups (PSG).
├── New org → Minimum Profile + Permission Sets
├── Existing org → Migrate Profile permissions to PS over time
└── Agent/Integration user → Dedicated Permission Set with minimum access
```

Rules:
- One Permission Set per feature/function
- Group related PS into Permission Set Groups
- Never edit standard profiles — clone if needed
- Field-Level Security → Permission Sets
- Object CRUD → Permission Sets
- Record access → OWD + Sharing Rules + Manual Shares

## Agentforce: Topics vs Actions vs Knowledge

```
Building an agent?
├── Define the job → Topic (natural language scope + instructions)
│   ├── What tools does it need? → Actions (Flow or Apex Invocable)
│   │   ├── Simple data lookup/update → Flow Action
│   │   ├── Complex logic → Apex Invocable Action
│   │   └── External API call → External Service Action or Apex
│   ├── What does it need to know? → Knowledge
│   │   ├── Structured data → CRM objects (grounding)
│   │   └── Unstructured → Knowledge Articles, Files, Data Cloud
│   └── Where does it work? → Channel (Web, Slack, Mobile, API)
└── Guardrails → Topic instructions + Einstein Trust Layer
```

### Agent Action Design Rules
- Each action = one clear purpose (single responsibility)
- Input parameters: mark as "Collect from user" or "Derive from conversation"
- Output parameters: mark as "Show in conversation" for user-facing data
- Keep action count per topic ≤10 for optimal reasoning
- Use descriptive labels — the LLM uses them to select actions

## Integration Patterns

```
Need external data?
├── Real-time, simple → External Services (OpenAPI spec) + Named Credential
├── Real-time, complex → Custom Apex callout + Named Credential
├── Near real-time → Platform Events
├── Batch/bulk → Bulk API or MuleSoft
├── Salesforce-to-Salesforce → Salesforce Connect or Event-Based
└── File-based → Custom import with Bulk API
```

Authentication:
- Always use Named Credentials (never hardcode secrets)
- OAuth 2.0 preferred for user context
- Client Credentials for server-to-server
- JWT Bearer for headless integration
