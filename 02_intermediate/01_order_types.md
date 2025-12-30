# Module 1.1: Order Types and Customization

## Introduction

Order Types are the backbone of SAP CS service order processing. Understanding order types allows you to handle different service scenarios appropriately and optimize your service operations.

## What is an Order Type?

An **Order Type** defines:
- What kind of service work is being performed
- How the order should behave
- What fields are required
- How costs are handled
- Integration with other modules
- Settlement rules
- Authorization controls

Think of it as a "template" that controls order behavior.

## Why Different Order Types?

**Example Company: TechServe Solutions**

```
Different Service Scenarios Require Different Order Types:

Standard Service (SM01):
├── Regular service calls
├── Billable to customer
├── Standard pricing
└── Example: "Fix printer"

Warranty Service (SM02):
├── Warranty repairs
├── NOT billable to customer
├── Claim from manufacturer
└── Example: "Laptop under warranty repair"

Service Contract (SM03):
├── Covered by contract
├── Pre-paid service
├── Track against contract
└── Example: "Annual maintenance visit"

Internal Service (SM04):
├── Company's own equipment
├── Cost to cost center
├── Not billable
└── Example: "Fix office coffee machine"

Emergency Service (SM05):
├── After-hours/urgent
├── Premium pricing
├── Expedited processing
└── Example: "Server down at midnight"
```

## Standard SAP Service Order Types

### Common Order Types

| Order Type | Description | Typical Use | Billable | Settlement |
|------------|-------------|-------------|----------|------------|
| **SM01** | Standard Service | Regular service calls | Yes | Customer |
| **SM02** | Warranty Service | Warranty work | No (claim mfr) | Manufacturer |
| **SM03** | Service Contract | Contract maintenance | No (pre-paid) | Contract |
| **SM04** | Internal Service | Own equipment | No | Cost Center |
| **SM05** | Preventive Maintenance | Scheduled PM | Varies | Varies |
| **SM06** | Emergency Service | Urgent/After-hours | Yes (premium) | Customer |

**Note:** Your company may have custom order types with different codes!

## Practice Example 1: Standard Service Order

### Scenario: Printer Repair (Billable)

**Customer Situation:**
- Customer: ABC Corporation
- Equipment: Printer XYZ-500
- Problem: Frequent paper jams
- Status: No warranty, no service contract
- Expectation: Pay for service

**Step-by-Step: Create Standard Service Order**

**Step 1: Transaction and Order Type**
```
Transaction: /nIW31

Initial Screen:
┌──────────────────────────────────────────────┐
│ Create Service Order                         │
├──────────────────────────────────────────────┤
│ Order Type:    [SM01] * 🔍                  │
│                Standard Service Order        │
│                                              │
│ Reference:                                   │
│ Notification:  [________]                   │
│ Service Order: [________]                   │
│                                              │
│ [Continue]                                   │
└──────────────────────────────────────────────┘
```

**Order Type Selection:**
```
Click 🔍 to see all available order types:

┌────────────────────────────────────────────────┐
│ Order Type│ Description                        │
├───────────┼────────────────────────────────────┤
│ SM01      │ Standard Service Order             │ ← Select
│ SM02      │ Warranty Service Order             │
│ SM03      │ Service Contract Order             │
│ SM04      │ Internal Service Order             │
│ SM05      │ Preventive Maintenance             │
│ SM06      │ Emergency Service                  │
└────────────────────────────────────────────────┘

Selection Guide:
─────────────────
✓ Customer pays? → SM01
✓ Under warranty? → SM02
✓ Contract covers? → SM03
✓ Company equipment? → SM04
✓ Emergency? → SM06
```

**Press Enter to Continue**

