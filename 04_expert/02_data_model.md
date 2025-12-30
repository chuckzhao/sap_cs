# S/4HANA Simplified Data Model for Customer Service

## Overview

One of the most significant changes in S/4HANA is the **simplified data model**. SAP has reduced the number of database tables, eliminated redundant data storage, and introduced **CDS Views** as the new way to access business data.

For Customer Service, this means:
- **Faster queries** (HANA in-memory computing)
- **Simpler data structures** (fewer tables to join)
- **Real-time analytics** (no aggregates needed)
- **Better extensibility** (CDS Views instead of custom Z-tables)

## Table Simplification: Before and After

### ECC Data Model (Complex)

In ECC, a single service order touches **25+ database tables**:

```
SERVICE ORDER #800001 in ECC

┌─────────────────────────────────────────┐
│ HEADER DATA (Split across 4 tables)    │
├─────────────────────────────────────────┤
│ AUFK   - Order Header (Basic)          │
│ AFKO   - Order Header (Controlling)    │
│ JEST   - Order Status                  │
│ CAUFV  - Header Data (Materialized)    │
└─────────────────────────────────────────┘
         │
         ├──────────────────────────────────┐
         ▼                                  ▼
┌──────────────────────┐      ┌──────────────────────┐
│ OPERATIONS           │      │ COMPONENTS           │
├──────────────────────┤      ├──────────────────────┤
│ AFVC - Operations    │      │ RESB - Reservations  │
│ AFVV - Versions      │      │ MSEG - Material Docs │
│ AFRU - Confirmations │      │ MKPF - Mat Doc Hdr   │
└──────────────────────┘      └──────────────────────┘
         │
         ▼
┌──────────────────────────────────────────┐
│ COSTS (Split across 8 tables)           │
├──────────────────────────────────────────┤
│ COEP  - CO Line Items                   │
│ COBK  - CO Document Header              │
│ COSS  - Cost Totals (Actual)            │
│ COSP  - Cost Totals (Plan)              │
│ COKZ  - Activity Types                  │
│ AUFM  - Goods Movements for Order       │
│ MLCD  - Material Ledger Docs            │
│ ACDOCA - Universal Journal (Optional)   │
└──────────────────────────────────────────┘

PLUS: Partner tables, configuration tables, text tables...
TOTAL: 25+ tables for a single order
```

### S/4HANA Data Model (Simplified)

In S/4HANA, the same data is **consolidated**:

```
SERVICE ORDER #800001 in S/4HANA

┌─────────────────────────────────────────┐
│ I_SERVICEORDER (CDS View)               │
├─────────────────────────────────────────┤
│ - Combines AUFK + AFKO + JEST + CAUFV   │
│ - Single unified view                   │
│ - No joins needed for basic data        │
└─────────────────────────────────────────┘
         │
         ├──────────────────────────────────┐
         ▼                                  ▼
┌──────────────────────┐      ┌──────────────────────┐
│ I_SERVICEOPERATION   │      │ I_SERVICECOMPONENT   │
├──────────────────────┤      ├──────────────────────┤
│ - AFVC + AFVV        │      │ - RESB + MSEG        │
│ - Includes confirms  │      │ - Material movements │
└──────────────────────┘      └──────────────────────┘
         │
         ▼
┌──────────────────────────────────────────┐
│ ACDOCA (Universal Journal)              │
├──────────────────────────────────────────┤
│ - Single source of financial truth      │
│ - Replaces COEP, COBK, COSS, COSP       │
│ - Real-time cost calculation            │
└──────────────────────────────────────────┘

TOTAL: Accessed via 4 primary CDS Views
       (backed by ~10 tables instead of 25+)
```

**Key Benefits:**
- **90% fewer joins** for common queries
- **Sub-second response** for order display
- **Real-time costing** (no periodic updates)
- **Consistent data** (no sync issues)

---

## Core Data Services (CDS) in Detail

### What are CDS Views?

**CDS Views** replace traditional database views and custom Z-tables. They are:

1. **Semantic**: Include business logic and calculations
2. **Reusable**: Build on top of each other
3. **Annotated**: Carry metadata (labels, help text, data types)
4. **Performance-optimized**: Push logic to HANA database

### CDS View Architecture

