# Advanced Exercise Set 1: System Configuration and Customization

## Overview

Master SAP CS configuration through IMG (Implementation Guide). These exercises cover order type configuration, number ranges, status profiles, pricing procedures, and partner determination.

## Exercise Duration
- **Time Required:** 10-12 hours
- **Difficulty:** Advanced
- **Prerequisites:** Intermediate exercises completed, IMG access (display mode minimum)

⚠️ **IMPORTANT:** These exercises require customizing authorization. If you don't have access, complete in DISPLAY mode to learn the concepts.

## Exercise 1: Configure New Order Type for Emergency Service

### Objective
Create and configure a custom order type for 24/7 emergency service with premium pricing and expedited processing.

### Business Requirement

**Scenario:** Your company wants to offer 24/7 emergency service with:
- Premium pricing (2x standard rates)
- Immediate dispatch (30-minute response)
- After-hours surcharge
- Weekend surcharge
- No credit check (invoice later)
- Auto-notification to management
- Priority scheduling

### Part A: Analysis and Design (60 minutes)

**Step 1: Analyze Requirements**

Create detailed specification:

```
EMERGENCY SERVICE ORDER TYPE SPECIFICATION:
═══════════════════════════════════════════

Business Requirements:
──────────────────────
1. Order Type Code: ZEM1 (Z = custom, EM = Emergency)
2. Description: Emergency Service 24/7
3. Purpose: Urgent after-hours service calls

Pricing:
────────
- Labor Rate: 2x standard ($200/hr vs $100/hr)
- After-Hours Surcharge: +$150 flat fee
- Weekend Surcharge: +$250 flat fee
- Holiday Surcharge: +$500 flat fee
- No volume discounts
- Travel: Premium rate ($150/hr vs $75/hr)

Processing Rules:
─────────────────
- Response SLA: 30 minutes
- No credit check (invoice after service)
- Auto-release upon save
- Priority: Always 1 (Very High)
- Immediate notification to:
  * Service Manager
  * On-call Technician
  * Customer

Settlement:
───────────
- Settlement to customer (billable)
- Revenue recognition immediate
- No purchase order required
- Payment terms: Due on receipt

Authorization:
──────────────
- Only Service Managers can create
- All can display
- Service Coordinators can change
- Customer approval not required (emergency)

Integration:
────────────
- Auto-create notification
- Email alerts to service team
- SMS to on-call technician
- Calendar entry for tracking
```

**Step 2: Compare with Standard Order Types**

```
COMPARISON MATRIX:
══════════════════

Feature          │ SM01    │ SM06    │ ZEM1 (New)
                 │Standard │Emergency│ 24/7 Emerg
─────────────────┼─────────┼─────────┼────────────
Labor Rate       │ $100/hr │ $150/hr │ $200/hr
After-Hrs Charge │ None    │ +$50    │ +$150
Weekend Charge   │ None    │ +$100   │ +$250
Response SLA     │ 24hr    │ 4hr     │ 30min
Credit Check     │ Yes     │ Yes     │ No
Auto-Release     │ No      │ No      │ Yes
Priority         │ Varies  │ 1-2     │ Always 1
Notification     │ Manual  │ Email   │ Email+SMS
Price Procedure  │ ZSERV01 │ ZSERV02 │ ZSERV03
Settlement       │ Customer│ Customer│ Customer
Revenue Recog    │ Period  │ Period  │ Immediate
Auth Level       │ All     │ Coord   │ Manager
```

**Step 3: Identify Configuration Objects**

Document what needs to be configured:

```
CONFIGURATION CHECKLIST:
════════════════════════

IMG Nodes Required:
───────────────────
[1] Plant Maintenance and Customer Service
    └─► Maintenance and Service Processing
        └─► Maintenance and Service Orders
            ├─► [✓] Order Types
            ├─► [✓] Number Ranges
            ├─► [✓] Status Profile
            ├─► [✓] Settlement Profile
            ├─► [✓] Partner Determination
            └─► [✓] Field Selection

[2] Sales and Distribution
    └─► Basic Functions
        └─► Pricing
            └─► [✓] Pricing Procedures (ZSERV03)

Configuration Tasks:
────────────────────
1. Create order type: ZEM1
2. Assign number range: 80 (800000-899999)
3. Create/assign status profile: ZSEM (emergency)
4. Configure field selection: Emergency fields
5. Set settlement profile: Emergency settlement
6. Create pricing procedure: ZSERV03
7. Partner determination: Emergency partners
8. User status: Emergency tracking
9. Notification type: Auto-link
10. Authorization: Manager-only create
```

