# Agent Flow Examples

Real flow specifications from Trailhead hands-on badges.

## Example 1: Get Contact's Upcoming Bookings

**Type:** Autolaunched Flow (No Trigger)
**Purpose:** Find upcoming bookings for a contact (agent action for Coral Cloud Resorts)

### Variables
| Name | Type | Data Type | Input | Output | Description |
|---|---|---|---|---|---|
| Contact_ID | Variable | Text | ✅ | ❌ | The Id of a Contact record to find bookings for |
| Contact_Bookings | Variable | Record Collection (Booking) | ❌ | ✅ | Upcoming non-canceled bookings for the contact |
| Error_Message_Output | Variable | Text | ❌ | ✅ | Error message if flow fails |

### Elements

**1. Get Records: Get Contact's Upcoming Bookings**
- Object: Booking
- Filters (ALL must match):
  - Contact Equals {Contact_ID}
  - Date Greater Than {$Flow.CurrentDate}
  - Is Canceled Equals {$GlobalConstant.False}
- Sort: Ascending by Date__c
- Store: All records, choose fields → Contact_Bookings
- Fields: Experience_Name__c, Date__c, Start_Time__c, End_Time__c

**2. Assignment: Set the Error Message Output** (on Fault path)
- Error_Message_Output = "I'm sorry, I'm having a problem finding your booked activities. Would you like to speak to our support team?"

### Structure
```
Start → Get Records
  ├── Default → End
  └── Fault → Set Error Message → End
```

---

## Example 2: Verify Code and Log Verification

**Type:** Autolaunched Flow (No Trigger)
**Purpose:** Verify customer identity by matching codes, log verification date on Contact

### Variables
| Name | Type | Data Type | Input | Output | Description |
|---|---|---|---|---|---|
| customerId | Variable | Text | ✅ | ❌ | User or Contact record ID |
| customerType | Variable | Text | ✅ | ❌ | "User" or "Contact" |
| codeFromCustomer | Variable | Text | ✅ | ❌ | Code provided by customer |
| codeFromRecord | Variable | Text | ✅ | ❌ | Code from the database record |
| isVerified | Variable | Boolean | ❌ | ✅ | Whether verification succeeded |

### Elements

**1. Decision: Do Codes Match?**
- Outcome "Yes": codeFromCustomer Equals {codeFromRecord}
- Default "No"

**2. Decision: Is the Customer a User or Contact?** (under Yes path)
- Outcome "User": customerType Equals "User"
- Outcome "Contact": customerType Equals "Contact"
- Default

**3. Assignment: Set Verified to True** (under Contact path)
- isVerified = {$GlobalConstant.True}

**4. Update Records: Update Contact's Last Customer Verification** (after Set Verified)
- Object: Contact
- Filter: Id Equals {customerId}
- Set: Last_Customer_Verification__c = {$Flow.CurrentDate}

**5. Assignment: Set Verified to False** (under No/Default path)
- isVerified = {$GlobalConstant.False}

### Structure
```
Start → Do Codes Match?
  ├── Yes → Is Customer User or Contact?
  │     ├── User → End
  │     └── Contact → Set Verified True → Update Contact Verification → End
  └── No (Default) → Set Verified False → End
```

---

## Example 3: Adjust Booking (Simple Update)

**Type:** Autolaunched Flow (No Trigger)
**Purpose:** Update number of guests on a booking record

### Variables
| Name | Type | Data Type | Input | Output |
|---|---|---|---|---|
| Booking_Record_Number | Variable | Text | ✅ | ❌ |
| Number_of_Guests | Variable | Number | ✅ | ❌ |
| Error_Message_Output | Variable | Text | ❌ | ✅ |

### Elements

**1. Get Records: Get Booking**
- Object: Booking
- Filter: Record_Number Equals {Booking_Record_Number}
- Store: First record only

**2. Assignment: Update Guest Count**
- {recordBooking}.Number_of_Guests__c = {Number_of_Guests}

**3. Update Records: Save Booking**
- Use record variable: {recordBooking}

**4. Assignment: Set Error** (Fault paths)
- Error_Message_Output = "Sorry, I couldn't update the booking. Please try again."

---

## Pattern Summary

All agent action flows follow this template:
1. Input variables for identifiers + data from conversation
2. Get Records to find the target
3. Decision/logic for business rules
4. Create/Update/Delete for data changes
5. Output variables for results + Error_Message_Output
6. Fault paths on all DML elements