```
┌─────────────────────────────────────────────────────┐
│          FIORI APPS / REPORTS                       │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│    CONSUMPTION VIEWS (C_*)                          │
│    - User-facing                                    │
│    - Include UI annotations                         │
│    - Authorization checks                           │
│    Example: C_ServiceOrderTP                        │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│    INTERFACE VIEWS (I_*)                            │
│    - Reusable across apps                           │
│    - Business logic layer                           │
│    - Calculations and derivations                   │
│    Example: I_ServiceOrder                          │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│    BASIC VIEWS (I_*Basic)                           │
│    - Direct table access                            │
│    - Minimal logic                                  │
│    - 1:1 with database tables                       │
│    Example: I_ServiceOrderBasic                     │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│    DATABASE TABLES                                  │
│    AUFK, AFKO, RESB, ACDOCA, etc.                   │
└─────────────────────────────────────────────────────┘
```

### Example: Service Order CDS View

Here's a simplified **I_ServiceOrder** CDS View:

```abap
@AbapCatalog.sqlViewName: 'ISRVCORDER'
@AbapCatalog.compiler.compareFilter: true
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Service Order'

define view I_ServiceOrder
  as select from aufk as Order

  association [0..1] to I_Customer         as _Customer         on $projection.Customer = _Customer.Customer
  association [0..1] to I_Equipment        as _Equipment        on $projection.Equipment = _Equipment.Equipment
  association [0..1] to I_FunctionalLocation as _FunctionalLocation on $projection.FunctionalLocation = _FunctionalLocation.FunctionalLocation
  association [0..*] to I_ServiceOperation as _Operation        on $projection.ServiceOrder = _Operation.ServiceOrder
  association [0..*] to I_ServiceComponent as _Component        on $projection.ServiceOrder = _Component.ServiceOrder
  association [0..1] to I_ServiceOrderCost as _Cost            on $projection.ServiceOrder = _Cost.ServiceOrder

{
      // Key
  key Order.aufnr                          as ServiceOrder,

      // Header Data
      Order.auart                          as ServiceOrderType,
      Order.erdat                          as CreationDate,
      Order.aedat                          as LastChangeDate,
      Order.ernam                          as CreatedBy,
      Order.aenam                          as LastChangedBy,

      // Customer
      @Semantics.customer: true
      Order.kunnr                          as Customer,

      // Equipment
      @Semantics.businessObject.node: true
      Order.equnr                          as Equipment,

      // Functional Location
      Order.tplnr                          as FunctionalLocation,

      // Notification
      Order.qmnum                          as ServiceNotification,

      // Dates
      @Semantics.systemDate.plannedStartDate: true
      Order.gstrp                          as PlannedStartDate,
      @Semantics.systemDate.plannedEndDate: true
      Order.gltrp                          as PlannedEndDate,

      // Status
      @Semantics.systemStatus: true
      case
        when jest.stat = 'I0001' then 'CREATED'
        when jest.stat = 'I0002' then 'RELEASED'
        when jest.stat = 'I0045' then 'TECHNICALLY_COMPLETED'
        when jest.stat = 'I0046' then 'CLOSED'
        else 'UNKNOWN'
      end                                  as SystemStatus,

      // Costs (calculated in real-time)
      @Semantics.amount.currencyCode: 'Currency'
      _Cost.PlannedCost                    as PlannedCost,
      @Semantics.amount.currencyCode: 'Currency'
      _Cost.ActualCost                     as ActualCost,

      Order.waers                          as Currency,

      // Associations (exposed for drill-down)
      _Customer,
      _Equipment,
      _FunctionalLocation,
      _Operation,
      _Component,
      _Cost
}
```

**Key Features of this CDS View:**

1. **@Annotations**: Metadata for tools to understand the data
   - `@Semantics.customer: true` → Tells Fiori this is a customer field
   - `@Semantics.amount.currencyCode` → Links amount to currency

2. **Associations**: Lazy-loaded related data
   - `_Customer` → Navigate to customer master
   - `_Operation` → Get all operations
   - `_Cost` → Real-time cost calculation

3. **Case Logic**: Business rules in the view
   - Status mapping from technical to business-friendly

4. **Calculations**: Done in HANA (not ABAP)
   - `_Cost.PlannedCost` and `_Cost.ActualCost` calculated on-the-fly

### Using CDS Views in Code

**Old ECC Approach (ABAP):**