### Part B: IMG Navigation and Display (45 minutes)

**Step 4: Navigate IMG Structure**

1. Access IMG:
   ```
   Transaction: SPRO
   → SAP Reference IMG
   ```

2. Navigate to order types:
   ```
   Path:
   Plant Maintenance and Customer Service
   → Maintenance and Service Processing
     → Maintenance and Service Orders
       → Order Types
         → Define Order Types

   Transaction Code (direct): OMS1
   ```

3. Display existing order types:
   ```
   Transaction: OMS1

   View Existing Order Types:
   ┌─────────────────────────────────────────────────────────┐
   │ Order Type│Description              │Number Range│Status│
   ├───────────┼─────────────────────────┼────────────┼──────┤
   │ SM01      │ Standard Service Order  │ 60         │Active│
   │ SM02      │ Warranty Service        │ 61         │Active│
   │ SM03      │ Service Contract        │ 62         │Active│
   │ SM04      │ Internal Service        │ 63         │Active│
   │ SM05      │ Preventive Maintenance  │ 64         │Active│
   │ SM06      │ Emergency Service       │ 65         │Active│
   │ PM01      │ Breakdown Maintenance   │ 01         │Active│
   │ PM02      │ Planned Maintenance     │ 02         │Active│
   └─────────────────────────────────────────────────────────┘

   Select: SM06 (most similar to our needs)
   Click: Display
   ```

4. Document SM06 configuration:
   ```
   ORDER TYPE: SM06 CONFIGURATION:
   ═══════════════════════════════

   General Data:
   ├── Order Type: SM06
   ├── Description: Emergency Service
   ├── Order Category: Service Order (30)
   ├── Number Range: 65 (650000-659999)
   └── Plant: All plants

   Control Data:
   ├── Status Profile: PMSER (Service orders)
   ├── Settlement Profile: SER-CUST (to customer)
   ├── Results Analysis Key: Blank
   ├── Commitment Update: No
   └── Scheduling Type: Forward scheduling

   Default Values:
   ├── Priority: 2 (High)
   ├── Work Center: (from equipment)
   ├── System Condition: (none)
   └── Maintenance Planner Group: (blank)

   Field Selection:
   ├── Field Selection Key: SORD (Service order)
   ├── Header Fields: Standard
   ├── Equipment: Required
   └── Customer: Required

   Settlement:
   ├── Settlement Rule: To customer
   ├── Account Assignment: Customer
   └── Billing Relevance: Yes

   Integration:
   ├── Notification: Can reference
   ├── Sales Order: Can reference
   └── Project: Can reference

   Authorization:
   └── Authorization Group: (none - all can create)
   ```

5. Compare what you'll change for ZEM1:
   ```
   CHANGES FOR ZEM1:
   ═════════════════

   What's Different from SM06:
   ───────────────────────────
   ✎ Description: "24/7 Emergency Service"
   ✎ Number Range: 80 (new range needed)
   ✎ Status Profile: ZSEM (custom - auto-release)
   ✎ Priority: 1 (always very high)
   ✎ Field Selection: ZEME (emergency fields)
   ✎ Authorization: EMGR (Emergency - Manager only)

   What Stays Same:
   ────────────────
   ✓ Order Category: 30 (Service Order)
   ✓ Settlement: To customer
   ✓ Billing: Yes
   ✓ Can reference notification
   ```

**Step 5: Review Related Configuration**

Document the configuration chain:

```
CONFIGURATION DEPENDENCY CHAIN:
════════════════════════════════

Order Type (ZEM1)
├─► Requires: Number Range (80)
│   └─► Transaction: SNUM (Number Range Maintenance)
│       └─► Object: AUFNR (Order numbers)
│
├─► Requires: Status Profile (ZSEM)
│   └─► Transaction: OIAA (Status Profile)
│       ├─► System Status: Standard
│       └─► User Status: Emergency tracking
│
├─► Requires: Settlement Profile (ZEMER)
│   └─► Transaction: OKO7 (Settlement Profiles)
│       └─► Settlement: To customer, immediate
│
├─► Requires: Field Selection (ZEME)
│   └─► Transaction: OPUV (Field Selection)
│       └─► Fields: Emergency-specific
│
├─► Requires: Pricing Procedure (ZSERV03)
│   └─► Transaction: V/08 (Pricing Procedures)
│       └─► Conditions: Premium rates
│
└─► Requires: Partner Schema (ZSEMPT)
    └─► Transaction: OPL8 (Partner Determination)
        └─► Partners: Manager + On-call tech
```