**Step 2: Order Header**
```
┌──────────────────────────────────────────────────────────┐
│ Create Standard Service Order (SM01)                     │
├──────────────────────────────────────────────────────────┤
│ Order Number:      [____________] (will be assigned)    │
│ Order Type:        SM01 - Standard Service              │
│ Priority:          [2  ] High                           │
│                                                          │
│ Object Information:                                      │
│ Equipment:         [PRINTER-500] * 🔍                   │
│ Serial Number:     P-2023-0567  (auto-filled)          │
│ Functional Loc:    BLDG-A-FL2-PRINT                     │
│                                                          │
│ Description:       [Printer paper jam repair] *         │
│                                                          │
│ Planning:                                                │
│ Work Center:       [SERVICE-TECH] * 🔍                  │
│ Plant:             [1000] Main Plant                    │
│ Planning Plant:    [1000]                               │
│                                                          │
│ Status:            CRE (Created)                        │
│                                                          │
│ [Header] [Operations] [Components] [Costs] [Dates]     │
└──────────────────────────────────────────────────────────┘
```

**What Makes SM01 Different:**

```
Standard Service Order (SM01) Configuration:
════════════════════════════════════════════

Settlement Profile:    CUSTOMER
├── Settles to customer account
├── Creates billing document
└── Revenue recognition

Cost Elements Allowed:
├── Labor costs: YES
├── Material costs: YES
├── Overhead: YES
└── External services: YES

Required Fields:
├── Customer: MANDATORY
├── Equipment: RECOMMENDED
├── Work Center: MANDATORY
└── Operations: RECOMMENDED

Default Values:
├── Settlement rule: → Customer
├── Results analysis: → Revenue
├── Profit center: From work center
└── Billing relevance: YES

Authorization:
├── Can everyone create? Usually NO
├── Approval required? DEPENDS
└── Credit check: YES (customer credit limit)
```

**Step 3: Customer Data**
```
Click on [Header] tab, then customer section:

┌──────────────────────────────────────────────────────────┐
│ Customer Data (Required for SM01)                        │
├──────────────────────────────────────────────────────────┤
│ Sold-To Party:     [ABC123  ] * ABC Corporation         │
│ Ship-To Party:     [ABC123  ]   Same                    │
│ Bill-To Party:     [ABC123  ]   Same                    │
│ Payer:             [ABC123  ]   Same                    │
│                                                          │
│ Contact Person:    [John Smith        ]                 │
│ Phone:             [555-0123          ]                 │
│ Email:             [jsmith@abc.com    ]                 │
│                                                          │
│ Credit Information:                                      │
│ Credit Limit:      $100,000.00                          │
│ Credit Exposure:   $ 45,230.00  (45%)                   │
│ Available Credit:  $ 54,770.00                          │
│ Status:            ⚫ OK for Billing                     │
│                                                          │
│ Service Level:                                           │
│ SLA Type:          STANDARD - 24hr response             │
│ Pricing Procedure: ZSERV01 - Service Pricing            │
│ Payment Terms:     Net 30 Days                          │
└──────────────────────────────────────────────────────────┘
```

**Important for SM01:**
⚠️ **Credit check happens automatically** - Order blocked if over limit
💡 **Pricing procedure determines charges** - Labor + material rates
✓ **All customer fields required** - Can't bill without customer!

**Step 4: Operations (Work Steps)**
```
Click on [Operations] tab:

┌──────────────────────────────────────────────────────────┐
│ Operations                                               │
├──────────────────────────────────────────────────────────┤
│ Op│Description          │WorkCtr│Dur │UoM│ActivityType  │
├───┼─────────────────────┼───────┼────┼───┼──────────────┤
│010│Diagnose jam issue   │SRV-01 │1.0 │H  │1410-Service  │
│020│Disassemble printer  │SRV-01 │0.5 │H  │1410-Service  │
│030│Clean rollers/path   │SRV-01 │1.0 │H  │1410-Service  │
│040│Replace worn parts   │SRV-01 │1.5 │H  │1410-Service  │
│050│Reassemble & test    │SRV-01 │1.0 │H  │1410-Service  │
│060│Customer sign-off    │SRV-01 │0.5 │H  │1410-Service  │
├───┴─────────────────────┴───────┴────┴───┴──────────────┤
│ Total Duration: 5.5 hours                                │
│ Estimated Labor Cost: $550.00 (@ $100/hr)                │
└──────────────────────────────────────────────────────────┘

[Add Operation] [Delete Operation] [Copy Operation]
```