```abap
" Read service order header
SELECT SINGLE * FROM aufk
  WHERE aufnr = '000800001'
  INTO @DATA(ls_order).

" Get customer name
SELECT SINGLE name1 FROM kna1
  WHERE kunnr = @ls_order-kunnr
  INTO @DATA(lv_customer_name).

" Get operations
SELECT * FROM afvc
  WHERE aufnr = '000800001'
  INTO TABLE @DATA(lt_operations).

" Calculate actual costs
SELECT SUM( wkgbtr ) FROM coep
  WHERE aufnr = '000800001'
  AND bewtyp = '11'  " Actual
  INTO @DATA(lv_actual_cost).
```

**New S/4HANA Approach (CDS):**

```abap
" Single query gets everything
SELECT SINGLE * FROM I_ServiceOrder
  WHERE ServiceOrder = '000800001'
  INTO @DATA(ls_order).

" Customer name via association
DATA(lv_customer_name) = ls_order-_Customer-CustomerName.

" Operations via association
DATA(lt_operations) = ls_order-_Operation.

" Costs already calculated in the view
DATA(lv_actual_cost) = ls_order-ActualCost.
```

**Performance:**
- ECC: 4 separate queries, ~800ms
- S/4HANA: 1 query, ~45ms (17x faster)

---

## Virtual Data Model (VDM)

The **Virtual Data Model** is SAP's standard set of CDS Views that replace direct table access.

### VDM Layers for Customer Service

```
APPLICATION LAYER
├── C_ServiceOrder               (Transactional - Create/Edit)
├── C_ServiceOrderQuery          (Analytical - Reporting)
├── C_ServiceNotification
└── C_Equipment

INTERFACE LAYER
├── I_ServiceOrder               (Core business logic)
├── I_ServiceOperation
├── I_ServiceComponent
├── I_ServiceOrderCost
├── I_ServiceNotification
└── I_Equipment

BASIC LAYER
├── I_ServiceOrderBasic          (Direct table mapping)
├── I_ServiceOperationBasic
└── I_EquipmentBasic
```

### Key VDM Views for CS Module

| CDS View | Purpose | Replaces (ECC) |
|----------|---------|----------------|
| **I_ServiceOrder** | Service order header | AUFK, AFKO, JEST joins |
| **I_ServiceOperation** | Order operations | AFVC, AFVV joins |
| **I_ServiceComponent** | Material components | RESB, MSEG joins |
| **I_ServiceConfirmation** | Time/material confirmations | AFRU, AFFW joins |
| **I_ServiceNotification** | Service notifications | QMEL, QMFE joins |
| **I_Equipment** | Equipment master | EQUI, EQKT joins |
| **I_FunctionalLocation** | Functional locations | IFLOT, IFLOTX joins |
| **I_ServiceOrderCost** | Real-time costing | COEP, COSS aggregation |
| **I_ServiceContract** | Service contracts | VEDA, VBAK joins |

---

## Custom CDS Views for Customer Service

You can create **custom CDS Views** to meet specific business needs.

### Example 1: Overdue Service Orders

```abap
@AbapCatalog.sqlViewName: 'ZCSOVERDUE'
@EndUserText.label: 'Overdue Service Orders'

define view Z_I_OverdueServiceOrders
  as select from I_ServiceOrder

{
  key ServiceOrder,
      ServiceOrderType,
      Customer,
      Equipment,
      PlannedEndDate,
      SystemStatus,
      ActualCost,
      Currency,

      // Calculate days overdue
      dats_days_between( PlannedEndDate, $session.system_date ) as DaysOverdue,

      // Priority flag
      case
        when dats_days_between( PlannedEndDate, $session.system_date ) > 14
          then 'HIGH'
        when dats_days_between( PlannedEndDate, $session.system_date ) > 7
          then 'MEDIUM'
        else 'LOW'
      end as PriorityLevel,

      // Associations
      _Customer,
      _Equipment
}
where
  SystemStatus <> 'CLOSED'
  and PlannedEndDate < $session.system_date
```

**Usage in Fiori App:**

