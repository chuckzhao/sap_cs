# Module 1.3: SAP CS Architecture and Data Flow

## Introduction

Understanding SAP CS architecture is crucial for working effectively with the system. This module explains how SAP CS fits within the SAP ecosystem and how data flows through the system.

## SAP CS in the SAP Ecosystem

### The Big Picture

```
┌─────────────────────────────────────────────────────────────┐
│                    SAP ERP System                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────┐      ┌──────────┐      ┌──────────┐        │
│  │   SD     │◄────►│   CS     │◄────►│   MM     │        │
│  │  Sales   │      │ Customer │      │Materials │        │
│  │          │      │ Service  │      │          │        │
│  └──────────┘      └──────────┘      └──────────┘        │
│       ▲                  ▲                  ▲             │
│       │                  │                  │             │
│       └──────────────────┼──────────────────┘             │
│                          │                                │
│                   ┌──────▼──────┐                         │
│                   │   FI / CO   │                         │
│                   │  Finance &  │                         │
│                   │ Controlling │                         │
│                   └─────────────┘                         │
│                                                            │
│       ┌──────────┐                    ┌──────────┐        │
│       │   PM     │◄──────────────────►│   HR     │        │
│       │  Plant   │                    │  Human   │        │
│       │Maintenance│                   │Resources │        │
│       └──────────┘                    └──────────┘        │
└─────────────────────────────────────────────────────────────┘
```

### Integration Points Explained

#### CS ↔ SD (Sales & Distribution)
**What flows between them:**
- Customer master data
- Sales orders converted to service orders
- Returns and complaints
- Pricing information
- Billing documents

**Example Scenario:**
```
1. Customer buys a laptop (SD Sales Order)
2. Laptop delivered and invoice created
3. Customer reports defect (CS Notification)
4. Return processed (SD Return Order + CS Service Order)
5. Replacement sent (SD Delivery)
```

#### CS ↔ MM (Materials Management)
**What flows between them:**
- Material master data
- Spare parts inventory
- Goods movements (withdrawals/returns)
- Purchase requisitions for parts
- Vendor information

**Example Scenario:**
```
1. Service order created for printer repair
2. Technician needs toner cartridge
3. Material withdrawal from warehouse (MM)
4. If not in stock, purchase requisition created (MM)
5. Material cost flows to service order
```

#### CS ↔ FI/CO (Finance/Controlling)
**What flows between them:**
- Cost center assignments
- Service order costs
- Revenue recognition
- Billing documents
- Profitability analysis

**Example Scenario:**
```
1. Service order completed
2. Labor hours × hourly rate = cost
3. Materials used = cost
4. Total cost posted to cost center (CO)
5. Invoice created and posted to revenue (FI)
6. Settlement clears service order
```

#### CS ↔ PM (Plant Maintenance)
**What flows between them:**
- Equipment master data
- Functional locations
- Maintenance plans
- Work orders
- Technical objects

**Example Scenario:**
```
CS handles: Customer-owned equipment (external)
PM handles: Company-owned equipment (internal)

Shared: Equipment master, notifications, work orders
```

## SAP CS Technical Architecture

### Database Layer

```
┌─────────────────────────────────────────────────────┐
│              SAP Database (HANA/Oracle/etc)        │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Master Data Tables          Transaction Tables    │
│  ┌──────────────────┐       ┌──────────────────┐  │
│  │ EQUI (Equipment) │       │ QMEL (Notif.)    │  │
│  │ KNA1 (Customer)  │       │ AUFK (Orders)    │  │
│  │ MARA (Material)  │       │ AFKO (Order Hdr) │  │
│  │ ILOA (Func.Loc.) │       │ AFPO (Order Item)│  │
│  └──────────────────┘       └──────────────────┘  │
│                                                     │
│  Configuration Tables        Document Tables       │
│  ┌──────────────────┐       ┌──────────────────┐  │
│  │ T003O (Ord.Type) │       │ VBAK (Sales Doc) │  │
│  │ TQ80 (Notif.Type)│       │ VBRK (Billing)   │  │
│  │ T001 (Co. Code)  │       │ MKPF (Goods Mvmt)│  │
│  └──────────────────┘       └──────────────────┘  │
└─────────────────────────────────────────────────────┘
```

### Application Layer

