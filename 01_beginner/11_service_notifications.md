# Module 4.2: Service Notifications (IW21/IW22/IW23)

## Introduction

Service Notifications are the entry point for most customer service activities in SAP. They document customer issues, requests, and problems. Mastering notifications is essential for effective service management.

## What is a Service Notification?

A **Service Notification** is a document that records:
- Customer complaints or problems
- Service requests
- Warranty claims
- Quality issues
- Damage reports
- Maintenance requests

Think of it as a "trouble ticket" or "service ticket" in customer service terms.

## Notification vs. Service Order

Understanding the difference is crucial:

```
SCENARIO: Customer's Printer Broken

Step 1: NOTIFICATION (IW21)
┌─────────────────────────────────────────┐
│ "My printer isn't working!"             │
│ - What: Customer report                │
│ - When: Problem logged                  │
│ - Why: Documentation                    │
│ - Cost Tracking: NO                     │
└─────────────────────────────────────────┘
          ↓
Step 2: SERVICE ORDER (IW31)
┌─────────────────────────────────────────┐
│ "We will fix your printer"              │
│ - What: Work authorization             │
│ - When: Work planned                    │
│ - Why: Execution & billing              │
│ - Cost Tracking: YES                    │
└─────────────────────────────────────────┘
```

**Key Differences:**

| Notification | Service Order |
|--------------|---------------|
| Problem reported | Work authorized |
| Customer perspective | Company perspective |
| Fast to create | Detailed planning |
| No cost tracking | Full cost tracking |
| Optional | Usually required |
| Can exist alone | Often references notification |

**Typical Flow:**
```
1. Customer calls → Create Notification (IW21)
2. Evaluate issue → Create Service Order from Notification (IW31)
3. Perform work → Confirm Order (IW41)
4. Bill customer → Create Invoice
5. Close notification → Complete cycle
```

## Service Notification Types

SAP supports different notification types for different purposes:

| Type | Code | Purpose | Example |
|------|------|---------|---------|
| **Service Request** | S1 | Customer requests service | "Annual maintenance needed" |
| **Service Notification** | S2 | Problem reported | "Equipment not working" |
| **Quality Notification** | M1 | Quality issue | "Defective part received" |
| **Complaint** | M2 | Customer complaint | "Poor service quality" |
| **Activity Report** | M3 | Service performed | "Maintenance completed" |

**Most common in CS:** S1 (Service Request) and S2 (Service Notification)

## Creating Service Notifications

### Transaction: IW21 - Create Service Notification

## Practice Example 1: Simple Service Notification

### Scenario: Coffee Machine Not Heating