```
Open Fiori Launchpad → Custom Tiles
   → "Overdue Service Orders"

Shows:
┌────────────────────────────────────────────────┐
│ OVERDUE SERVICE ORDERS (23)                    │
├────────┬──────────┬───────────┬──────────────┐
│ Order  │ Customer │ Equipment │ Days Overdue │ Priority │
├────────┼──────────┼───────────┼──────────────┼──────────┤
│ 800045 │ 10005    │ EQ-7821   │ 18 days      │ 🔴 HIGH   │
│ 800052 │ 10012    │ EQ-9004   │ 12 days      │ 🟡 MEDIUM │
│ 800061 │ 10003    │ EQ-5512   │ 5 days       │ 🟢 LOW    │
└────────┴──────────┴───────────┴──────────────┴──────────┘
```

### Example 2: Warranty Claim Analytics

```abap
@AbapCatalog.sqlViewName: 'ZCSWARCLAIM'
@EndUserText.label: 'Warranty Claim Analytics'
@Analytics.dataCategory: #CUBE

define view Z_I_WarrantyClaimAnalytics
  as select from I_ServiceOrder

  association [0..1] to I_Equipment as _Equipment
    on $projection.Equipment = _Equipment.Equipment

{
  @AnalyticsDetails.query.axis: #ROWS
  key ServiceOrder,

  @AnalyticsDetails.query.axis: #ROWS
  ServiceOrderType,

  @AnalyticsDetails.query.axis: #ROWS
  Equipment,

  @AnalyticsDetails.query.axis: #ROWS
  _Equipment.EquipmentCategory,

  @AnalyticsDetails.query.axis: #ROWS
  _Equipment.Manufacturer,

  @AnalyticsDetails.query.axis: #COLUMNS
  CreationDate,

  // Measures
  @DefaultAggregation: #SUM
  @Semantics.amount.currencyCode: 'Currency'
  ActualCost as TotalClaimAmount,

  @DefaultAggregation: #SUM
  @Semantics.amount.currencyCode: 'Currency'
  case
    when ServiceOrderType = 'SM02'  // Warranty
      then ActualCost
    else 0
  end as ManufacturerReimbursement,

  @DefaultAggregation: #AVG
  dats_days_between( CreationDate,
                     cast( _Equipment.WarrantyStartDate as abap.dats ) )
    as DaysUnderWarranty,

  Currency,

  // Associations
  _Equipment
}
where
  ServiceOrderType = 'SM02'  // Only warranty orders
```

**Creates Analytics Dashboard:**

```
WARRANTY CLAIM ANALYTICS (Q4 2024)

By Manufacturer:
┌────────────────┬────────────────┬──────────────────┐
│ Manufacturer   │ Total Claims   │ Avg Days Under   │
│                │ (Reimbursed)   │ Warranty         │
├────────────────┼────────────────┼──────────────────┤
│ Dell           │ $127,400       │ 245 days         │
│ HP             │ $89,200        │ 178 days         │
│ Lenovo         │ $63,800        │ 312 days         │
│ Apple          │ $45,100        │ 156 days         │
└────────────────┴────────────────┴──────────────────┘

Trend:
         │
 $150K   │         ╱╲
         │        ╱  ╲
 $100K   │   ╱╲  ╱    ╲
         │  ╱  ╲╱      ╲╱
  $50K   │ ╱
         │╱
    $0   └─────────────────────
         Jan  Apr  Jul  Oct
```

---

## Table Comparison: ECC vs S/4HANA

### Service Order Tables

| ECC Table | S/4HANA Equivalent | Change |
|-----------|-------------------|--------|
| **AUFK** | AUFK (kept) | Simplified fields |
| **AFKO** | AFKO (kept) | Reduced redundancy |
| **CAUFV** | ❌ Removed | Data merged into AUFK |
| **JEST** | JEST (kept) | Same |
| **TJ02T** | TJ02T (kept) | Status texts |
| **AFVC** | AFVC (kept) | Same |
| **AFVV** | ❌ Removed | Merged into AFVC |
| **AFRU** | AFRU (kept) | Same |
| **RESB** | RESB (kept) | Same |
| **COEP** | ❌ Removed | Replaced by ACDOCA |
| **COBK** | ❌ Removed | Replaced by ACDOCA |
| **COSS** | ❌ Removed | Replaced by ACDOCA |
| **COSP** | ❌ Removed | Replaced by ACDOCA |

**Result:**
- **ECC**: 25+ tables
- **S/4HANA**: 12 tables (48% reduction)
- **Access Method**: CDS Views (not direct SELECT)

### Universal Journal (ACDOCA)

The biggest change is the **Universal Journal**:

