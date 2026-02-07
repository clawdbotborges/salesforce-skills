# Common Flow Element Patterns

## Identity Verification Pattern
```
Start → Get Records (lookup by identifier)
→ Decision (record found?)
  → Yes → Decision (codes/values match?)
    → Yes → Assignment (set verified = true) → Update Records
    → No → Assignment (set verified = false)
  → No → Assignment (set not found)
→ End
```

## Record Lookup + Conditional Update
```
Get Records → Decision (found?) 
→ Yes → Assignment (set fields) → Update Records → End
→ No → Create Records (new record) → End
```

## Loop with Collection Processing
```
Get Records (collection) → Loop (iterate)
→ Per item: Decision → Assignment (add to output collection)
→ After loop: Update Records (output collection) → End
```

## Screen Flow: User Input + Create
```
Screen (input fields) → Decision (validate)
→ Valid → Create Records → Screen (success message) → End
→ Invalid → Screen (error message) → [back to input screen]
```

## Agent Action Pattern (Autolaunched)
Input variables (from agent) → Get Records → Decision → Assignment → Output variables (to agent)
- Mark inputs as "Available for Input"
- Mark outputs as "Available for Output"
- Run in System Context (agent user may not have direct object access)

## Scheduled Cleanup Pattern
```
Scheduled Start (daily/weekly) → Get Records (stale records, date filter)
→ Loop → Update Records (set status = Archived) → End
```

## Error Handling
- Use Fault paths on DML/callout elements
- Fault → Create Records (Error_Log__c) → End or Retry
- Screen Flows: Fault → Screen (user-friendly error message)

## Variable Naming in Flows
- Record variables: `record{ObjectName}` (e.g., `recordContact`)
- Collection variables: `col{ObjectName}s` (e.g., `colContacts`)
- Text variables: `var{Purpose}` (e.g., `varVerificationCode`)
- Boolean variables: `var{Question}` (e.g., `varIsVerified`)
- Input/output: prefix with purpose, mark availability appropriately