**Background:**
- Customer: GlobalTech Inc. (Customer #1000567)
- Equipment: Coffee Machine (Equipment #10000001)
- Location: Office Building A, 3rd Floor
- Problem: Coffee machine brewing but water not heating
- Reported by: Sarah Johnson, Office Manager
- Priority: Medium (can use microwave temporarily)

### Step-by-Step Creation

**Step 1: Access Transaction**
```
Command Field: /nIW21
Press: Enter
```

**Step 2: Initial Screen**
```
┌──────────────────────────────────────────────────────────┐
│ Create Service Notification: Initial Screen              │
├──────────────────────────────────────────────────────────┤
│                                                          │
│ Notification Type:  [S2    ] * 🔍 Service Notification │
│                                                          │
│ Priority:           [3     ] 🔍                         │
│   1 - Very High / Immediate                             │
│   2 - High / Urgent                                     │
│   3 - Medium / Normal       ← Selected                  │
│   4 - Low / Can Wait                                    │
│                                                          │
│ [Continue]  [Cancel]                                     │
└──────────────────────────────────────────────────────────┘
```

**Priority Selection Guide:**
```
Priority 1 (Very High):
- Safety hazard
- Complete system down
- Revenue impact
- Example: "Gas leak in equipment"

Priority 2 (High):
- Major functionality lost
- Affecting multiple users
- Example: "Main server down"

Priority 3 (Medium):
- Partial functionality
- Workaround available
- Example: "Printer jamming frequently"

Priority 4 (Low):
- Minor issues
- Cosmetic problems
- Example: "Scratched paint on equipment"
```

**Important Notes:**
⚠️ **Notification type must exist** - Check with admin if unsure
💡 **Priority affects response time** - SLAs may be tied to priority
💡 **Can change priority later** - Not locked after creation

**Press Enter to Continue**

**Step 3: Header Screen**
```
┌──────────────────────────────────────────────────────────┐
│ Create Service Notification: Header Data                 │
├──────────────────────────────────────────────────────────┤
│ Notification:    [Will be assigned]                     │
│ Notif. Type:     S2 - Service Notification              │
│ Priority:        3 - Medium                              │
│ Status:          OSNO - Outstanding                      │
│                                                          │
│ Reference Data:                                          │
│ Functional Loc:  [_____________] 🔍                     │
│ Equipment:       [10000001     ] * 🔍                   │
│ Serial Number:   [CM-2023-0145 ] (auto-filled)          │
│                                                          │
│ Short Text:                                              │
│ Description:     [Coffee machine not heating water] *   │
│                                                          │
│ Reported By:                                             │
│ Reporter:        [SJOHNSON    ] 🔍                      │
│ Phone:           [555-0145    ]                         │
│ Email:           [sarah.j@globaltech.com]               │
│                                                          │
│ [General] [Dates] [Customer] [Tasks] [Details]          │
└──────────────────────────────────────────────────────────┘
```

**Field-by-Field Guide:**

**Equipment (EQUNR):**
```
Click 🔍 or press F4

Equipment Search:
┌──────────────────────────────────────────────────────┐
│ Equipment Number:   [          ]                     │
│ Description:        [Coffee*   ]                     │
│ Plant:             [1000       ]                     │
│ Serial Number:     [          ]                     │
│                                                      │
│ [Execute F8]                                         │
└──────────────────────────────────────────────────────┘

Results:
┌────────────────────────────────────────────────────────────────┐
│Equip.No│Description         │Plant│Location      │Serial No.  │
├────────┼────────────────────┼─────┼──────────────┼────────────┤
│10000001│Coffee Maker Deluxe │1000 │Bldg A, Fl 3 │CM-2023-0145│
│10000234│Coffee Grinder Pro  │1000 │Bldg B, Fl 1 │CG-2023-0089│
└────────────────────────────────────────────────────────────────┘

Double-click on correct equipment
```

**What Happens When You Select Equipment:**
```
System automatically fills:
✓ Serial Number (if exists)
✓ Manufacturer
✓ Model Number
✓ Functional Location (if assigned)
✓ Warranty Information
✓ Customer (if assigned to equipment)
```

**Description/Short Text:**
```
Guidelines for Good Descriptions:
✓ What: Coffee machine
✓ Problem: Not heating water
✓ When: Started this morning
✓ Impact: Cannot make hot coffee

Good Examples:
✓ "Printer frequent paper jams - started yesterday"
✓ "AC unit making loud noise - needs inspection"
✓ "Computer won't boot - blue screen error"

Bad Examples:
❌ "Broken" (too vague)
❌ "Not working" (not specific)
❌ "Problem" (no information)
```

**Reporter Field:**
```
Who reported the issue:
- Customer contact
- Employee
- Service technician

Tip: Use F4 to search:
┌────────────────────────────────┐
│ Last Name:  [Johnson*]        │
│ First Name: [Sarah   ]        │
│ [Execute]                      │
└────────────────────────────────┘
```

**Step 4: General Tab - Additional Details**
```
Click on [General] tab

┌──────────────────────────────────────────────────────────┐
│ General Data                                             │
├──────────────────────────────────────────────────────────┤
│ Master Data:                                             │
│ Equipment:          10000001 - Coffee Maker Deluxe      │
│ Manufacturer:       BrewMaster Inc.                     │
│ Model Number:       BM-500DX                             │
│ Serial Number:      CM-2023-0145                        │
│                                                          │
│ Location:                                                │
│ Plant:              1000 - Main Plant                   │
│ Functional Loc:     BLDG-A-FL3-BREAK - Breakroom        │
│ Room:               305                                 │
│ Building:           Building A                           │
│ Floor:              3                                   │
│                                                          │
│ Technical Details:                                       │
│ Catalog Profile:    [SERVICE  ] 🔍                      │
│ Damage Code:        [        ] 🔍                       │
│ Object Part:        [        ] 🔍                       │
│ Defect Code:        [        ] 🔍                       │
│                                                          │
│ Planning:                                                │
│ Work Center:        [SERV-TECH-01] 🔍                   │
│ Planner Group:      [SERVICE     ]                      │
│ Main Work Ctr:      [SERV-TECH-01]                      │
└──────────────────────────────────────────────────────────┘
```

**Catalog Codes Explained:**

**Why Use Codes?**
- Standardize problem reporting
- Enable analytics and trending
- Facilitate root cause analysis
- Improve search and filtering

**Example Catalog Structure:**
```
Damage Code: What's broken?
├── H - Heating System
├── E - Electrical
├── M - Mechanical
├── S - Software
└── O - Other

Object Part: Which component?
├── HEAT - Heating Element
├── PUMP - Water Pump
├── CTRL - Control Panel
├── TANK - Water Tank
└── SEAL - Seals/Gaskets

Defect Code: What type of problem?
├── FAIL - Complete Failure
├── DEGR - Degraded Performance
├── LEAK - Leaking
├── NOIS - Noisy Operation
└── ERRO - Error Messages
```

**Selecting Codes:**
```
Click 🔍 on Damage Code

┌──────────────────────────────────────────────┐
│ Code Group: DAMAGE                           │
├───────┬──────────────────────────────────────┤
│ Code  │ Description                          │
├───────┼──────────────────────────────────────┤
│ H     │ Heating System                       │ ← Select this
│ E     │ Electrical                           │
│ M     │ Mechanical                           │
│ S     │ Software                             │
│ O     │ Other                                │
└───────┴──────────────────────────────────────┘

Then select Object Part:
├───────┬──────────────────────────────────────┤
│ HEAT  │ Heating Element                      │ ← Select this
│ PUMP  │ Water Pump                           │
│ CTRL  │ Control Panel                        │
└───────┴──────────────────────────────────────┘

Then select Defect Code:
├───────┬──────────────────────────────────────┤
│ FAIL  │ Complete Failure                     │ ← Select this
│ DEGR  │ Degraded Performance                 │
│ LEAK  │ Leaking                              │
└───────┴──────────────────────────────────────┘
```

**Result:**
```
System can now report:
- "Heating System failures increased 30% this month"
- "Most common issue: Heating Elements"
- "Heating Element defects mostly FAIL vs DEGR"
```

**Important Notes:**
💡 **Codes are optional but recommended** - Better analytics
💡 **Your system may have different codes** - Ask your admin
⚠️ **Wrong codes = bad data** - Choose carefully
💡 **Can add multiple codes** - For complex issues

**Step 5: Customer Tab**
```
Click on [Customer] tab

┌──────────────────────────────────────────────────────────┐
│ Customer Data                                            │
├──────────────────────────────────────────────────────────┤
│ Customer Information:                                    │
│ Customer:           [1000567   ] * 🔍                   │
│ Customer Name:      GlobalTech Inc.                     │
│ Address:            456 Technology Drive                 │
│                     Suite 100                            │
│                     New York, NY 10001                   │
│                                                          │
│ Contact Person:                                          │
│ Contact Name:       [Sarah Johnson      ]               │
│ Department:         [Facilities         ]               │
│ Phone:              [555-0145           ]               │
│ Mobile:             [555-0199           ]               │
│ Email:              [sarah.j@globaltech.com]            │
│ Preferred Contact:  [✓] Email  [ ] Phone  [ ] SMS      │
│                                                          │
│ Service Level:                                           │
│ SLA Type:           PREMIUM - 4hr response              │
│ Response Due:       Today, 14:30 (auto-calculated)      │
│ Resolution Due:     Tomorrow, 10:00                     │
│                                                          │
│ Special Instructions:                                    │
│ Access Code:        [Building A: Code 1234*]            │
│ Best Time:          [09:00 - 17:00 weekdays]           │
│ Notes:              [Contact Sarah before visiting]     │
└──────────────────────────────────────────────────────────┘
```

**Customer Field:**
- Often auto-filled from equipment master
- Can override if different customer
- Use F4 to search if needed

**Service Level Agreement (SLA):**
```
System calculates due dates based on:
- Notification priority
- Customer service level
- Business calendar

Example:
Priority 1 + Premium Customer = 2 hour response
Priority 3 + Standard Customer = 24 hour response
```

**Important Notes:**
⚠️ **Missing customer = no billing** - Always fill if billable
💡 **SLA shown to technicians** - Drives prioritization
💡 **Access codes critical** - Technicians need building access

**Step 6: Long Text - Detailed Description**
```
Click on [Long Text] button (usually at top of screen)

┌──────────────────────────────────────────────────────────┐
│ Long Text Editor                                         │
├──────────────────────────────────────────────────────────┤
│                                                          │
│ ┌──────────────────────────────────────────────────────┐ │
│ │PROBLEM DESCRIPTION:                                  │ │
│ │                                                      │ │
│ │Coffee machine is brewing but water is not heating.  │ │
│ │Water comes out cold. This started this morning       │ │
│ │around 8:00 AM. Machine was working fine yesterday.   │ │
│ │                                                      │ │
│ │No error messages displayed on the control panel.    │ │
│ │All lights are normal. Machine completes brew cycle  │ │
│ │but coffee is cold.                                   │ │
│ │                                                      │ │
│ │IMPACT:                                               │ │
│ │20 employees use this machine daily for coffee       │ │
│ │Currently using microwave to heat water (workaround) │ │
│ │                                                      │ │
│ │ACTIONS ALREADY TAKEN:                                │ │
│ │- Unplugged and restarted: No change                 │ │
│ │- Tried different brew sizes: Same issue             │ │
│ │- Checked circuit breaker: OK                        │ │
│ │                                                      │ │
│ │ADDITIONAL INFO:                                      │ │
│ │Machine is 18 months old                              │ │
│ │Last service: 6 months ago (annual maintenance)      │ │
│ │Under warranty until March 2025                       │ │
│ └──────────────────────────────────────────────────────┘ │
│                                                          │
│ [Save]  [Back]                                           │
└──────────────────────────────────────────────────────────┘
```

**Long Text Best Practices:**

```
Good Long Text Should Include:
✓ Detailed problem description
✓ When it started
✓ What changed recently
✓ Error messages (exact text)
✓ Impact on operations
✓ Workarounds being used
✓ Actions already attempted
✓ Photos/screenshots (if possible)

Template:
---------
PROBLEM:
[What's wrong?]

WHEN:
[When did it start? Any pattern?]

SYMPTOMS:
[What exactly happens?]

IMPACT:
[Who/what is affected?]

TRIED:
[What have you already tried?]

ADDITIONAL:
[Warranty status, recent changes, etc.]
```

**Important Notes:**
💡 **Detail helps diagnosis** - More info = faster fix
💡 **Photos can be attached** - Use attachment function
⚠️ **Visible to customer** - Keep professional
💡 **Technicians read this** - Be thorough

**Step 7: Tasks/Activities (Optional)**
```
Click on [Tasks] tab

┌──────────────────────────────────────────────────────────┐
│ Tasks                                                    │
├──────────────────────────────────────────────────────────┤
│ Task│Description             │Status│Completed│By       │
├─────┼────────────────────────┼──────┼─────────┼─────────┤
│     │                        │      │         │         │
│     │                        │      │         │         │
└──────────────────────────────────────────────────────────┘

[Add Task]

Add Task Dialog:
┌──────────────────────────────────────────────┐
│ Task Code:        [DIAG  ] 🔍 Diagnose      │
│ Description:      [Diagnose heating issue   ]│
│ Task Status:      [NEW   ] New              │
│ Planned Start:    [Today, 14:00            ]│
│ Planned Dur:      [1.0   ] hours           │
│ Assigned To:      [TECH01] 🔍              │
│ [OK]  [Cancel]                              │
└──────────────────────────────────────────────┘
```

**Common Tasks:**
```
DIAG - Diagnose problem
REPA - Repair
TEST - Test equipment
CLEA - Clean equipment
REPL - Replace part
INSP - Inspect
CONF - Confirm resolution
```

**When to Use Tasks:**
- Break down complex notifications
- Assign different people
- Track progress
- Multi-step resolutions

**Step 8: Dates Tab**
```
Click on [Dates] tab

┌──────────────────────────────────────────────────────────┐
│ Dates                                                    │
├──────────────────────────────────────────────────────────┤
│ Notification Dates:                                      │
│ Created On:         12/29/2024  10:15 (auto)            │
│ Created By:         JSMITH                               │
│                                                          │
│ Reported Dates:                                          │
│ Malfunction Start:  [12/29/2024] * [08:00] *           │
│ Malfunction End:    [          ]   [     ]              │
│ Required Start:     [12/29/2024]   [14:00]              │
│ Required End:       [12/30/2024]   [12:00]              │
│                                                          │
│ Planned Dates:                                           │
│ Planned Start:      [12/29/2024]   [14:00]              │
│ Planned Finish:     [12/29/2024]   [16:00]              │
│                                                          │
│ SLA Dates (auto-calculated):                             │
│ Response Due:       12/29/2024  14:30                   │
│ Resolution Due:     12/30/2024  10:00                   │
│                                                          │
│ Actual Dates (filled during/after work):                 │
│ Actual Start:       [          ]   [     ]              │
│ Actual End:         [          ]   [     ]              │
└──────────────────────────────────────────────────────────┘
```

**Date Types Explained:**

**Malfunction Start:**
```
When did the problem begin?
- Important for warranty determination
- Used in failure analysis
- Helps identify patterns

Example: "Machine worked yesterday, broken today at 8 AM"
→ Malfunction Start: Today, 08:00
```

**Required Start/End:**
```
When does customer need this fixed?
- Customer's deadline
- May differ from SLA
- Helps prioritize work

Example: "Need it working by tomorrow noon for big meeting"
→ Required End: Tomorrow, 12:00
```

**Planned Start/Finish:**
```
When do we plan to work on it?
- Technician scheduling
- Resource planning
- Parts availability

Example: "Technician available at 2 PM today, should take 2 hours"
→ Planned: Today 14:00 - 16:00
```

**Actual Dates:**
```
When did work actually happen?
- Filled during/after work
- For historical analysis
- SLA compliance tracking

Filled later by technician
```

**Important Notes:**
⚠️ **Malfunction start required** - Important for reporting
💡 **SLA dates auto-calculate** - Based on priority and service level
💡 **Track when problem started** - Not when reported (may be different)

**Step 9: Review and Save**
```
Review all entered data:

Header:
✓ Equipment: 10000001
✓ Description: Coffee machine not heating water
✓ Priority: 3

General:
✓ Damage Code: H (Heating)
✓ Object Part: HEAT (Heating Element)
✓ Defect: FAIL (Complete Failure)

Customer:
✓ Customer: 1000567 - GlobalTech Inc.
✓ Contact: Sarah Johnson
✓ SLA: Premium - 4hr response

Dates:
✓ Malfunction Start: Today, 08:00
✓ Required End: Tomorrow, 12:00

Long Text:
✓ Detailed description entered

Ready to save? Click [Save] 💾 or Ctrl+S
```

**System Validation:**
```
System checks:
✓ Required fields filled?
✓ Equipment exists?
✓ Customer valid?
✓ Codes valid?
✓ Dates logical?

If all OK:
┌──────────────────────────────────────────┐
│ ✓ Notification 100000789 has been saved  │
│                                          │
│ Status: OSNO (Outstanding)               │
│ Response due: Today, 14:30               │
└──────────────────────────────────────────┘
```

**Important Notes:**
💡 **Note the number** - You'll need it to create service order
💡 **Status is Outstanding** - Meaning: awaiting action
💡 **SLA clock started** - Response timer running

## Practice Example 2: Creating Service Order from Notification

### Continuing the Coffee Machine Scenario

**Step 1: Create Service Order**
```
From notification screen, click:
[Create Service Order] button

Or manually:
/nIW31
Reference Notification: 100000789
```

**System Pre-Fills:**
```
Automatically copied from notification:
✓ Equipment: 10000001
✓ Description: Coffee machine not heating water
✓ Customer: 1000567
✓ Priority: 3
✓ Required dates
✓ Long text
✓ Codes

Just add:
- Operations (work to do)
- Materials (parts needed)
- Costs (if not auto-calculated)
```

**This saves time and ensures consistency!**

## Common Notification Scenarios

### Scenario 1: Warranty Claim

```
Customer: "My laptop is broken and it's under warranty"

Notification Creation:
─────────────────────
Notif. Type:    S2 - Service Notification
Equipment:      LAPTOP-12345
Description:    Laptop screen flickering - warranty claim
Priority:       2 (high - customer without laptop)

Customer Tab:
Customer:       1000456 - ABC Corp
Warranty:       Valid until 03/2025 ✓

Long Text:
─────────
Screen intermittently flickers and goes black.
Started 2 days ago. Getting worse.
Customer cannot work without laptop.
Still under manufacturer warranty.
Customer has backup laptop temporarily.

Tasks:
1. Verify warranty (check serial number)
2. Diagnose screen issue
3. If warranty: Process warranty claim
4. If not warranty: Quote customer for repair

Special Notes:
- Check warranty status FIRST
- Don't start repair until warranty verified
- If warranty expired, get customer approval for costs
```

### Scenario 2: Recurring Issue

```
Customer: "This is the third time this month the AC fails!"

Notification Creation:
─────────────────────
Notif. Type:    S2 - Service Notification
Equipment:      AC-UNIT-789
Description:    AC unit failure - RECURRING ISSUE
Priority:       1 (very high - hot weather)

Long Text:
─────────
AC unit stopped cooling again.
This is the THIRD occurrence this month:
- 12/05: Not cooling - compressor replaced
- 12/15: Not cooling - refrigerant added
- 12/29: Not cooling again - CURRENT

Previous repairs did not solve root cause.
Need thorough diagnosis, not quick fix.
Customer frustrated with repeat visits.
Temperature in office now 85°F.

Actions Needed:
1. Review history of previous repairs
2. Perform comprehensive diagnostic
3. Identify root cause (not symptom)
4. Replace unit if necessary
5. Consider preventive maintenance plan

Special Instructions:
- Send senior technician (not junior)
- Allow extra time for proper diagnosis
- Customer expects permanent solution
- Consider customer satisfaction gesture
```

### Scenario 3: Emergency/Safety Issue

```
Customer: "Equipment is sparking and smells like burning!"

Notification Creation:
─────────────────────
Notif. Type:    S2 - Service Notification
Equipment:      MACHINE-456
Description:    ⚠️ SAFETY HAZARD - Equipment sparking
Priority:       1 (immediate - safety issue)

Customer Tab:
Contact:        CALL IMMEDIATELY: 555-0911
Response SLA:   ASAP (override normal SLA)

Long Text:
─────────
🚨 URGENT - SAFETY HAZARD 🚨

Equipment is sparking from electrical panel.
Burning smell detected.
Equipment powered OFF immediately.
Circuit breaker TRIPPED.
Area EVACUATED as precaution.

IMMEDIATE ACTIONS TAKEN:
- Equipment powered off
- Circuit breaker locked out
- Area cordoned off
- Fire safety notified
- Facilities manager informed

DO NOT ATTEMPT TO RESTART
Requires immediate inspection by qualified electrician

Contact person standing by: Tom Wilson, 555-0911

Tasks (URGENT):
1. Dispatch qualified electrician IMMEDIATELY
2. Safety inspection before any work
3. Identify electrical fault
4. Repair or replace equipment
5. Safety re-certification required

Special Instructions:
- Treat as emergency
- No junior technicians
- Qualified electrician only
- Safety first - do not rush repair
- Customer will pay premium for emergency service
```

## Changing Notifications

### Transaction: IW22 - Change Service Notification

**When to Use:**
- Update status
- Add information
- Change priority
- Assign to different team
- Add notes from technician
- Link to service order

**Example: Technician Update**

```
/nIW22
Notification: 100000789

Technician adds to Long Text:
────────────────────────────
TECHNICIAN UPDATE - 12/29/2024 14:45

Arrived on site at 14:30.
Inspected coffee machine.

DIAGNOSIS:
Heating element has failed (no continuity when tested).
Water pump working correctly.
Control board functioning normally.

ROOT CAUSE:
Heating element burnout due to mineral buildup.
Machine not descaled regularly.

RECOMMENDATION:
1. Replace heating element (Part# HEAT-500DX)
2. Descale entire system
3. Set up regular descaling schedule (monthly)

NEXT STEPS:
- Part ordered (arrives tomorrow)
- Will return tomorrow to install
- Estimated repair time: 1 hour
- Customer notified of timeline

Updated Status to: INPR (In Process)
Updated Planned Return: Tomorrow, 09:00
```

## Displaying Notifications

### Transaction: IW23 - Display Service Notification

**Uses:**
- Review notification history
- Check current status
- See all related documents
- Review technician notes
- Customer inquiry response
- Management reporting

**Example Display:**

```
/nIW23
Notification: 100000789

Display Shows:
──────────────
Notification:     100000789
Type:            S2 - Service Notification
Status:          INPR - In Process
Priority:        3 - Medium

Equipment:       10000001 - Coffee Maker Deluxe
Customer:        1000567 - GlobalTech Inc.
Contact:         Sarah Johnson

Description:     Coffee machine not heating water

Created:         12/29/2024 10:15 by JSMITH
Response Due:    12/29/2024 14:30 ✓ MET
Resolution Due:  12/30/2024 10:00 (in progress)

Related Documents:
- Service Order: 600000456 (created from this notif.)
- Purchase Req:  1000123 (for heating element)

Tasks:
✓ Diagnose - Complete (Technician: TECH01)
⏳ Repair - In Progress (Scheduled: Tomorrow 09:00)
○ Test - Not Started
○ Customer Acceptance - Not Started

Status History:
12/29 10:15 - Created (OSNO - Outstanding)
12/29 14:45 - Changed to INPR (In Process) by TECH01
```

## Notification Status Management

### Understanding Status

```
Notification Lifecycle:
═══════════════════════

OSNO (Outstanding)
    │
    │ Technician assigned
    ↓
INPR (In Process)
    │
    │ Work completed
    ↓
NOPR (Notification Completed)
    │
    │ Final approval
    ↓
NOCO (Notification Closed)
```

**Status Meanings:**

| Status | Code | Meaning | Actions Allowed |
|--------|------|---------|-----------------|
| Outstanding | OSNO | Awaiting action | Create order, assign |
| In Process | INPR | Work started | Update, confirm |
| Part Missing | POMI | Awaiting parts | Order parts |
| Postponed | POST | Delayed | Reschedule |
| Completed | NOPR | Work done | Customer approval |
| Closed | NOCO | Fully closed | Archive only |

**Status Best Practices:**
✓ Update status promptly
✓ Add notes when changing status
✓ Inform customer of status changes
✓ Don't close until customer satisfied

## Notification Reporting

### Finding Notifications: IW28

**Transaction: IW28 - Notification List**

```
/nIW28

Selection Screen:
┌──────────────────────────────────────────────┐
│ Notification List                            │
├──────────────────────────────────────────────┤
│ Notification Number:   [          ]         │
│ Equipment:            [10000001  ]         │
│ Customer:             [          ]         │
│                                              │
│ Status:                                      │
│ [✓] Outstanding (OSNO)                      │
│ [✓] In Process (INPR)                       │
│ [ ] Completed (NOPR)                        │
│ [ ] Closed (NOCO)                           │
│                                              │
│ Date Range:                                  │
│ Created from:  [12/01/2024] to [12/31/2024]│
│                                              │
│ Priority:                                    │
│ [ ] Very High (1)                           │
│ [✓] High (2)                                │
│ [✓] Medium (3)                              │
│ [ ] Low (4)                                 │
│                                              │
│ [Execute F8]                                 │
└──────────────────────────────────────────────┘

Results:
┌──────────────────────────────────────────────────────────────────┐
│Notif. │Description          │Equipment│Status│Priority│Due Date │
├───────┼────────────────────┼─────────┼──────┼────────┼─────────┤
│100789 │Coffee not heating  │10000001 │INPR  │3       │12/30    │
│100756 │Printer jamming     │10000045 │OSNO  │2       │12/29 ⚠️│
│100723 │AC not cooling      │10000123 │OSNO  │1       │12/29 🚨│
└──────────────────────────────────────────────────────────────────┘
```

**Common Reports:**
- Open notifications by equipment
- Overdue notifications (past SLA)
- Notifications by customer
- High priority notifications
- Notifications without orders
- Average resolution time
- Recurring issues

## Best Practices

### Creating Quality Notifications

✅ **Complete Information**
- Fill all relevant fields
- Detailed long text
- Accurate codes
- Current contact info

✅ **Proper Prioritization**
- Be honest about priority
- Consider customer SLA
- Safety issues = high priority
- Don't cry wolf

✅ **Clear Description**
- What's wrong (symptoms)
- When it started
- What changed
- Impact on operations

✅ **Link to Equipment**
- Always link if equipment-related
- Builds equipment history
- Enables trend analysis
- Supports warranty tracking

### Managing Notifications

✅ **Regular Updates**
- Update status as work progresses
- Add technician notes
- Inform customer of delays
- Keep long text current

✅ **Timely Closure**
- Close when work complete
- Get customer acknowledgment
- Update final status
- Complete all documentation

✅ **Analytics**
- Review recurring issues
- Track resolution times
- Identify problem equipment
- Improve processes

## Common Mistakes to Avoid

❌ **Creating Order Without Notification**
- Loses customer complaint tracking
- No audit trail
- Harder to analyze trends

❌ **Vague Descriptions**
- "Broken" - what's broken?
- "Not working" - how exactly?
- "Problem" - what problem?

❌ **Wrong Priority**
- Everything as Priority 1 (cry wolf)
- Safety issues as low priority
- Not considering customer SLA

❌ **Missing Customer Information**
- Cannot contact customer
- Cannot bill properly
- Poor customer service

❌ **Not Updating Status**
- Customer left wondering
- Management has no visibility
- SLA tracking inaccurate

❌ **Closing Too Soon**
- Customer not satisfied
- Issue returns
- Damages relationship

## Practice Exercises

### Exercise 1: Create Simple Notification

**Task:** Create notification for printer paper jam

**Details:**
- Equipment: (search for any printer)
- Description: "Printer frequently jamming"
- Priority: 3
- Add details in long text
- Use appropriate codes

### Exercise 2: Create from Customer Call

**Scenario:** Customer calls:
"Hi, this is Mike from Accounting. Our office AC unit isn't cooling properly. It's running but air is warm. We have 15 people in this office and it's getting hot. Can someone come today?"

**Task:** Create complete notification with:
- Proper priority
- Customer details
- Long text with all info
- Appropriate codes
- SLA consideration

### Exercise 3: Update Existing Notification

**Task:**
1. Display your notification (IW23)
2. Change it (IW22)
3. Add technician update in long text
4. Change status to "In Process"
5. Save and verify

### Exercise 4: Search and Analyze

**Task:** Use IW28 to find:
1. All open notifications
2. High priority notifications
3. Notifications created this week
4. Notifications for specific equipment

### Exercise 5: Complete Lifecycle

**Task:** Take one notification through complete lifecycle:
1. Create (IW21)
2. Create service order from it (IW31)
3. Update with technician notes (IW22)
4. Change status to complete (IW22)
5. Close notification (IW22)
6. Display final result (IW23)

## Summary

In this module, you learned:

✅ What service notifications are and why they matter
✅ Difference between notifications and service orders
✅ How to create notifications (IW21) with complete details
✅ How to change notifications (IW22)
✅ How to display notifications (IW23)
✅ How to use notification lists (IW28)
✅ How to manage notification status
✅ Best practices for quality notifications
✅ Real-world scenarios and examples

## Key Takeaways

🎯 **Notifications track customer issues** - Essential for service quality
🎯 **Detail matters** - Better info = faster resolution
🎯 **Status updates critical** - Keep everyone informed
🎯 **Link to equipment** - Builds valuable history
🎯 **Proper codes enable analytics** - Identify trends and improve

## Next Steps

You've mastered service notifications! Next, learn about basic reporting:
[Module 4.3: Basic Reporting](12_basic_reporting.md)

## Quick Reference Card

```
NOTIFICATION QUICK REFERENCE
───────────────────────────────────
Create:     IW21
Change:     IW22
Display:    IW23
List:       IW28

Status Codes:
OSNO - Outstanding
INPR - In Process
NOPR - Completed
NOCO - Closed

Priority Guide:
1 - Emergency/Safety
2 - High/Urgent
3 - Medium/Normal
4 - Low/Can Wait

Required Fields:
✓ Notification Type
✓ Equipment (usually)
✓ Description
✓ Priority
✓ Customer (for billing)

Best Practices:
✓ Detailed long text
✓ Use catalog codes
✓ Update status regularly
✓ Link to equipment
✓ Close when complete
───────────────────────────────────
```