**ECC Financial Posting:**
```
Single transaction creates entries in:
├── BKPF  (Accounting document header)
├── BSEG  (Accounting document line items)
├── COEP  (CO line items)
├── COBK  (CO document header)
├── COSS  (Cost totals - actual)
├── COSP  (Cost totals - plan)
├── MLCD  (Material ledger)
└── AUFM  (Goods movements for orders)

8 different tables, potential inconsistency
```

**S/4HANA Financial Posting:**
```
Single transaction creates ONE entry in:
└── ACDOCA  (Universal Journal)

All financial data in one place
```

**ACDOCA Structure (Simplified):**

| Field | Description | Example |
|-------|-------------|---------|
| **RCLNT** | Client | 800 |
| **RLDNR** | Ledger | 0L (Leading) |
| **RBUKRS** | Company Code | 1000 |
| **GJAHR** | Fiscal Year | 2024 |
| **BELNR** | Document Number | 4900012345 |
| **DOCLN** | Line Item | 000001 |
| **RACCT** | G/L Account | 420000 (Labor) |
| **AUFNR** | Order Number | 000800001 |
| **HSL** | Amount (Local Currency) | 450.00 USD |
| **KUNNR** | Customer | 10005 |
| **EQUNR** | Equipment | EQ-7821 |
| **TIMESTAMP** | Creation Timestamp | 2024-10-15 14:32:18 |

**Benefits:**
1. **Single source of truth** (no reconciliation)
2. **Real-time reporting** (no aggregates)
3. **Simplified queries** (one table, not 8)
4. **Complete audit trail** (timestamp-based)

---

## Data Migration Considerations

When migrating from ECC to S/4HANA:

### 1. Data Cleanup Required

**Remove obsolete data:**
- Closed orders older than 7 years
- Archived notifications
- Deleted equipment (with deletion flag)
- Incomplete master data

**Fix data quality issues:**
- Missing cost centers on orders
- Equipment without functional locations
- Notifications without equipment links
- Invalid customer assignments

### 2. Custom Code Adaptation

**Old ECC Code:**
```abap
SELECT aufnr auart kunnr FROM aufk
  INTO TABLE @DATA(lt_orders)
  WHERE erdat IN @s_erdat.

LOOP AT lt_orders INTO DATA(ls_order).
  " Get customer name
  SELECT SINGLE name1 FROM kna1
    WHERE kunnr = @ls_order-kunnr
    INTO @DATA(lv_name).

  " Get costs
  SELECT SUM( wkgbtr ) FROM coep
    WHERE aufnr = @ls_order-aufnr
    INTO @DATA(lv_cost).
ENDLOOP.
```

**New S/4HANA Code:**
```abap
SELECT ServiceOrder, ServiceOrderType, Customer,
       \_Customer-CustomerName as CustomerName,
       ActualCost
  FROM I_ServiceOrder
  INTO TABLE @DATA(lt_orders)
  WHERE CreationDate IN @s_erdat.

" No loop needed - associations handled in one query
" 100x faster performance
```

### 3. Report Adaptation

All custom reports must be rewritten:
- Replace table joins with CDS Views
- Use VDM views (I_* and C_*)
- Leverage HANA calculation engine
- Remove aggregates (use real-time data)

**Migration Effort:**
- Small reports (< 500 lines): 2-4 hours each
- Medium reports (500-2000 lines): 1-2 days each
- Large reports (> 2000 lines): 1-2 weeks each

---

## Performance Comparison

### Query Performance: ECC vs S/4HANA

**Scenario: Display all service orders for customer 10005 with costs**

**ECC (SAP GUI - IW38):**
```
1. Read AUFK table (1,500,000 rows)      → 2.3 sec
2. Join AFKO (1,500,000 rows)            → 1.8 sec
3. Join JEST for status (4,200,000 rows) → 3.1 sec
4. Read COEP for costs (22,000,000 rows) → 5.5 sec
5. Aggregate costs by order              → 2.4 sec
6. Display in ALV grid                   → 0.8 sec
                                    ───────────────
                              TOTAL: 15.9 seconds
```

**S/4HANA (Fiori - Manage Service Orders):**
```
1. Query I_ServiceOrder CDS View         → 0.12 sec
   (with associations to cost data)
2. Render Fiori UI                       → 0.08 sec
                                    ───────────────
                              TOTAL: 0.20 seconds

79x faster!
```