**Operation Details for SM01:**
```
Each Operation Needs:
─────────────────────
✓ Operation Number (0010, 0020, etc.)
✓ Description (what work?)
✓ Work Center (who does it?)
✓ Duration (how long?)
✓ Activity Type (for costing)

Activity Type Determines:
├── Hourly rate
├── Cost center
├── Skill level required
└── Billing rate

Example Activity Types:
1410 - Standard Service  → $100/hr
1420 - Senior Technician → $150/hr
1430 - Emergency Service → $200/hr
1440 - Travel Time       → $75/hr
```

**Step 5: Components (Materials Needed)**
```
Click on [Components] tab:

┌──────────────────────────────────────────────────────────┐
│ Components / Spare Parts                                 │
├──────────────────────────────────────────────────────────┤
│ Item│Material    │Description        │Qty │UoM│Price    │
├────┼────────────┼───────────────────┼────┼───┼─────────┤
│0010│ROLLER-001  │Feed Roller Kit    │1   │EA │$45.00   │
│0020│ROLLER-002  │Exit Roller        │1   │EA │$35.00   │
│0030│PAD-SEP-100 │Separator Pads (pk)│1   │PK │$25.00   │
│0040│CLEAN-KIT   │Cleaning Kit       │1   │EA │$15.00   │
├────┴────────────┴───────────────────┴────┴───┴─────────┤
│ Total Material Cost: $120.00                             │
└──────────────────────────────────────────────────────────┘

Material Details:
─────────────────
✓ Stock available? → Green light ⚫
⚠ Not in stock? → Yellow light ⚫ (Purchase req created)
❌ Material blocked? → Red light ⚫
```

**Material Pricing for SM01:**
```
Material cost calculation:
──────────────────────────
Base Price:       $45.00  (from material master)
+ Markup:         $9.00   (20% service markup)
+ Handling:       $2.00   (fixed handling fee)
─────────────────────────
Customer Price:   $56.00  per Feed Roller Kit

Configured in pricing procedure ZSERV01
```

**Step 6: Cost Summary**
```
Click on [Costs] tab:

┌──────────────────────────────────────────────────────────┐
│ Cost Overview                                            │
├──────────────────────────────────────────────────────────┤
│ PLANNED COSTS:                                           │
│                                                          │
│ Labor Costs:                                             │
│   5.5 hours @ $100/hr         $550.00                   │
│                                                          │
│ Material Costs:                                          │
│   Parts (4 items)             $120.00                   │
│   Markup (20%)                $ 24.00                   │
│   Handling fees               $  8.00                   │
│   Subtotal Materials          $152.00                   │
│                                                          │
│ Overhead:                                                │
│   Service overhead (15%)      $ 82.50                   │
│                                                          │
│ Travel/Other:                                            │
│   Travel (estimated)          $ 25.00                   │
│   ────────────                                           │
│                                                          │
│ TOTAL PLANNED COST:           $809.50                   │
│                                                          │
│ CUSTOMER BILLING (estimated):                            │
│   Labor                       $550.00                   │
│   Materials                   $176.00  (with markup)    │
│   Travel                      $ 35.00  (with markup)    │
│   ────────────                                           │
│   Subtotal                    $761.00                   │
│   Tax (8%)                    $ 60.88                   │
│   ────────────                                           │
│ TOTAL CUSTOMER INVOICE:       $821.88                   │
│                                                          │
│ ESTIMATED MARGIN:             $ 12.38  (1.5%)           │
└──────────────────────────────────────────────────────────┘
```

**Cost Transparency for SM01:**
- Customer sees billing amount ($821.88)
- Company tracks actual cost ($809.50)
- Margin analysis available
- Can quote customer before work

