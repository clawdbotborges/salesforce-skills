# Common Task Splits

## Complex Flow (>10 elements)
Split into:
1. **Create flow shell** — Create flow, define variables, save as inactive
2. **Build elements** — Add all elements and connectors
3. **Test and activate** — Debug run, fix issues, activate

## Agent with Multiple Topics
Split into:
1. **Create agent** — Basic agent config, user assignment
2. **Topic 1** — Create topic, add instructions, add actions
3. **Topic 2** — Create topic, add instructions, add actions
4. **Activate** — Final review, activate agent

## Object with Many Fields (>15)
Split into:
1. **Create object** — Object + first 10 fields (core fields)
2. **Add remaining fields** — Fields 11+ (supplementary fields)
3. **Page layout** — Arrange all fields on layout

## Permission Set for Complex Feature
Split into:
1. **Create PS** — Object/field permissions
2. **System permissions** — If any app/system perms needed
3. **Assign PS** — Assign to users/groups

## Integration Setup
Split into:
1. **Named Credential** — Auth provider + named credential
2. **External Service** — Register OpenAPI spec
3. **Flow/Apex** — Build the integration logic
4. **Test** — Verify end-to-end

## Full Agent Authentication Feature (real example)
From the Deploy Agent Authentication superbadge:

```
Task 01: Create Last_Customer_Verification__c field on Contact
Task 02: Create Verify Code and Log Verification flow (shell + variables)
Task 03: Build flow elements (Get Records, Decision, Assignments, Update)
Task 04: Add flow resources and agent action configuration
Task 05: Create Permission Set (Agent Authentication Access)
Task 06: Assign Permission Set to agent user
Task 07: Create agent topic (Identity Verification)
Task 08: Add actions to topic + configure instructions
Task 09: Activate agent
Task 10: Test end-to-end (run agent conversation)
```