### Part C: Configuration Design Document (90 minutes)

**Step 6: Create Detailed Configuration Spec**

Create complete configuration workbook:

```
═════════════════════════════════════════════════════════
CONFIGURATION WORKBOOK: EMERGENCY ORDER TYPE ZEM1
═════════════════════════════════════════════════════════

Project: Emergency Service Enhancement
Date: ____________
Prepared by: ____________
Approved by: ____________

═════════════════════════════════════════════════════════

SECTION 1: ORDER TYPE CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: OMS1 (Define Order Types)
IMG Path: PM/CS → Maintenance Processing → Orders → Order Types

Configuration Table: T003O (Order Types)

Field Name          │ Value      │ Description
────────────────────┼────────────┼──────────────────────
AUART (Order Type)  │ ZEM1       │ Emergency Service 24/7
AUTYP (Category)    │ 30         │ Service Order
TXT04 (Short Text)  │ EMRG       │ Short description
TXT30 (Description) │ 24/7 Emerg │ Long description

Control Parameters:
───────────────────
IWERK (Plant)       │ (all)      │ Valid for all plants
WERKS (Planning Pl) │ (all)      │ All planning plants
KOKRS (Ctrl Area)   │ 1000       │ Controlling area
BUKRS (Co.Code)     │ (all)      │ All company codes

Number Range:
─────────────
NUMKI (Int Number)  │ 80         │ Range: 800000-899999
NUMNK (Ext Number)  │ (blank)    │ No external numbering

Status Control:
───────────────
IPHAS (Status Prof) │ ZSEM       │ Emergency status
SSTAL (Stand.Status)│ PMSER      │ Standard: Service
ISTAT (Init Status) │ CRE REL    │ Created AND Released
ESTAT (Enter Status)│ (blank)    │ No entry status

Settlement:
───────────
ABGSL (Settl.Prof)  │ ZEMER      │ Emergency settlement
IVPRO (Insp.Profile)│ (blank)    │ No inspection
AFRES (Results)     │ (blank)    │ No results analysis

Default Values:
───────────────
PRIOK (Priority)    │ 1          │ Always Very High
REVNR (Revision)    │ (blank)    │ No revision
WERKS (Work Ctr Pl) │ (blank)    │ From equipment

Field Selection:
────────────────
FGRUL (Field Key)   │ ZEME       │ Emergency fields
PSPEL (WBS)         │ Optional   │ Project optional
KOSTL (Cost Ctr)    │ Optional   │ Cost center optional

Authorization:
──────────────
BEGRU (Auth Group)  │ EMGR       │ Manager authorization

Integration:
────────────
QMEL_FLAG (Notif)   │ X          │ Can reference notif
VBELN_FLAG (Sales)  │ (blank)    │ No sales order link
AUFNR_FLAG (Order)  │ (blank)    │ No order link

═════════════════════════════════════════════════════════

SECTION 2: NUMBER RANGE CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: SNUM (Number Range Maintenance)
Object: AUFNR (Order Numbers)

Number Range: 80
Description: Emergency Orders
From Number: 800000
To Number: 899999
Current Number: 800000

Buffer Settings:
├── No external buffer
├── Internal buffer: 10 numbers
└── Warning at: 890000 (90% used)

═════════════════════════════════════════════════════════

SECTION 3: STATUS PROFILE CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: OIAA (Status Profile Maintenance)

Profile: ZSEM
Description: Emergency Service Status

System Status Sequence:
───────────────────────
CRE (Created) → Automatically set
└─► REL (Released) → Auto-transition on save
    └─► PCNF (Partially Confirmed) → After first confirmation
        └─► CNF (Confirmed) → All operations confirmed
            └─► TECO (Technically Complete) → Work done
                └─► STL (Settled) → Costs settled
                    └─► CLSD (Closed) → Final closure

User Status Definition:
──────────────────────
Status│ Num│ Description      │ Initial│ Required Trans
──────┼────┼──────────────────┼────────┼────────────────
DSPCH │ E0001│ Dispatched     │   X    │ Auto on release
ENRTE │ E0002│ Technician En Route│      │ Mobile update
ONSTE │ E0003│ On Site        │        │ Mobile check-in
WKPRG │ E0004│ Work in Progress│       │ First confirmation
MGRAP │ E0005│ Manager Approval│       │ If >$5000
CUSTOK│ E0006│ Customer OK    │        │ Sign-off
BILLED│ E0007│ Invoiced       │        │ After settlement

Status Transitions:
───────────────────
From    → To      → Transaction    → Authorization
────────┼─────────┼────────────────┼──────────────
(none)  → DSPCH  → Auto (on save) → System
DSPCH   → ENRTE  → Mobile app     → Technician
ENRTE   → ONSTE  → Mobile app     → Technician
ONSTE   → WKPRG  → IW41 (Confirm) → Technician
WKPRG   → MGRAP  → IW32 (if req)  → System
MGRAP   → CUSTOK → IW32          → Manager
CUSTOK  → BILLED → VF01 (Billing)→ Accounting
BILLED  → CLSD   → IW48          → System

Business Rules:
───────────────
- Cannot TECO without CUSTOK status
- Cannot BILL without Manager Approval (>$5000)
- Cannot CLOSE without BILLED status
- MGRAP required if total > $5000

═════════════════════════════════════════════════════════

SECTION 4: PRICING PROCEDURE CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: V/08 (Pricing Procedures)

Pricing Procedure: ZSERV03
Description: Emergency Service Pricing

Condition Table Sequence:
──────────────────────────
Step│ Cond│ Description          │ From│ To │ Rate
────┼─────┼──────────────────────┼─────┼────┼────────
 10 │ ZLAB│ Labor - Regular Hours│  +  │ +  │$200/hr
 20 │ ZAFL│ After-Hours Labor    │  +  │ +  │$200/hr
 30 │ ZAWL│ Weekend Labor        │  +  │ +  │$200/hr
 40 │ ZAHF│ After-Hours Flat Fee │  +  │ +  │$150.00
 50 │ ZWKF│ Weekend Flat Fee     │  +  │ +  │$250.00
 60 │ ZHLF│ Holiday Flat Fee     │  +  │ +  │$500.00
 70 │ ZMAT│ Materials            │  +  │ +  │Cost+30%
 80 │ ZTRV│ Travel Time          │  +  │ +  │$150/hr
 90 │ ZTRD│ Travel Distance      │  +  │ +  │$3.00/mi
100 │ MWST│ Sales Tax            │  +  │ +  │8%
110 │      │ SUBTOTAL            │  =  │    │
120 │ SKTO│ Cash Discount        │  -  │    │(0%)
130 │      │ TOTAL               │  =  │    │

Condition Types Detail:
───────────────────────

ZLAB - Regular Labor:
├── Base: Activity type 1410
├── Rate: $200.00 per hour
├── Applies: Mon-Fri 8am-5pm
└── Calculation: Hours × $200

ZAFL - After-Hours Labor:
├── Base: Activity type 1420
├── Rate: $200.00 per hour
├── Applies: Mon-Fri 5pm-8am
└── Calculation: Hours × $200

ZAWL - Weekend Labor:
├── Base: Activity type 1430
├── Rate: $200.00 per hour
├── Applies: Sat-Sun all hours
└── Calculation: Hours × $200

ZAHF - After-Hours Fee:
├── Fixed amount: $150.00
├── Applies: Mon-Fri 5pm-8am
├── Per order (not per hour)
└── Added automatically

ZWKF - Weekend Fee:
├── Fixed amount: $250.00
├── Applies: Sat-Sun
├── Per order
└── Added automatically

ZHLF - Holiday Fee:
├── Fixed amount: $500.00
├── Applies: Company holidays
├── Per order
└── Manual if needed

ZMAT - Materials:
├── Base: Material cost
├── Markup: 30%
├── Calculation: (Cost × 1.30)
└── All materials

ZTRV - Travel Time:
├── Rate: $150.00 per hour
├── From: Service center
├── To: Customer site
├── Round trip included

ZTRD - Travel Distance:
├── Rate: $3.00 per mile
├── Round trip
├── Google Maps calculation
└── Fuel surcharge included

MWST - Sales Tax:
├── Rate: 8% (varies by location)
├── Base: All charges
├── Exempt: (none for emergency)
└── Auto-calculated

Pricing Example:
────────────────
Service Call: Sunday, 2:00 PM
Work: 3 hours on-site
Travel: 1 hour, 25 miles round trip
Parts: $500 cost

Calculation:
───────────
ZAWL (Weekend Labor):     3.0 hrs × $200 = $  600.00
ZWKF (Weekend Fee):       Flat fee        = $  250.00
ZMAT (Materials):         $500 × 1.30     = $  650.00
ZTRV (Travel Time):       1.0 hr × $150   = $  150.00
ZTRD (Travel Distance):   25 mi × $3      = $   75.00
                                            ──────────
SUBTOTAL:                                  $1,725.00
MWST (Sales Tax 8%):                       $  138.00
                                            ──────────
TOTAL:                                     $1,863.00
═════════════════════════════════════════════════════════

═════════════════════════════════════════════════════════

SECTION 5: FIELD SELECTION CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: OPUV (Field Selection Maintenance)

Field Selection Key: ZEME
Description: Emergency Service Fields

Screen: Order Header
────────────────────
Field Name        │ Status    │ Note
──────────────────┼───────────┼────────────────────
Order Type        │ Display   │ Auto-filled: ZEM1
Priority          │ Display   │ Auto-filled: 1
Equipment         │ Required  │ Must have equipment
Functional Loc    │ Optional  │ If no equipment
Customer          │ Required  │ Billing required
Work Center       │ Required  │ Dispatcher assigns
Description       │ Required  │ Problem description
Long Text         │ Required  │ Detailed notes

Emergency-Specific Fields:
──────────────────────────
After Hours       │ Display   │ Auto-detect
Weekend/Holiday   │ Display   │ Auto-detect
Callback Number   │ Required  │ 24/7 contact
On-Call Tech      │ Required  │ Assigned tech
Estimated Arrival │ Required  │ 30-min SLA
Manager Notified  │ Display   │ Auto-notification

Screen: Operations
──────────────────
Activity Type     │ Required  │ Determines rate
Duration          │ Required  │ For pricing
Work Center       │ Required  │ Who performs
Actual Times      │ Required  │ For billing

Screen: Materials
─────────────────
Material Number   │ Required  │ Must specify
Quantity          │ Required  │ For billing
Unit Price        │ Display   │ Auto-calculated

═════════════════════════════════════════════════════════

SECTION 6: PARTNER DETERMINATION CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: OPL8 (Partner Determination Procedures)

Partner Schema: ZSEMPT
Description: Emergency Service Partners

Required Partners:
──────────────────
Function│ Code │ Description      │ Source         │ Required
────────┼──────┼──────────────────┼────────────────┼─────────
AG      │ 0001 │ Customer         │ From order     │ YES
VW      │ 0002 │ On-Call Tech     │ From schedule  │ YES
VE      │ 0003 │ Service Manager  │ From work ctr  │ YES
AP      │ 0004 │ Contact Person   │ From customer  │ YES

Partner Determination Rules:
────────────────────────────
1. Customer (AG):
   ├── From order header
   ├── Validation: Must exist, not blocked
   └── Default: None

2. On-Call Technician (VW):
   ├── Source: On-call schedule (custom table)
   ├── Logic: Current date/time → schedule → tech
   ├── Backup: If primary unavailable, secondary
   └── Notification: SMS + Email immediately

3. Service Manager (VE):
   ├── Source: Work center responsible person
   ├── Logic: Work center → responsible manager
   ├── Escalation: Copy to regional manager
   └── Notification: Email with order details

4. Contact Person (AP):
   ├── Source: Customer master or order
   ├── Requirement: 24/7 reachable number
   └── Validation: Phone number required

Auto-Notifications:
───────────────────
Partner    │ Method      │ Timing        │ Template
───────────┼─────────────┼───────────────┼──────────
On-Call    │ SMS + Email │ Immediate     │ ZEME_TECH
Service Mgr│ Email       │ Immediate     │ ZEME_MGR
Customer   │ Email       │ Within 5 min  │ ZEME_CUST
Contact    │ SMS         │ Immediate     │ ZEME_CONT

═════════════════════════════════════════════════════════

SECTION 7: SETTLEMENT PROFILE CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: OKO7 (Settlement Profiles)

Settlement Profile: ZEMER
Description: Emergency Service Settlement

Settlement Rules:
─────────────────
Rule│ %  │ Receiver          │ Settlement Type
────┼────┼───────────────────┼────────────────
001 │100%│ Customer Account  │ Full settlement
    │    │ (from order)      │ Revenue recognition

Allocation Structure:
─────────────────────
Cost Element     │ Settlement Acct │ Revenue Acct
─────────────────┼─────────────────┼──────────────
Labor Costs      │ 400000          │ 800100
Material Costs   │ 400100          │ 800200
Overhead         │ 400200          │ 800300
Travel Costs     │ 400300          │ 800400

Settlement Cycle:
─────────────────
- Type: Individual settlement (per order)
- Frequency: Immediate upon TECO
- Revenue Recognition: Immediate
- Billing: Separate process (VF01)

Accounting Documents:
─────────────────────
Document Type: SA (Settlement)
Company Code: (from order plant)
Posting Date: Order TECO date
Reference: Order number

═════════════════════════════════════════════════════════

SECTION 8: AUTHORIZATION CONFIGURATION
═════════════════════════════════════════════════════════

Transaction: SU24 (Authorization Objects)

Authorization Object: I_AUART (Order Type)

Authorization Profile: EMGR_CREATE
Description: Emergency Order - Create Authorization

Field Name     │ Value  │ Description
───────────────┼────────┼──────────────────────
ACTVT (Activity│ 01     │ Create
I_AUART (Type) │ ZEM1   │ Emergency orders only
WERKS (Plant)  │ *      │ All plants
IWERK (Pl.Plant│ *      │ All planning plants

Roles Assigned:
───────────────
Z_SERVICE_MANAGER       → Full access (create/change/display)
Z_SERVICE_COORDINATOR   → Change and display only
Z_SERVICE_TECHNICIAN    → Display and confirm only
Z_SERVICE_VIEWER        → Display only

Authorization Matrix:
─────────────────────
Action     │ Manager│ Coordinator│ Technician│ Viewer
───────────┼────────┼────────────┼───────────┼───────
Create     │   ✓    │     ✗      │     ✗     │   ✗
Change     │   ✓    │     ✓      │     ✗     │   ✗
Display    │   ✓    │     ✓      │     ✓     │   ✓
Release    │   ✓    │     ✓      │     ✗     │   ✗
Confirm    │   ✓    │     ✓      │     ✓     │   ✗
TECO       │   ✓    │     ✓      │     ✗     │   ✗
Settle     │   ✓    │     ✗      │     ✗     │   ✗

═════════════════════════════════════════════════════════

SECTION 9: TESTING PLAN
═════════════════════════════════════════════════════════

Test Scenarios:
───────────────

Test 1: Order Creation
├── User: Service Manager
├── Scenario: Create emergency order
├── Expected: Order created with type ZEM1
├── Validation: Number from range 80
└── Status: CRE and REL automatically

Test 2: Pricing Calculation
├── Scenario: Weekend service, 3 hours
├── Expected Charges:
│   ├── Weekend Labor: $600
│   ├── Weekend Fee: $250
│   ├── Materials: $390 (Cost $300 + 30%)
│   ├── Travel: $150 (1 hr) + $75 (25 mi)
│   └── Tax: $118 (8%)
├── Total Expected: $1,583
└── Validation: Invoice matches

Test 3: Authorization
├── User: Service Coordinator (should fail)
├── Action: Try to create ZEM1
├── Expected: Authorization error
└── Message: "No authorization for order type ZEM1"

Test 4: Partner Determination
├── Create order at 10 PM Friday
├── Expected Partners:
│   ├── On-Call Tech: From schedule
│   ├── Service Manager: Auto-assigned
│   └── Customer: From order
├── Validation: All partners filled
└── Notifications: SMS + Email sent

Test 5: Status Transitions
├── Create order (CRE + REL)
├── Mobile check-in (ONSTE)
├── Confirm work (WKPRG)
├── Complete (TECO)
├── Expected: All transitions automatic
└── Validation: Status history correct

Test 6: Settlement
├── Complete emergency order
├── Total cost: $2,500
├── Expected Settlement:
│   └── 100% to Customer Revenue
├── Validation: FI documents created
└── Revenue recognized immediately

Test 7: Negative Tests
├── Try without equipment (should fail)
├── Try without customer (should fail)
├── Try to delete (should prevent)
└── Try to change after TECO (should prevent)

═════════════════════════════════════════════════════════

SECTION 10: IMPLEMENTATION PLAN
═════════════════════════════════════════════════════════

Phase 1: Development (Week 1)
──────────────────────────────
Day 1-2: Create number range and order type
Day 3: Configure status profile
Day 4: Configure field selection
Day 5: Unit testing

Phase 2: Integration (Week 2)
──────────────────────────────
Day 1-2: Configure pricing procedure
Day 3: Configure partner determination
Day 4: Configure settlement profile
Day 5: Integration testing

Phase 3: Authorization (Week 3)
────────────────────────────────
Day 1-2: Create authorization objects
Day 3: Assign to roles
Day 4: Test authorization matrix
Day 5: UAT preparation

Phase 4: User Acceptance (Week 4)
──────────────────────────────────
Day 1-3: User acceptance testing
Day 4: Defect fixes
Day 5: Go-live approval

Phase 5: Go-Live (Week 5)
──────────────────────────
Day 1: Transport to production
Day 2: Production validation
Day 3: User training
Day 4: Go-live
Day 5: Post go-live support

═════════════════════════════════════════════════════════
END OF CONFIGURATION WORKBOOK
═════════════════════════════════════════════════════════

Approval Signatures:
────────────────────
Business Owner: ____________  Date: ______
IT Manager: ____________      Date: ______
SAP Basis: ____________        Date: ______
Security: ____________         Date: ______
```