**Step 7: Dates and Scheduling**
```
Click on [Dates] tab:

┌──────────────────────────────────────────────────────────┐
│ Dates and Scheduling                                     │
├──────────────────────────────────────────────────────────┤
│ Basic Dates:                                             │
│ Created On:        12/29/2024  14:30                    │
│ Created By:        JSMITH                                │
│                                                          │
│ Customer Required Dates:                                 │
│ Required Start:    12/30/2024  08:00                    │
│ Required Finish:   12/30/2024  17:00                    │
│                                                          │
│ Planned Dates (Your Schedule):                           │
│ Basic Start:       12/30/2024  09:00                    │
│ Basic Finish:      12/30/2024  15:30                    │
│                                                          │
│ Scheduling:                                              │
│ Technician:        Mike Johnson (SRV-01)                │
│ Availability:      ⚫ Available                          │
│ Capacity Check:    ⚫ Passed                             │
│                                                          │
│ SLA Compliance:                                          │
│ Response SLA:      24 hours                             │
│ SLA Status:        ⚫ Within SLA                         │
└──────────────────────────────────────────────────────────┘
```

**Step 8: Save and Release**
```
Review Complete Order:
✓ Order type: SM01 (Standard Service)
✓ Customer: ABC Corporation
✓ Equipment: PRINTER-500
✓ Operations: 6 steps, 5.5 hours
✓ Materials: 4 parts, $152 cost
✓ Estimated total: $821.88
✓ Scheduled: Tomorrow 09:00-15:30
✓ Technician: Mike Johnson

Click [Save] 💾

System Message:
┌──────────────────────────────────────────┐
│ ✓ Order 600000789 has been saved         │
│                                          │
│ Status: CRE (Created)                    │
│ ⚠ Order must be RELEASED before work    │
└──────────────────────────────────────────┘

Click [Release] to make available for work:

System Message:
┌──────────────────────────────────────────┐
│ ✓ Order 600000789 has been released      │
│                                          │
│ Status: REL (Released)                   │
│ Materials reserved                       │
│ Technician notified                      │
│ Work can begin                           │
└──────────────────────────────────────────┘
```

## Practice Example 2: Warranty Service Order

### Scenario: Laptop Under Warranty

**Customer Situation:**
- Customer: TechCorp Inc.
- Equipment: Laptop Model X1000
- Serial: LT-2023-4567
- Problem: Screen flickering
- Status: Under manufacturer warranty until 03/2025
- Expectation: No charge (warranty claim)

**Key Differences with SM02 (Warranty):**

```
Warranty Order (SM02) vs Standard (SM01):
═══════════════════════════════════════════

Customer Billing:
SM01: YES → Customer pays
SM02: NO → Manufacturer pays (warranty claim)

Settlement:
SM01: → Customer account
SM02: → Manufacturer claim account

Required Documentation:
SM01: Basic service docs
SM02: + Warranty proof
      + Serial number verification
      + Failure analysis
      + Photos of defect

Pricing:
SM01: Your standard pricing
SM02: Manufacturer's warranty rates (usually lower)

Approval:
SM01: Customer approval
SM02: + Warranty validation BEFORE work
      + May need manufacturer pre-approval

Parts:
SM01: Use any parts, charge customer
SM02: MUST use manufacturer-approved parts
      or warranty void
```

**Step-by-Step: Create Warranty Service Order**

**Step 1: Select Warranty Order Type**
```
Transaction: /nIW31

┌──────────────────────────────────────────────┐
│ Order Type:    [SM02] * 🔍                  │
│                Warranty Service Order        │
└──────────────────────────────────────────────┘
```

**Step 2: Warranty Validation**
```
┌──────────────────────────────────────────────────────────┐
│ Warranty Information (CRITICAL for SM02!)                │
├──────────────────────────────────────────────────────────┤
│ Equipment:         LT-2023-4567                          │
│ Serial Number:     LT-2023-4567        [Verify] ✓       │
│                                                          │
│ Warranty Status:                                         │
│ Manufacturer:      TechHardware Inc.                     │
│ Warranty Start:    03/15/2023                           │
│ Warranty End:      03/14/2025          ⚫ VALID          │
│ Warranty Type:     Manufacturer Full Warranty            │
│ Days Remaining:    75 days                               │
│                                                          │
│ Coverage:                                                │
│ Parts:             ✓ Covered                            │
│ Labor:             ✓ Covered                            │
│ Travel:            ❌ NOT Covered (charge customer)     │
│                                                          │
│ Restrictions:                                            │
│ Max Labor Hours:   4 hours                              │
│ Pre-approval Req:  > $500                               │
│ Authorized Parts:  Manufacturer parts only              │
│                                                          │
│ Claim Information:                                       │
│ Claim Number:      [WC-2024-789123]  (auto-generated)   │
│ Claim Status:      Pending Validation                   │
└──────────────────────────────────────────────────────────┘
```

