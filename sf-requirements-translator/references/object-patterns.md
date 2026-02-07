# Common Object & Field Patterns

## Customer Management (B2B)
- Account (company) → Contact (people) → Opportunity (deals)
- Lead → convert to Account + Contact + Opportunity

## Customer Management (B2C)
- Person Account (individual) → Case (support) → Opportunity (purchase)
- Or: Account + Contact with RecordType for individual

## Service/Support
- Case → CaseComment, EmailMessage, CaseFeed
- Knowledge__kav for self-service and agent grounding
- Entitlement + SlaProcess for SLA tracking
- Milestone for SLA milestone tracking

## Order Management
- Account → Opportunity → Quote → Order → OrderItem
- Product2 + PricebookEntry for catalog
- Custom: Return__c, Shipment__c if needed

## Event/Appointment Booking
- Custom: Booking__c (Master-Detail to Contact, Lookup to Resource)
- Or: ServiceAppointment (Field Service)
- Event (standard) for calendar integration

## Approval Processes
- Standard Approval Process on any object
- Custom: Approval_Request__c for complex multi-step

## Audit/Logging
- Custom: Verification_Log__c, Activity_Log__c
- Master-Detail to parent for rollup capability
- DateTime fields for timestamps, Lookup to User for actor

## Common Field Patterns

### Verification/Identity
- `Verification_Code__c` (Text, encrypted if sensitive)
- `Is_Verified__c` (Checkbox)
- `Last_Verified_Date__c` (Date/DateTime)
- `Verified_By__c` (Lookup to User)

### Status Tracking
- `Status__c` (Picklist: Draft, Active, Closed, etc.)
- `Stage__c` (Picklist for process stages)
- `Last_Status_Change__c` (DateTime, set by automation)

### External Integration
- `External_ID__c` (Text, External ID, Unique)
- `Sync_Status__c` (Picklist: Pending, Synced, Error)
- `Last_Sync__c` (DateTime)