### Part D: Testing and Validation (60 minutes)

**Step 7: Create Test Scripts**

Document detailed test cases:

```
TEST CASE: TC-ZEM1-001
══════════════════════
Test: Create Emergency Order - Happy Path

Preconditions:
- User has EMGR authorization
- Test customer exists
- Test equipment exists
- Current time: Friday 8:00 PM (after hours)

Test Steps:
───────────
1. Login as Service Manager
2. Execute /nIW31
3. Enter order type: ZEM1
4. System should auto-fill:
   ├── Priority: 1
   └── Status: CRE + REL

5. Fill required fields:
   ├── Equipment: TEST-001
   ├── Customer: 1000999
   ├── Description: "Emergency - No power"
   └── Callback: 555-0199

6. Add operation:
   ├── Op 10: Diagnose issue (2.0 hrs)
   └── Activity: 1420 (After-hours)

7. Save order

Expected Results:
─────────────────
✓ Order number: 800001 (from range 80)
✓ Status: CRE REL (auto-released)
✓ User status: DSPCH (Dispatched)
✓ Pricing:
  ├── ZAFL: 2.0 × $200 = $400
  ├── ZAHF: $150 (after-hours fee)
  └── Subtotal: $550

✓ Partners assigned:
  ├── On-Call Tech: (from schedule)
  ├── Service Manager: (from work center)
  └── Customer: 1000999

✓ Notifications sent:
  ├── SMS to on-call tech
  ├── Email to service manager
  └── Email to customer

✓ Calendar entry created

Actual Results:
───────────────
[To be filled during testing]

Status: [ ] PASS  [ ] FAIL

Notes:
_________________________________
_________________________________
```