### Data Volume Handling

| Scenario | ECC | S/4HANA | Improvement |
|----------|-----|---------|-------------|
| Display 1 order | 0.8 sec | 0.05 sec | **16x** |
| Display 100 orders | 12.3 sec | 0.18 sec | **68x** |
| Display 1,000 orders | 3.2 min | 1.4 sec | **137x** |
| Analytics (1M orders) | 45 min | 8 sec | **337x** |

---

## Extensibility with CDS Views

### Custom Fields (The S/4HANA Way)

**Old ECC Approach:**
1. Create Z-table (e.g., ZCUST_ORDER)
2. Add custom fields
3. Create function module to read/write
4. Modify screen (Screen Painter)
5. Add user exits

**New S/4HANA Approach:**
1. Extend CDS View with custom fields
2. Add to Fiori app (no coding)

**Example: Add "Warranty Type" to Service Orders**

```abap
@AbapCatalog.sqlViewAppendName: 'ZCSORDEXT'
@EndUserText.label: 'Service Order Extension'

extend view I_ServiceOrder with Z_ServiceOrderExtension
{
  zusage.warranty_type as WarrantyType,
  zusage.warranty_vendor as WarrantyVendor,
  zusage.claim_number as ClaimNumber
}
```

That's it! The field now appears in:
- Fiori apps automatically
- Analytics dashboards
- Custom reports
- API calls

---

## Key Takeaways

### For Functional Consultants

✅ **Learn CDS View names** (not table names)
   - I_ServiceOrder (not AUFK)
   - I_Equipment (not EQUI)
   - I_ServiceNotification (not QMEL)

✅ **Use Fiori apps** (not transaction codes)
   - Manage Service Orders (not IW38)
   - Manage Equipment (not IE03)
   - Service Analytics (not custom reports)

✅ **Think real-time** (not batch jobs)
   - No need for hourly cost updates
   - No need for daily KPI refreshes
   - Everything is live

### For Technical Consultants

✅ **Build on VDM** (don't access tables directly)
   - Use I_* views for extensions
   - Create C_* views for UI
   - Never SELECT from AUFK/AFKO

✅ **Push logic to HANA** (not ABAP)
   - Use CDS calculations
   - Use associations
   - Leverage HANA functions

✅ **Leverage annotations** (for automation)
   - @Semantics for meaning
   - @Analytics for reporting
   - @UI for Fiori generation

### For End Users

✅ **Everything is faster**
   - Order display: 16x faster
   - Reports: 100x+ faster
   - Analytics: real-time

✅ **Better UI**
   - Fiori instead of SAP GUI
   - Mobile-friendly
   - Intuitive navigation

✅ **More insights**
   - Embedded analytics
   - Real-time dashboards
   - Predictive alerts

---

## Next Steps

Now that you understand the simplified data model, explore:

1. **[03_fiori_apps.md](03_fiori_apps.md)** - Learn the Fiori apps that use these CDS Views
2. **[04_embedded_analytics.md](04_embedded_analytics.md)** - Build dashboards with real-time data
3. **[05_mobile_solutions.md](05_mobile_solutions.md)** - Mobile field service using this data model
4. **[06_migration_guide.md](06_migration_guide.md)** - Migrate from ECC to S/4HANA

---

## Practice Exercise

**Create a custom CDS View:**

**Requirement:** Display all service orders for a specific equipment, with:
- Order number, type, status
- Planned vs actual costs
- Days to complete (creation to closure)
- List of operations

**Steps:**
1. Identify base view: `I_ServiceOrder`
2. Add associations to `I_ServiceOperation` and `I_ServiceOrderCost`
3. Add calculated fields for days and cost variance
4. Test in SE16H (HANA table browser)
5. Create Fiori Elements app using the view

**Solution hint:**
```abap
define view Z_I_EquipmentServiceHistory
  as select from I_ServiceOrder
{
  key ServiceOrder,
      Equipment,
      ServiceOrderType,
      SystemStatus,
      _Cost.PlannedCost,
      _Cost.ActualCost,
      _Cost.ActualCost - _Cost.PlannedCost as CostVariance,
      dats_days_between( CreationDate, ClosureDate ) as DaysToComplete,
      _Operation
}
where Equipment is not initial
```

Congratulations! You now understand the S/4HANA data model for Customer Service. This is the foundation for everything else in S/4HANA.