```
┌─────────────────────────────────────────────────────┐
│           SAP Application Server (ABAP)            │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Function Modules        Business Objects          │
│  ┌──────────────────┐   ┌──────────────────┐      │
│  │ Equipment APIs   │   │ BAPI_ALM_NOTIF   │      │
│  │ Order Processing │   │ BAPI_ALM_ORDER   │      │
│  │ Notification Mgmt│   │ BAPI_EQUI        │      │
│  └──────────────────┘   └──────────────────┘      │
│                                                     │
│  Workflow Engine         Partner Functions         │
│  ┌──────────────────┐   ┌──────────────────┐      │
│  │ Status Control   │   │ Pricing Engine   │      │
│  │ User Exits       │   │ Settlement       │      │
│  │ BAdIs            │   │ Availability Chk │      │
│  └──────────────────┘   └──────────────────┘      │
└─────────────────────────────────────────────────────┘
```

### Presentation Layer

```
┌─────────────────────────────────────────────────────┐
│              User Interface                         │
├─────────────────────────────────────────────────────┤
│                                                     │
│  SAP GUI               Fiori Apps      Mobile Apps │
│  ┌──────────────┐     ┌─────────┐    ┌─────────┐  │
│  │ IW31, IW21   │     │ Service │    │ Field   │  │
│  │ IW32, IW22   │     │ Order   │    │ Service │  │
│  │ Classic UI   │     │ Tile    │    │ App     │  │
│  └──────────────┘     └─────────┘    └─────────┘  │
└─────────────────────────────────────────────────────┘
```

## Data Flow: Complete Service Scenario

Let me walk you through a complete scenario showing how data flows:

### Scenario: Coffee Machine Repair

**Step 1: Customer Calls (Notification Created)**

```
User Action: Creates notification IW21
     ↓
System reads: Customer Master (KNA1, KNB1)
              Equipment Master (EQUI)
              Notification Type Config (TQ80)
     ↓
System writes: QMEL (Notification header)
               QMSM (Notification items)
               QMFE (Activities/Tasks)
     ↓
System assigns: Number from range
                Status (OSNO = Outstanding)
                Partner functions
     ↓
Result: Notification 100000123 created
```

**Important Notes:**
⚠️ Equipment must exist before creating notification
⚠️ Customer must be valid and not blocked
⚠️ Notification type must be configured
💡 System auto-populates data from equipment master

**Step 2: Service Order Created**

```
User Action: Creates service order IW31 (referencing notification)
     ↓
System reads: Notification 100000123
              Order Type Config (T003O)
              Customer data
              Equipment BOM
              Pricing procedure
     ↓
System writes: AUFK (Order header)
               AFKO (Order control data)
               AFPO (Order operations)
               RESB (Material reservations)
     ↓
System creates: Operations automatically (if configured)
                Material list from BOM
                Pricing conditions
     ↓
Result: Service Order 600000456 created
```

**Important Notes:**
⚠️ Order must be released before work can start
⚠️ Materials are reserved, not withdrawn yet
💡 Reference to notification creates automatic link
💡 Equipment history updated with order number

**Step 3: Material Withdrawal**

```
User Action: Technician withdraws spare parts
     ↓
Transaction: MB1A (Goods Issue) or via order (IW32)
     ↓
System reads: Material Master (MARA, MARC)
              Storage location stock (MARD)
              Service order reservation (RESB)
     ↓
System writes: MKPF (Material document header)
               MSEG (Material document line)
               MBEW (Material valuation - cost)
     ↓
System updates: Stock levels decreased
                Order cost updated
                Reservation fulfilled
     ↓
Result: Material document 500000789
        Cost posted to order
```

**Important Notes:**
⚠️ Check stock availability before withdrawal
⚠️ Correct plant and storage location required
💡 Cost automatically posts to service order
💡 Inventory decreased in real-time

**Step 4: Work Confirmation**

```
User Action: Technician confirms work IW41
     ↓
System reads: Service order operations
              Work center capacity
              Employee/resource data
     ↓
System writes: AFRU (Confirmation record)
               AFVC (Operation update)
     ↓
System calculates: Actual hours × hourly rate = labor cost
                   + Material costs
                   + Overhead
                   = Total actual cost
     ↓
System updates: Order status to CNF (Confirmed)
                Actual dates
                Costs
     ↓
Result: Confirmation recorded
        Order ready for completion
```

**Important Notes:**
⚠️ Order must be released to confirm
⚠️ Work center must exist and be valid
💡 Actual costs replace planned costs
💡 Can confirm multiple times for one operation

**Step 5: Technical Completion**

```
User Action: Mark order technically complete
     ↓
System checks: All operations confirmed?
               All materials accounted for?
               No open reservations?
     ↓
System updates: Status to TECO (Technically Complete)
                Final costs calculated
                No more postings allowed (except settlement)
     ↓
Result: Order locked for changes
        Ready for billing/settlement
```