Continue creating test cases for all scenarios...

### Expected Results

✓ Complete configuration specification created
✓ All dependencies documented
✓ Test plan prepared
✓ Ready for implementation (if authorized)

### Deliverables

1. Configuration workbook (completed above)
2. Test scripts
3. Training materials
4. Implementation plan

---

## Exercise 2: Pricing Procedure Configuration

### Objective
Design and configure complex pricing procedure with multiple condition types, scales, and business rules.

### Business Requirement
Create pricing for:
- Time-based labor rates (regular, after-hours, weekend, holiday)
- Skill-based pricing (junior, senior, expert)
- Distance-based travel charges
- Material markup by category
- Volume discounts
- Customer-specific pricing
- Promotional discounts

### Tasks
1. Design condition table structure
2. Configure condition types
3. Build pricing procedure
4. Configure access sequences
5. Test all scenarios
6. Document pricing examples

---

## Exercise 3: Partner Determination Schema

### Objective
Configure partner determination for automatic assignment of responsible parties.

### Scenarios to Configure
- Equipment-based partner assignment
- Location-based assignment
- Time-based (on-call schedule)
- Skill-based assignment
- Escalation rules
- Notification triggers

---

## Exercise 4: Status Profile and User Status

### Objective
Create custom status profile with user-defined statuses for workflow control.

### Requirements
- Track order through custom workflow
- Prevent actions based on status
- Auto-transitions where appropriate
- Integration with notifications
- Reporting by status

---

**Continue to Advanced Exercise Set 2 for integration configuration...**
