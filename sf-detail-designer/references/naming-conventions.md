# Salesforce Naming Conventions

## Objects
- Custom objects: `PascalCase__c` (e.g., `Booking_Request__c`)
- Junction objects: descriptive name (e.g., `Contact_Campaign__c`)
- Max 40 characters for API name

## Fields
- Custom fields: `PascalCase_With_Underscores__c`
- Boolean fields: `Is_` or `Has_` prefix (e.g., `Is_Verified__c`)
- Date fields: descriptive + `_Date__c` suffix (e.g., `Last_Verification_Date__c`)
- Count/number fields: descriptive (e.g., `Total_Amount__c`)
- External IDs: `External_ID__c` or `{System}_ID__c`

## Flows
- API Name: `PascalCase_Descriptive` (e.g., `Verify_Customer_Identity`)
- Variables: `camelCase` or `PascalCase` — be consistent within a flow
- Resources: prefix with type context (e.g., `recordContact`, `varIsVerified`)
- Decision outcomes: descriptive labels (e.g., `Codes_Match`, `Default_No_Match`)

## Apex
- Classes: `PascalCase` (e.g., `CustomerVerificationHandler`)
- Methods: `camelCase` (e.g., `verifyCustomerCode`)
- Test classes: `{ClassName}_Test` or `{ClassName}Test`
- Invocable methods: clear `@InvocableMethod(label='' description='')`

## Agents
- Agent names: descriptive, user-facing (e.g., `Customer Service Agent`)
- Topics: action-oriented (e.g., `Order Management`, `Identity Verification`)
- Actions: verb + noun (e.g., `Look Up Order`, `Verify Customer Code`)

## Permission Sets
- API Name: `{Feature}_Access` (e.g., `Agent_Authentication_Access`)
- Description: always include what the PS grants access to

## General Rules
- No abbreviations unless universally understood (ID, URL, API)
- No trailing numbers unless versioning is intentional
- Consistent casing within a project — don't mix styles