**Important Warranty Order Steps:**

```
BEFORE Starting Work:
══════════════════════

Step 1: Verify Warranty
├── Check serial number matches
├── Confirm warranty still valid
├── Review coverage (parts/labor)
└── ✓ System shows green light

Step 2: Check Restrictions
├── Labor hour limits?
├── Pre-approval needed?
├── Authorized parts only?
└── Document requirements?

Step 3: Get Approval (if needed)
├── Contact manufacturer
├── Provide failure description
├── Get claim number
├── Get approval for estimated cost
└── ⚠ DO NOT PROCEED without approval!

Step 4: Use Correct Parts
├── ONLY manufacturer-approved parts
├── ❌ NO aftermarket parts
├── ❌ NO refurbished parts
├── Document part numbers

Step 5: Detailed Documentation
├── Photos of defect
├── Failure analysis
├── Testing results
├── Serial number verification
└── Customer signature
```

**Step 3: Operations for Warranty**
```
┌──────────────────────────────────────────────────────────┐
│ Operations (SM02 - Different from SM01!)                 │
├──────────────────────────────────────────────────────────┤
│ Op│Description              │Hrs│Rate    │Notes         │
├───┼─────────────────────────┼───┼────────┼──────────────┤
│010│Verify warranty/serial   │0.5│$0.00   │Pre-check     │
│020│Diagnose screen issue    │1.0│$85.00  │Warranty rate │
│030│Document defect/photos   │0.5│$85.00  │Required!     │
│040│Replace screen assembly  │2.0│$85.00  │Mfr part only │
│050│Test and validate        │1.0│$85.00  │Full testing  │
│060│Complete warranty docs   │0.5│$0.00   │Paperwork     │
└──────────────────────────────────────────────────────────┘

Notes:
⚠ Rate: $85/hr (warranty rate, lower than standard $100/hr)
⚠ Extra documentation operations required
⚠ Total hours: 5.5 (within 6hr warranty limit)
✓ Photos/documentation mandatory
```

**Step 4: Materials (Manufacturer Parts)**
```
┌──────────────────────────────────────────────────────────┐
│ Components (MUST be manufacturer-approved)               │
├──────────────────────────────────────────────────────────┤
│ Material     │Description      │Mfr Part│Cost   │Source │
├──────────────┼─────────────────┼────────┼───────┼───────┤
│SCREEN-X1000  │X1000 Screen Kit │MFR-OEM │$250.00│Factory│
│CABLE-FLAT-01 │Flat Cable       │MFR-OEM │$ 15.00│Factory│
│ADHESIVE-SCR  │Screen Adhesive  │MFR-OEM │$  5.00│Factory│
└──────────────────────────────────────────────────────────┘

⚠ ALL parts MUST have "MFR-OEM" indicator
❌ Cannot substitute aftermarket parts
✓ Parts ordered from manufacturer
✓ Part numbers verified against warranty database
```

**Step 5: Cost Settlement (Different!)**
```
┌──────────────────────────────────────────────────────────┐
│ Cost Summary - Warranty Order                            │
├──────────────────────────────────────────────────────────┤
│ Costs Claimed from Manufacturer:                         │
│ ├── Labor: 5.0 hrs @ $85/hr     $ 425.00                │
│ ├── Parts: Screen assembly      $ 250.00                │
│ ├── Parts: Cables and adhesive  $  20.00                │
│ └── TOTAL WARRANTY CLAIM:       $ 695.00                │
│                                                          │
│ Costs Charged to Customer:                               │
│ ├── Travel: 1 hour               $  75.00                │
│ └── TOTAL CUSTOMER CHARGE:      $  75.00                │
│                                                          │
│ Settlement Rules:                                        │
│ Warranty costs → Manufacturer Claim (Vendor 50000)      │
│ Travel cost → Customer Invoice                          │
└──────────────────────────────────────────────────────────┘
```