**Important Notes:**
⚠️ TECO is irreversible (usually)
⚠️ Ensure all costs posted first
💡 Order can still be settled after TECO

**Step 6: Settlement (Cost Transfer)**

```
User Action: Execute settlement IW47
     ↓
System reads: Order actual costs
              Settlement rule
              Receiving cost object (cost center/customer)
     ↓
System writes: CO documents (cost transfer)
               FI documents (if customer billing)
     ↓
System transfers: Labor costs → Customer/Cost Center
                  Material costs → Customer/Cost Center
                  Overhead → Customer/Cost Center
     ↓
System updates: Order status STL (Settled)
                Order cost cleared to zero
     ↓
Result: Costs transferred
        Order financially closed
```

**Important Notes:**
⚠️ Settlement rule must be configured
⚠️ Receiving cost object must be valid
💡 Settlement clears order costs
💡 Can be automatic or manual

**Step 7: Billing (if applicable)**

```
User Action: Create invoice VF01 or automatic billing
     ↓
System reads: Service order costs
              Customer master
              Pricing conditions
              Tax codes
     ↓
System writes: VBRK (Billing document header)
               VBRP (Billing document items)
     ↓
System creates: Customer invoice
                Revenue posting (FI)
                Accounts receivable entry
     ↓
Result: Invoice 9000000123 created
        Customer receivable created
```

**Important Notes:**
⚠️ Order must be technically complete
⚠️ Customer credit limit checked
💡 Pricing can differ from actual costs
💡 Invoice can include multiple orders

**Step 8: Order Closure**

```
User Action: Close order (automatic or manual)
     ↓
System checks: Order status TECO?
               Order settled?
               Invoice created?
     ↓
System updates: Status to CLSD (Closed)
                Order completion date
     ↓
Result: Order lifecycle complete
        Appears in historical reports only
```

## Master Data Architecture

### Equipment Master Structure

```
Equipment Master (EQUI table)
├── General Data
│   ├── Equipment Number (EQUNR)
│   ├── Description (EQKTX)
│   ├── Equipment Category (EQART)
│   ├── Manufacturer (HERST)
│   ├── Model Number (TYPBZ)
│   └── Serial Number (SERNR)
├── Organizational Data
│   ├── Plant (SWERK)
│   ├── Company Code (BUKRS)
│   ├── Cost Center (KOSTL)
│   └── Functional Location (TPLNR)
├── Location Data
│   ├── Room (RAUMNR)
│   ├── Building (GEBAEU)
│   └── Floor (ETAGE)
├── Warranty Data
│   ├── Warranty Start (GWLDT)
│   ├── Warranty End (GWLEN)
│   └── Warranty Type (WAERS)
└── Partner Functions
    ├── Customer (AG)
    ├── Manufacturer (VW)
    └── Service Provider (SP)
```

### Customer Master Structure

```
Customer Master
├── General Data (KNA1)
│   ├── Customer Number (KUNNR)
│   ├── Name (NAME1)
│   ├── Search Term (SORTL)
│   ├── Street (STRAS)
│   ├── City (ORT01)
│   ├── Country (LAND1)
│   └── Language (SPRAS)
├── Company Code Data (KNB1)
│   ├── Company Code (BUKRS)
│   ├── Reconciliation Account (AKONT)
│   ├── Payment Terms (ZTERM)
│   └── Credit Limit (KLIMK)
└── Sales Area Data (KNVV)
    ├── Sales Organization (VKORG)
    ├── Distribution Channel (VTWEG)
    ├── Division (SPART)
    ├── Pricing Procedure (KALKS)
    └── Incoterms (INCO1)
```

## Practice Example: Trace a Transaction

### Exercise: Find Where Data is Stored

**Scenario:** You created notification 100000123. Where is this data stored?

**Step-by-Step Investigation:**

**1. Find the Header Data**
```
Transaction: SE16 (Data Browser)
Table: QMEL
Key field: QMNUM = 100000123

What you'll find:
- QMNUM: Notification number
- QMART: Notification type
- PRIOK: Priority
- QMTXT: Description
- ERDAT: Created on
- ERNAM: Created by
```

**2. Find the Detail Data**
```
Table: QMSM (Notification items)
Key: QMNUM = 100000123

What you'll find:
- Item numbers
- Damage codes
- Object parts
- Defect descriptions
```

**3. Find Equipment Link**
```
Table: VIQMEL (Equipment-Notification link)
Key: QMNUM = 100000123

What you'll find:
- EQUNR: Equipment number
- Link between notification and equipment
```