**Important Differences:**
```
Standard Order (SM01):
Customer pays: $821.88
Company gets: $821.88

Warranty Order (SM02):
Manufacturer pays: $695.00 (claim)
Customer pays: $75.00 (travel only)
Company gets: $770.00 total

⚠ Warranty reimbursement may take 30-60 days
⚠ Manufacturer may dispute claim
⚠ Must follow all documentation requirements
```

## Practice Example 3: Service Contract Order

### Scenario: Annual Maintenance Visit

**Customer Situation:**
- Customer: GlobalCorp
- Contract: Annual Preventive Maintenance
- Contract Number: 40000123
- Service: Quarterly HVAC inspection
- Status: Pre-paid under contract
- Expectation: No additional charges

**Service Contract Order (SM03) Characteristics:**

```
Contract Order (SM03) Configuration:
════════════════════════════════════

Billing:
├── NOT billed to customer
├── Service pre-paid in contract
├── Track against contract limits
└── Consumption reduces contract value

Settlement:
├── Settles to contract
├── Consumes contract hours/amount
├── Tracks contract utilization
└── Alerts when contract limit approaching

Required Links:
├── MUST reference service contract
├── MUST be within contract coverage
├── MUST match contract items
└── Cannot exceed contract limits

Typical Uses:
├── Preventive maintenance
├── Annual inspections
├── Contract-based support
├── Pre-paid service hours
└── Managed service agreements
```

**Step 1: Create with Contract Reference**
```
Transaction: /nIW31

┌──────────────────────────────────────────────┐
│ Order Type:    [SM03] *                     │
│                Service Contract Order        │
│                                              │
│ Contract Ref:  [40000123] * 🔍 REQUIRED!   │
│                                              │
│ Contract Info (Auto-displayed):              │
│ Customer:      GlobalCorp                    │
│ Contract:      Annual HVAC Maintenance       │
│ Valid:         01/01/2024 - 12/31/2024      │
│ Total Value:   $12,000.00                   │
│ Used:          $ 3,200.00  (27%)            │
│ Available:     $ 8,800.00  (73%)            │
│                                              │
│ This Visit Consumes:  ~$500.00              │
│ Remaining After:      ~$8,300.00            │
└──────────────────────────────────────────────┘
```

**Contract Validation:**
```
System Checks:
✓ Contract exists?
✓ Contract valid (dates)?
✓ Contract not exceeded?
✓ Equipment covered by contract?
✓ Service type matches contract?

If ANY check fails → Order creation blocked!

Example Failure:
❌ "Contract 40000123 has exceeded available amount"
   Available: $200
   This order: $500
   → Cannot create order
   → Need contract amendment
```

**Step 2: Pre-Defined Operations from Contract**
```
┌──────────────────────────────────────────────────────────┐
│ Operations (Auto-populated from Contract!)               │
├──────────────────────────────────────────────────────────┤
│ Operations loaded from Contract Item 10:                 │
│ "Quarterly HVAC Inspection"                              │
│                                                          │
│ Op│Description              │Hrs│Rate│Notes             │
├───┼─────────────────────────┼───┼────┼──────────────────┤
│010│Visual inspection        │1.0│ $0│Contract covered  │
│020│Filter replacement       │0.5│ $0│Filters included  │
│030│Clean coils              │1.0│ $0│Contract covered  │
│040│Check refrigerant        │0.5│ $0│Contract covered  │
│050│Test all functions       │1.0│ $0│Contract covered  │
│060│Document findings        │0.5│ $0│Required          │
│070│Customer review          │0.5│ $0│Sign-off         │
└──────────────────────────────────────────────────────────┘

Total: 5.0 hours (within contract allowance)
Cost: $0 to customer (consumes contract value)
```

**Important Contract Notes:**
💡 **Operations pre-defined** - Standardized from contract
💡 **No billing to customer** - Already paid in contract
💡 **Track consumption** - Reduces available contract amount
⚠️ **Extra work needs approval** - Outside contract scope

**Step 3: Handling Additional Work**
```
During Service - Technician Finds Issue:
════════════════════════════════════════

Technician's Note:
"During inspection, found compressor making unusual noise.
Recommend replacement. NOT covered by standard contract."

Options:
────────

Option 1: Add to THIS Order (SM03)
├── Mark operation as "Additional/Billable"
├── Customer approval required
├── Generates separate invoice
└── Not consumed from contract

Example:
┌────────────────────────────────────────────────┐
│ Op│Description      │Hrs│Billable│Cost        │
├───┼─────────────────┼───┼────────┼────────────┤
│080│Replace compress.│3.0│  YES   │$450 + parts│
│   │(ADDITIONAL WORK)│   │        │            │
└────────────────────────────────────────────────┘

Option 2: Create Separate Order (SM01)
├── Keep contract order clean (SM03)
├── Create new standard order (SM01) for extra work
├── Clearly separate contract vs. non-contract
└── Better tracking and reporting

BEST PRACTICE: Use Option 2 (separate orders)
```

## Order Type Comparison Table

```
┌─────────────────────────────────────────────────────────────────────────┐
│ Feature          │ SM01     │ SM02      │ SM03      │ SM04     │ SM06   │
│                  │ Standard │ Warranty  │ Contract  │ Internal │ Emerg  │
├──────────────────┼──────────┼───────────┼───────────┼──────────┼────────┤
│ Customer Billed  │ YES      │ NO        │ NO        │ NO       │ YES    │
│ Settlement To    │ Customer │ Vendor    │ Contract  │ Cost Ctr │ Cust   │
│ Pricing          │ Standard │ Warranty  │ Contract  │ Internal │ Premium│
│ Customer Req'd   │ YES      │ YES       │ YES       │ NO       │ YES    │
│ Equipment Req'd  │ Usually  │ YES!      │ YES       │ Usually  │ Usually│
│ Special Docs     │ Standard │ Warranty  │ Contract  │ Minimal  │ Rush   │
│ Approval Needed  │ Sometimes│ Always    │ Automatic │ Manager  │ After  │
│ Response Time    │ Standard │ Per Warr. │ Per Contr.│ Flexible │ Immedi.│
│ Parts Markup     │ YES      │ NO        │ NO        │ NO       │ YES+   │
│ Labor Rate       │ $100/hr  │ $85/hr    │ Contract  │ Cost     │$200/hr │
└─────────────────────────────────────────────────────────────────────────┘
```

## When to Use Which Order Type - Decision Tree

```
SERVICE REQUEST RECEIVED
         │
         ├─→ Is it covered by warranty?
         │   └─→ YES → Use SM02 (Warranty)
         │       ├─→ Verify warranty first!
         │       ├─→ Check coverage limits
         │       └─→ Get approval if needed
         │
         ├─→ Is it covered by service contract?
         │   └─→ YES → Use SM03 (Contract)
         │       ├─→ Check contract balance
         │       ├─→ Verify coverage
         │       └─→ Track consumption
         │
         ├─→ Is it company's own equipment?
         │   └─→ YES → Use SM04 (Internal)
         │       ├─→ No customer billing
         │       └─→ Cost to cost center
         │
         ├─→ Is it urgent/after-hours?
         │   └─→ YES → Use SM06 (Emergency)
         │       ├─→ Premium pricing
         │       └─→ Expedited processing
         │
         └─→ None of the above?
             └─→ Use SM01 (Standard Service)
                 ├─→ Regular pricing
                 └─→ Bill customer
```

## Practice Exercises

### Exercise 1: Identify Correct Order Type

**For each scenario, identify the correct order type:**

**Scenario A:**
"Customer ABC calls about broken printer. No warranty, no contract. They will pay for repair."
**Answer:** ______ **Why:** ______

**Scenario B:**
"Customer reports laptop screen broken. Laptop is 6 months old, under 1-year warranty."
**Answer:** ______ **Why:** ______

**Scenario C:**
"Quarterly maintenance visit for customer with annual maintenance contract."
**Answer:** ______ **Why:** ______

**Scenario D:**
"Office manager reports break room refrigerator not cooling. It's company equipment."
**Answer:** ______ **Why:** ______

**Scenario E:**
"Customer calls at 11 PM - server down, business stopped, need immediate help."
**Answer:** ______ **Why:** ______