**Important Notes:**
💡 Never change data directly in tables!
💡 Use SE16 for viewing only (display mode)
💡 Understanding table structure helps troubleshooting
⚠️ Direct table changes can corrupt data

## System Configuration Architecture

### Customizing Hierarchy (IMG)

```
SAP Reference IMG (SPRO)
└── Plant Maintenance and Customer Service
    ├── Master Data
    │   ├── Technical Objects
    │   │   ├── Equipment
    │   │   └── Functional Locations
    │   └── Business Partners
    ├── Maintenance and Service Processing
    │   ├── Maintenance and Service Orders
    │   │   ├── Order Types
    │   │   ├── Number Ranges
    │   │   ├── Status Management
    │   │   └── Settlement
    │   └── Maintenance and Service Notifications
    │       ├── Notification Types
    │       ├── Catalogs
    │       └── Partner Determination
    └── Service Management Scenarios
        ├── Service Contracts
        ├── Warranty Management
        └── Returns and Complaints
```

## Real-World Architecture Example

### Company: GlobalTech Service Inc.

**Business Structure:**
```
GlobalTech Service Inc.
├── North America Division
│   ├── US East (Plant 1000)
│   │   ├── New York Service Center
│   │   └── Boston Service Center
│   └── US West (Plant 2000)
│       ├── San Francisco Service Center
│       └── Los Angeles Service Center
└── Europe Division
    ├── UK (Plant 3000)
    └── Germany (Plant 4000)
```

**SAP Configuration:**
```
Company Code: GT01
├── Plant 1000 (US East)
│   ├── Work Center: NYSVC-01
│   ├── Storage Location: 0001
│   └── Cost Center: 1000-SVC
├── Plant 2000 (US West)
│   ├── Work Center: SFSVC-01
│   ├── Storage Location: 0001
│   └── Cost Center: 2000-SVC
└── Plant 3000 (UK)
    ├── Work Center: LNSVC-01
    ├── Storage Location: 0001
    └── Cost Center: 3000-SVC
```

**Data Flow Example:**
```
Customer in New York reports printer issue
        ↓
Notification created in Plant 1000
        ↓
Service order created
   ├── Work Center: NYSVC-01
   ├── Materials from Storage 0001
   └── Costs to Cost Center 1000-SVC
        ↓
Technician confirms work
        ↓
Costs settle to Customer (if billable)
or Cost Center (if warranty)
        ↓
Invoice created (if billable)
```

## Key Takeaways

### Understanding Architecture Helps You:

✅ **Troubleshoot issues** - Know where to look for data
✅ **Design solutions** - Understand integration impacts
✅ **Optimize performance** - Know system bottlenecks
✅ **Plan implementations** - Structure your approach
✅ **Communicate effectively** - Speak the technical language

### Critical Architecture Concepts

1. **Master Data is Shared**
   - Equipment used by CS, PM, SD
   - Customer used by CS, SD, FI
   - Material used by CS, MM, SD

2. **Transactions Create Documents**
   - Each transaction writes to multiple tables
   - Documents are linked via key fields
   - History is preserved

3. **Integration is Automatic**
   - Data flows between modules seamlessly
   - SAP handles integration logic
   - Configuration controls behavior

4. **Everything is Configurable**
   - Order types, notification types
   - Number ranges, status profiles
   - Partner determination, pricing

## Practice Exercises

### Exercise 1: Map a Business Process

Draw the data flow for:
"Customer returns defective laptop for repair"

Include:
- Which modules involved?
- What documents created?
- What tables updated?
- Integration points?

### Exercise 2: Investigate a Service Order

For any service order:
1. Find it in table AUFK
2. Find its operations in AFVC
3. Find material reservations in RESB
4. Find confirmations in AFRU
5. Map the complete picture

### Exercise 3: Understand Your Organization

Document:
- Your company codes
- Your plants
- Your work centers
- Your storage locations
- How they're connected

## Summary

In this module, you learned:

✅ How SAP CS integrates with other modules
✅ Technical architecture (database, application, presentation)
✅ Complete data flow for service scenarios
✅ Master data architecture
✅ Where data is stored (tables)
✅ Configuration hierarchy
✅ Real-world architecture examples

## Next Module

Now that you understand the architecture, let's dive into master data:
[Module 2.1: Customer Master Data](04_customer_master.md)

## Important Reminder

💡 **Architecture knowledge is power!**
- Don't memorize table names (reference them)
- Focus on understanding data flow
- Use this knowledge for troubleshooting
- It gets easier with practice!