### Exercise 2: Create Different Order Types

**Task:** Create three different orders for the same customer:

1. **Standard Service Order (SM01)**
   - Equipment: Any printer
   - Problem: Regular maintenance
   - Customer pays

2. **Warranty Order (SM02)**
   - Equipment: Any equipment under warranty
   - Problem: Defect
   - Check warranty first!

3. **Contract Order (SM03)**
   - Find or create service contract
   - Create maintenance order
   - Verify contract consumption

### Exercise 3: Order Type Configuration Analysis

**Task:** For each order type in your system:

1. Display in IW33
2. Identify settlement rule
3. Check required fields
4. Document cost flow
5. Create comparison table

### Exercise 4: Warranty Validation

**Scenario:** Create warranty order and document:

1. How to verify warranty is valid?
2. Where is warranty info stored?
3. What happens if warranty expired?
4. How to get manufacturer approval?
5. What documentation is required?

### Exercise 5: Contract Consumption

**Task:** Create service contract order and track:

1. Contract value before order
2. Order estimated cost
3. Contract value after confirmation
4. How to see contract consumption?
5. What happens when contract limit reached?

## Common Mistakes and How to Avoid Them

### Mistake 1: Wrong Order Type Selection

```
❌ Problem:
Created SM01 (Standard) for warranty work
→ Customer billed when shouldn't be
→ Warranty claim not processed
→ Customer disputes charge

✓ Solution:
- ALWAYS check warranty status first
- Create checklist for order type selection
- Train team on order type differences
- Use decision tree above
```

### Mistake 2: Not Verifying Warranty Before Work

```
❌ Problem:
Started work on SM02 without verifying warranty
→ Warranty actually expired
→ Cannot bill customer (used wrong order type)
→ Company absorbs cost

✓ Solution:
- Mandatory warranty check before SM02
- System validation of warranty dates
- Block order release until verified
- Document warranty status in order
```

### Mistake 3: Contract Overrun

```
❌ Problem:
Created SM03 without checking contract balance
→ Contract already consumed
→ Customer not billed (expected contract coverage)
→ Revenue loss

✓ Solution:
- Check contract balance before creation
- System validation of available amount
- Alert when contract approaching limit
- Offer contract renewal proactively
```

### Mistake 4: Missing Customer on Billable Order

```
❌ Problem:
Created SM01 without customer
→ Cannot create invoice
→ No revenue recognition
→ Write-off

✓ Solution:
- Make customer field mandatory for SM01
- Validation at save
- Cannot release without customer
- Pre-populate from equipment master
```

## Summary

In this module, you learned:

✅ What order types are and why they matter
✅ Different standard SAP order types (SM01-SM06)
✅ How to create standard service orders (SM01)
✅ How to create warranty orders (SM02)
✅ How to create contract orders (SM03)
✅ When to use which order type
✅ Cost and settlement differences
✅ Common mistakes and solutions

## Key Takeaways

🎯 **Order type drives behavior** - Choose carefully!
🎯 **Verify warranty BEFORE work** - Save time and money
🎯 **Check contract balance** - Avoid overruns
🎯 **Different types = different costs** - Understand pricing
🎯 **Documentation requirements vary** - Follow rules

## Next Module

Master complex service orders:
[Module 1.2: Complex Service Orders](02_complex_orders.md)

## Quick Reference

```
ORDER TYPE SELECTION GUIDE
───────────────────────────────────
Standard Service (SM01):
✓ Customer pays
✓ No warranty, no contract
✓ Regular service work
✓ Bill to customer

Warranty Service (SM02):
✓ Under warranty
✓ Manufacturer pays
✓ Verify warranty FIRST
✓ Strict documentation

Contract Service (SM03):
✓ Service contract exists
✓ Pre-paid service
✓ Check contract balance
✓ Track consumption

Internal Service (SM04):
✓ Company equipment
✓ No customer billing
✓ Cost to cost center
✓ Internal tracking

Emergency Service (SM06):
✓ Urgent/after-hours
✓ Premium pricing
✓ Fast response
✓ Higher rates
───────────────────────────────────
```
