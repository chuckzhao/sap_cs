# Embedded Analytics in S/4HANA Customer Service

## Overview

**Embedded Analytics** is one of S/4HANA's most powerful features. Instead of extracting data to a separate BI system (like BW), you analyze data **directly in the operational system** in real-time.

For Customer Service, this means:
- **Real-time dashboards** showing current order status
- **KPI tiles** on the Fiori Launchpad
- **Drill-down reports** from summary to detail
- **Predictive analytics** for maintenance forecasting
- **No data latency** (always up-to-date)

## Traditional BI vs Embedded Analytics

### Old Approach (ECC + SAP BW)

```
┌──────────────────────────────────────────────┐
│ SAP ECC (Operational System)                │
│ - Create service orders                     │
│ - Confirm work                               │
│ - Post costs                                 │
└──────────────┬───────────────────────────────┘
               │
               │ Extract (Every night at 2 AM)
               ▼
┌──────────────────────────────────────────────┐
│ SAP BW (Data Warehouse)                      │
│ - Extract orders from AUFK/AFKO             │
│ - Transform data                             │
│ - Load into InfoCubes                        │
│ - Aggregate for performance                  │
└──────────────┬───────────────────────────────┘
               │
               │ Report (Data is 12-36 hours old)
               ▼
┌──────────────────────────────────────────────┐
│ Business Explorer (BEx)                      │
│ - Manager runs "Open Orders Report"          │
│ - Shows data from yesterday                  │
│ - Missing today's 127 new orders             │
└──────────────────────────────────────────────┘

PROBLEMS:
❌ Data is always outdated (12-36 hour lag)
❌ Expensive BW licenses and infrastructure
❌ Complex ETL processes to maintain
❌ Data inconsistencies between ECC and BW
❌ Separate user interfaces (SAP GUI vs BEx)
```

### New Approach (S/4HANA Embedded Analytics)

```
┌──────────────────────────────────────────────┐
│ SAP S/4HANA (Operational + Analytics)        │
│                                              │
│ ┌──────────────────────────────────────┐    │
│ │ Operational Apps                     │    │
│ │ - Create service orders              │    │
│ │ - Confirm work                       │    │
│ │ - Post costs                         │    │
│ └──────────────────────────────────────┘    │
│              │                               │
│              │ (Same database)               │
│              ▼                               │
│ ┌──────────────────────────────────────┐    │
│ │ Analytical Apps                      │    │
│ │ - KPI Tiles (real-time)              │    │
│ │ - Interactive dashboards             │    │
│ │ - Ad-hoc queries                     │    │
│ │ - Predictive models                  │    │
│ └──────────────────────────────────────┘    │
│                                              │
│ Both powered by same CDS Views               │
└──────────────────────────────────────────────┘

BENEFITS:
✅ Data is always current (real-time)
✅ No separate BI system needed
✅ No ETL processes to maintain
✅ Single source of truth
✅ Unified Fiori UI for ops + analytics
```

---

## KPI Tiles on Fiori Launchpad

### What are KPI Tiles?

**KPI Tiles** are live metrics displayed on your Fiori homepage. They update in real-time as data changes.

**Example Fiori Launchpad for Service Manager:**

```
┌─────────────────────────────────────────────────────────────┐
│  SAP Fiori Launchpad - Service Management                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  MY OPEN ORDERS                    OVERDUE ORDERS           │
│  ┌──────────────────┐              ┌──────────────────┐    │
│  │       37         │              │       12         │    │
│  │   ▲ 3 vs. yday   │              │   ▼ 2 vs. yday   │    │
│  └──────────────────┘              └──────────────────┘    │
│                                                             │
│  AVG COMPLETION TIME               CUSTOMER SATISFACTION    │
│  ┌──────────────────┐              ┌──────────────────┐    │
│  │   4.2 days       │              │     4.8 / 5      │    │
│  │   ▼ 0.3 vs. LM   │              │   ▲ 0.2 vs. LM   │    │
│  └──────────────────┘              └──────────────────┘    │
│                                                             │
│  OPEN NOTIFICATIONS                LABOR UTILIZATION        │
│  ┌──────────────────┐              ┌──────────────────┐    │
│  │      142         │              │      87%         │    │
│  │   ▲ 15 vs. yday  │              │   ═══════════░░  │    │
│  └──────────────────┘              └──────────────────┘    │
│                                                             │
│  [Manage Service Orders]  [Service Analytics]              │
│  [Manage Notifications]   [Technician Schedule]            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Key Features:**
- **Live Updates**: Numbers change as data changes
- **Trends**: ▲▼ show improvement/decline
- **Drill-Down**: Click tile to see detailed report
- **Personalized**: Each user sees their own KPIs

### Creating a Custom KPI Tile

**Scenario:** Create a "High-Value Orders" KPI showing orders > $5,000

**Step 1: Create Analytical CDS View**

```abap
@AbapCatalog.sqlViewName: 'ZCSHIGHVALUE'
@EndUserText.label: 'High-Value Orders KPI'
@Analytics.dataCategory: #CUBE

define view Z_C_HighValueOrders
  as select from I_ServiceOrder
{
  @AnalyticsDetails.query.axis: #FREE
  key ServiceOrder,

  @AnalyticsDetails.query.axis: #FREE
  ServiceOrderType,

  @AnalyticsDetails.query.axis: #FREE
  Customer,

  @AnalyticsDetails.query.axis: #FREE
  CreationDate,

  @DefaultAggregation: #SUM
  @Semantics.amount.currencyCode: 'Currency'
  ActualCost,

  Currency
}
where
  ActualCost > 5000
  and SystemStatus <> 'CLOSED'
```

**Step 2: Create Query in Query Browser**

1. Go to Fiori Launchpad
2. Open **Query Browser** (transaction RSRT or app F2926)
3. Create new query:
   - Data Source: `Z_C_HighValueOrders`
   - Measures: Count of ServiceOrder
   - Filters: None (already in view)
   - Save as: `Z_QRY_HIGHVALUE_ORDERS`

**Step 3: Create KPI**

1. Go to **Manage KPIs and Reports** (app F1799)
2. Create New KPI:
   - **Title**: High-Value Orders
   - **Query**: Z_QRY_HIGHVALUE_ORDERS
   - **Evaluation**: Count
   - **Target Value**: 10 (alert if > 10)
   - **Trend**: Daily comparison
3. Save and activate

**Step 4: Add to Launchpad**

1. Open **Manage Launchpad Settings** (admin)
2. Add tile to role `ZSERVICE_MANAGER`
3. Users see the tile immediately

**Result:**

```
┌──────────────────────┐
│ HIGH-VALUE ORDERS    │
│                      │
│         8            │
│   ▼ 3 vs. yesterday  │
│                      │
│ 🟢 Below target (10) │
└──────────────────────┘
```

---

## Interactive Dashboards

### Service Management Cockpit

The **Service Management Cockpit** is a comprehensive dashboard showing all key metrics.

**Dashboard Layout:**

```
┌──────────────────────────────────────────────────────────────────┐
│ SERVICE MANAGEMENT COCKPIT                          [Refresh 🔄]  │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│ OVERVIEW (Last 30 Days)                                          │
│ ┌────────────┬────────────┬────────────┬────────────┐           │
│ │ Created    │ Completed  │ Avg Time   │ Revenue    │           │
│ │   524      │    487     │  3.8 days  │  $428K     │           │
│ └────────────┴────────────┴────────────┴────────────┘           │
│                                                                  │
│ ORDER STATUS BREAKDOWN                                           │
│ ┌──────────────────────────────────────────────────┐            │
│ │ 📊 Status Distribution                            │            │
│ │                                                   │            │
│ │  CREATED      ██████░░░░░░░░░░░░░░░░   37 (7%)   │            │
│ │  RELEASED     ████████████████░░░░░░  142 (27%)  │            │
│ │  IN PROGRESS  ██████████████████████  256 (49%)  │            │
│ │  COMPLETED    ████░░░░░░░░░░░░░░░░░░   87 (17%)  │            │
│ │  CLOSED       ░░░░░░░░░░░░░░░░░░░░░░    2 (0%)   │            │
│ └──────────────────────────────────────────────────┘            │
│                                                                  │
│ TOP 5 CUSTOMERS (By Order Volume)                                │
│ ┌──────────────────────────────────────────────────┐            │
│ │ Customer            Orders    Revenue    Avg Time │            │
│ │ GlobalTech Corp       47      $87,200    2.1 days │            │
│ │ SmartDevices LLC      38      $62,400    4.5 days │            │
│ │ RetailMart Inc        31      $45,800    3.2 days │            │
│ │ HealthPlus Systems    28      $71,200    5.8 days │            │
│ │ AutoFlow Logistics    24      $38,900    2.9 days │            │
│ └──────────────────────────────────────────────────┘            │
│                                                                  │
│ ORDER TYPES                         COST BREAKDOWN               │
│ ┌────────────────────┐             ┌─────────────────────┐      │
│ │  SM01 (Std)  45%   │             │ Labor      62%      │      │
│ │  SM02 (War)  28%   │             │ Materials  31%      │      │
│ │  SM03 (Con)  18%   │             │ Overhead    7%      │      │
│ │  SM04 (Int)   9%   │             └─────────────────────┘      │
│ └────────────────────┘                                          │
│                                                                  │
│ TREND ANALYSIS (Last 12 Months)                                  │
│ ┌──────────────────────────────────────────────────┐            │
│ │  Orders                                          │            │
│ │  600│                        ╱╲                  │            │
│ │     │                   ╱╲  ╱  ╲   ╱╲            │            │
│ │  400│      ╱╲      ╱╲  ╱  ╲╱    ╲ ╱  ╲           │            │
│ │     │     ╱  ╲    ╱  ╲╱          ╲    ╲          │            │
│ │  200│    ╱    ╲  ╱                     ╲         │            │
│ │     │   ╱      ╲╱                       ╲        │            │
│ │    0└──────────────────────────────────────      │            │
│ │     J F M A M J J A S O N D                     │            │
│ └──────────────────────────────────────────────────┘            │
│                                                                  │
│ [📥 Export]  [📊 Custom View]  [🔍 Drill-Down]                  │
└──────────────────────────────────────────────────────────────────┘
```

**Features:**
- **Auto-refresh**: Updates every 30 seconds
- **Drill-down**: Click any metric to see details
- **Filtering**: Select date range, customer, order type
- **Export**: Download to Excel for offline analysis

### Building a Custom Dashboard

**Scenario:** Create "Technician Performance Dashboard"

**Step 1: Create Base CDS View**

```abap
@Analytics.dataCategory: #CUBE
define view Z_I_TechnicianPerformance
  as select from I_ServiceOrder
  association [0..1] to I_ServiceConfirmation as _Confirmation
    on $projection.ServiceOrder = _Confirmation.ServiceOrder
{
  @AnalyticsDetails.query.axis: #ROWS
  _Confirmation.Person as TechnicianID,

  @AnalyticsDetails.query.axis: #ROWS
  _Confirmation._Person.PersonFullName as TechnicianName,

  @AnalyticsDetails.query.axis: #COLUMNS
  CreationDate,

  // Measures
  @DefaultAggregation: #COUNT_DISTINCT
  ServiceOrder as OrderCount,

  @DefaultAggregation: #AVG
  dats_days_between( CreationDate, CompletionDate ) as AvgDaysToComplete,

  @DefaultAggregation: #SUM
  @Semantics.amount.currencyCode: 'Currency'
  ActualCost as TotalRevenue,

  @DefaultAggregation: #AVG
  CustomerSatisfactionScore as AvgSatisfaction,

  Currency
}
```

**Step 2: Create Consumption View for UI**

```abap
@Analytics.query: true
@OData.publish: true
define view Z_C_TechnicianDashboard
  as select from Z_I_TechnicianPerformance
{
  TechnicianID,
  TechnicianName,
  OrderCount,
  AvgDaysToComplete,
  TotalRevenue,
  AvgSatisfaction,

  // Calculate efficiency score (0-100)
  case
    when AvgDaysToComplete <= 3 then 100
    when AvgDaysToComplete <= 5 then 80
    when AvgDaysToComplete <= 7 then 60
    else 40
  end as EfficiencyScore,

  // Determine performance category
  case
    when AvgSatisfaction >= 4.5 and AvgDaysToComplete <= 4
      then 'EXCELLENT'
    when AvgSatisfaction >= 4.0 and AvgDaysToComplete <= 6
      then 'GOOD'
    when AvgSatisfaction >= 3.5 and AvgDaysToComplete <= 8
      then 'AVERAGE'
    else 'NEEDS_IMPROVEMENT'
  end as PerformanceCategory
}
```

**Step 3: Create Fiori Elements App**

1. Use **SAP Business Application Studio**
2. Create new project: Analytical List Page
3. Data source: `Z_C_TechnicianDashboard`
4. Configure visuals:
   - Table with technician details
   - Chart: Orders by technician
   - Chart: Satisfaction trend
5. Deploy to Fiori Launchpad

**Result:**

```
┌──────────────────────────────────────────────────────────────┐
│ TECHNICIAN PERFORMANCE DASHBOARD                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│ Filters: [Last 30 Days ▼] [All Technicians ▼]               │
│                                                              │
│ PERFORMANCE DISTRIBUTION                                      │
│ ┌────────────────────────────────────────────────┐          │
│ │ ⭐ Excellent        5 technicians (25%)        │          │
│ │ ✅ Good            12 technicians (60%)        │          │
│ │ ⚠️  Average         2 technicians (10%)        │          │
│ │ ❌ Needs Improve    1 technician   (5%)        │          │
│ └────────────────────────────────────────────────┘          │
│                                                              │
│ INDIVIDUAL PERFORMANCE                                        │
│ ┌──────────────────────────────────────────────────────────┐│
│ │Name           Orders  Avg Days  Revenue  Satisfaction  ⭐ ││
│ ├──────────────────────────────────────────────────────────┤│
│ │John Smith      47      2.1 d    $87.2K    4.9/5    100%  ││
│ │Maria Garcia    38      3.2 d    $62.4K    4.8/5     95%  ││
│ │David Chen      31      4.5 d    $45.8K    4.6/5     85%  ││
│ │Sarah Johnson   28      5.8 d    $71.2K    4.2/5     70%  ││
│ │Michael Brown   24      2.9 d    $38.9K    4.7/5     90%  ││
│ │...                                                        ││
│ └──────────────────────────────────────────────────────────┘│
│                                                              │
│ [📊 Charts View]  [📥 Export]  [📧 Email Report]            │
└──────────────────────────────────────────────────────────────┘
```

---

## Ad-Hoc Reporting with Query Browser

### What is Query Browser?

**Query Browser** is S/4HANA's self-service reporting tool. No coding required!

**Access:** Fiori Launchpad → **Query Browser** (or transaction RSRT)

### Creating an Ad-Hoc Report

**Scenario:** "Show all warranty orders created last month with costs > $1,000"

**Step 1: Open Query Browser**

```
┌──────────────────────────────────────────────┐
│ Query Browser                                │
├──────────────────────────────────────────────┤
│                                              │
│ Data Source: [I_ServiceOrder           ▼]   │
│                                              │
│ DIMENSIONS (Drag to Rows/Columns):           │
│ ☐ ServiceOrder                               │
│ ☐ ServiceOrderType                           │
│ ☐ Customer                                   │
│ ☐ Equipment                                  │
│ ☐ CreationDate                               │
│ ☐ SystemStatus                               │
│                                              │
│ MEASURES:                                    │
│ ☑ ActualCost                                 │
│ ☐ PlannedCost                                │
│ ☐ CostVariance                               │
│                                              │
│ FILTERS:                                     │
│ ServiceOrderType = 'SM02' (Warranty)         │
│ CreationDate ≥ 2024-09-01                    │
│ CreationDate ≤ 2024-09-30                    │
│ ActualCost > 1000                            │
│                                              │
│ [▶️ Run Query]  [💾 Save]  [📥 Export]        │
└──────────────────────────────────────────────┘
```

**Step 2: View Results**

```
RESULTS (15 orders found):

┌────────────┬──────────┬───────────────┬──────────┐
│ Order      │ Customer │ Equipment     │ Cost     │
├────────────┼──────────┼───────────────┼──────────┤
│ 800234     │ 10005    │ EQ-7821       │ $4,250   │
│ 800241     │ 10012    │ EQ-9004       │ $2,100   │
│ 800255     │ 10003    │ EQ-5512       │ $1,875   │
│ 800267     │ 10008    │ EQ-4423       │ $3,400   │
│ ...        │ ...      │ ...           │ ...      │
└────────────┴──────────┴───────────────┴──────────┘

Total Warranty Cost (Sep 2024): $28,750
```

**Step 3: Save for Reuse**

Click **💾 Save** → Name: "Monthly Warranty Analysis" → Share with team

Now anyone can run this report with one click!

---

## Predictive Analytics

### Predictive Maintenance

S/4HANA can predict when equipment will fail **before it breaks**.

**How it Works:**

```
1. Collect historical data:
   - Equipment failures (last 5 years)
   - Service orders (repair history)
   - Operating hours (from IoT sensors)
   - Environmental data (temperature, usage)

2. Train machine learning model:
   - Identify failure patterns
   - Calculate probability of failure
   - Predict optimal maintenance timing

3. Generate alerts:
   - "Equipment EQ-7821 has 78% chance of failure in next 30 days"
   - "Recommend preventive maintenance now to avoid $12K repair"

4. Create preventive work order:
   - Auto-schedule technician
   - Order parts in advance
   - Minimize downtime
```

**Predictive Maintenance Dashboard:**

```
┌──────────────────────────────────────────────────────────────┐
│ PREDICTIVE MAINTENANCE ALERTS                                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│ HIGH RISK (Next 30 Days)                                      │
│ ┌──────────────────────────────────────────────────────────┐│
│ │ 🔴 Equipment: EQ-7821 (HVAC Unit #12)                    ││
│ │    Failure Probability: 78%                              ││
│ │    Predicted Failure Date: Oct 28, 2024                  ││
│ │    Recommended Action: Replace compressor                ││
│ │    Est. Cost (Preventive): $1,200                        ││
│ │    Est. Cost (Failure): $12,400                          ││
│ │    Savings: $11,200                                      ││
│ │    [📅 Schedule Maintenance]  [📋 View History]          ││
│ └──────────────────────────────────────────────────────────┘│
│                                                              │
│ │ 🟡 Equipment: EQ-9004 (Printer - Bldg A)                 ││
│ │    Failure Probability: 62%                              ││
│ │    Predicted Failure Date: Nov 5, 2024                   ││
│ │    Recommended Action: Replace fuser assembly            ││
│ │    [📅 Schedule]  [📋 History]                            ││
│ └──────────────────────────────────────────────────────────┘│
│                                                              │
│ MEDIUM RISK (Next 60 Days): 12 equipment items               │
│ LOW RISK (Next 90 Days): 45 equipment items                  │
│                                                              │
│ PROJECTED SAVINGS (Last 6 Months):                           │
│ - 47 failures prevented                                      │
│ - $287,000 saved vs. reactive repairs                        │
│ - 98.2% uptime achieved (target: 95%)                        │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Setting Up Predictive Analytics

**Requirements:**
1. **SAP Predictive Analytics** license
2. **Historical data** (minimum 2-3 years)
3. **IoT integration** (optional but recommended)
4. **S/4HANA Cloud** or on-premise with HANA

**Configuration Steps:**

1. **Activate Predictive Scenarios**
   - Go to **Business Functions** (transaction SFW5)
   - Activate: `LOG_EAM_CI_2` (Predictive Maintenance)

2. **Configure Data Sources**
   - Map equipment master data
   - Connect service order history
   - Integrate IoT sensor data (if available)

3. **Train Models**
   - S/4HANA analyzes historical failures
   - Creates equipment-specific models
   - Validates accuracy (typically 75-85%)

4. **Set Alert Thresholds**
   - High Risk: > 70% probability
   - Medium Risk: 40-70%
   - Low Risk: 20-40%

5. **Create Preventive Work Orders**
   - Auto-generate orders for high-risk items
   - Route to maintenance planner
   - Track effectiveness

---

## Real-Time Operational Reporting

### Service Order Completion Report

**Traditional Approach (ECC):**
- Run report at end of day
- See what was completed today
- Data frozen at runtime

**S/4HANA Approach:**
- Report updates every time order is completed
- See completions in real-time
- No refresh needed

**Example: Real-Time Completion Tracker**

```
┌──────────────────────────────────────────────────────────────┐
│ TODAY'S COMPLETED ORDERS (Live)        Last Update: 14:37:22 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│ Target: 25 orders  |  Actual: 18  |  Remaining: 7  (72%)    │
│ Progress: ██████████████████░░░░░░                           │
│                                                              │
│ RECENTLY COMPLETED:                                          │
│ ┌──────────────────────────────────────────────────────────┐│
│ │ 14:37  Order 800567  John Smith    $450   ⏱️ 2.1h       ││
│ │ 14:22  Order 800554  Maria Garcia  $320   ⏱️ 1.8h       ││
│ │ 14:05  Order 800543  David Chen    $780   ⏱️ 3.4h       ││
│ │ 13:48  Order 800532  Sarah Johnson $210   ⏱️ 0.9h       ││
│ │ ...                                                       ││
│ └──────────────────────────────────────────────────────────┘│
│                                                              │
│ BY TECHNICIAN:                                                │
│ John Smith     █████ 5 orders    (28%)                       │
│ Maria Garcia   ████ 4 orders     (22%)                       │
│ David Chen     ███ 3 orders      (17%)                       │
│ Sarah Johnson  ███ 3 orders      (17%)                       │
│ Michael Brown  ██ 2 orders       (11%)                       │
│ Lisa Anderson  █ 1 order         (6%)                        │
│                                                              │
│ REVENUE TODAY: $8,240 / $12,000 target (69%)                 │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

**CDS View for Real-Time Tracking:**

```abap
@Analytics.dataCategory: #CUBE
@Analytics.query: true
define view Z_C_TodayCompletions
  as select from I_ServiceOrder
{
  @AnalyticsDetails.query.axis: #ROWS
  ServiceOrder,

  @AnalyticsDetails.query.axis: #ROWS
  _Confirmation.Person as TechnicianID,

  @AnalyticsDetails.query.axis: #ROWS
  _Confirmation._Person.PersonFullName as TechnicianName,

  @AnalyticsDetails.query.axis: #FREE
  CompletionTime,

  @DefaultAggregation: #SUM
  @Semantics.amount.currencyCode: 'Currency'
  ActualCost,

  @DefaultAggregation: #SUM
  dats_days_between( CreationDate, CompletionDate ) as DurationHours,

  Currency
}
where
  CompletionDate = $session.system_date
  and SystemStatus = 'COMPLETED'
```

---

## Analytics Path Framework

### What is APF?

**Analytics Path Framework** creates guided analytics experiences.

**Example: Customer Service Performance Explorer**

```
Step 1: Select Time Period
   └─→ Last 30 Days

Step 2: View Overall Metrics
   └─→ 524 orders, $428K revenue, 3.8 day avg

Step 3: Drill into Problem Area
   └─→ "Overdue Orders" (12)

Step 4: Analyze Root Cause
   └─→ Group by technician
       └─→ Sarah Johnson: 8 overdue (67%)

Step 5: View Details
   └─→ List of Sarah's 8 overdue orders

Step 6: Take Action
   └─→ Reassign to John Smith
```

**Creating an APF Application:**

1. Define analytical steps (6 steps above)
2. Create CDS Views for each step
3. Configure navigation paths
4. Build Fiori app using APF template
5. Deploy to Launchpad

Users can explore data **without technical knowledge**.

---

## Performance Comparison

### Query Performance

| Report Type | ECC | S/4HANA | Improvement |
|-------------|-----|---------|-------------|
| Simple list (100 orders) | 3.2 sec | 0.08 sec | **40x** |
| Aggregated report (1M orders) | 45 min | 8 sec | **337x** |
| Real-time dashboard | N/A | 0.2 sec | New capability |
| Predictive analytics | N/A | 2 sec | New capability |

### Data Freshness

| Scenario | ECC + BW | S/4HANA |
|----------|----------|---------|
| Order completion → Report | 12-36 hours | **Instant** |
| Cost posting → Dashboard | 24 hours | **Instant** |
| New customer → Analytics | Next day | **Instant** |

---

## Best Practices

### 1. Design for Performance

✅ **Use CDS Views** (not custom queries)
- Pre-optimized by SAP
- Leverage HANA engine
- Reusable across apps

✅ **Filter Early**
- Add WHERE clauses in base views
- Don't retrieve unnecessary data
- Use date ranges

❌ **Avoid:**
- SELECT * FROM table (use CDS)
- Aggregating in ABAP (use HANA)
- Nested loops (use associations)

### 2. Think Real-Time

✅ **Eliminate Batch Jobs**
- No nightly aggregations
- No periodic cost updates
- No data warehouse extracts

✅ **Design for Live Data**
- KPI tiles auto-refresh
- Dashboards show current state
- Alerts trigger immediately

### 3. Reuse Standard Content

SAP provides **200+ standard CDS Views** for CS module:

- I_ServiceOrder
- I_ServiceNotification
- I_ServiceContract
- I_Equipment
- I_TechnicalObject
- I_MaintenancePlan

**Always check standard content first** before creating custom views.

### 4. Leverage Associations

**Bad (ECC style):**
```abap
SELECT aufnr FROM aufk WHERE kunnr = '10005'.
LOOP AT orders.
  SELECT SINGLE name1 FROM kna1 WHERE kunnr = orders-kunnr.
ENDLOOP.
```

**Good (S/4HANA style):**
```abap
SELECT ServiceOrder, _Customer-CustomerName
  FROM I_ServiceOrder
  WHERE Customer = '10005'.
```

**Associations:**
- Fetch related data on-demand
- No performance penalty
- Cleaner code

---

## Hands-On Exercise

### Create "Top 10 Customers" Dashboard

**Requirements:**
1. Show top 10 customers by order count (last 90 days)
2. Display customer name, order count, total revenue
3. Add trend vs. previous 90 days
4. Create KPI tile showing total revenue from top 10

**Solution Steps:**

**Step 1: Create Base CDS View**

```abap
@Analytics.dataCategory: #CUBE
define view Z_I_CustomerOrderAnalytics
  as select from I_ServiceOrder
{
  @AnalyticsDetails.query.axis: #ROWS
  Customer,

  @AnalyticsDetails.query.axis: #ROWS
  _Customer.CustomerName,

  @AnalyticsDetails.query.axis: #COLUMNS
  CreationDate,

  @DefaultAggregation: #COUNT
  ServiceOrder as OrderCount,

  @DefaultAggregation: #SUM
  @Semantics.amount.currencyCode: 'Currency'
  ActualCost as TotalRevenue,

  Currency
}
where
  CreationDate >= dats_add_days( $session.system_date, -90 )
  and SystemStatus <> 'CANCELLED'
```

**Step 2: Create Consumption View**

```abap
@Analytics.query: true
@OData.publish: true
define view Z_C_Top10Customers
  as select from Z_I_CustomerOrderAnalytics
{
  Customer,
  CustomerName,
  OrderCount,
  TotalRevenue,
  Currency
}
order by OrderCount descending
```

**Step 3: Create Query in Query Browser**
- Data Source: Z_C_Top10Customers
- Top 10 rows only
- Save as: Z_QRY_TOP10_CUSTOMERS

**Step 4: Create Dashboard**
- Use Fiori Elements: Analytical List Page
- Add table visualization
- Add chart (bar chart: customers vs revenue)
- Deploy

**Step 5: Create KPI Tile**
- Manage KPIs → New
- Query: Z_QRY_TOP10_CUSTOMERS
- Measure: SUM of TotalRevenue
- Add to Launchpad

**Result:**

```
┌──────────────────────────────────────────────────────────────┐
│ TOP 10 CUSTOMERS (Last 90 Days)                              │
├──────────────────────────────────────────────────────────────┤
│ Rank Customer Name        Orders  Revenue    vs. Prev 90d    │
│  1   GlobalTech Corp        47    $87,200    ▲ 12%          │
│  2   SmartDevices LLC       38    $62,400    ▲ 8%           │
│  3   RetailMart Inc         31    $45,800    ▼ 3%           │
│  4   HealthPlus Systems     28    $71,200    ▲ 15%          │
│  5   AutoFlow Logistics     24    $38,900    ▲ 5%           │
│  6   TechStart Solutions    22    $34,100    ▼ 8%           │
│  7   MegaCorp Industries    19    $52,300    ▲ 22%          │
│  8   QuickServe Partners    17    $28,700    ▲ 3%           │
│  9   DataVault Systems      15    $41,200    ▼ 12%          │
│ 10   CloudNine Enterprises  14    $26,900    ▲ 7%           │
│                                                              │
│ TOTAL: $488,700 (72% of all revenue)                         │
│                                                              │
│ [📊 Chart View]  [📥 Export]  [📧 Email]                     │
└──────────────────────────────────────────────────────────────┘
```

---

## Next Steps

Now that you understand embedded analytics:

1. **[05_mobile_solutions.md](05_mobile_solutions.md)** - Mobile analytics for field technicians
2. **[06_migration_guide.md](06_migration_guide.md)** - Migrate reports from ECC to S/4HANA
3. **SAP Learning Hub** - Take course "S4F20 - Embedded Analytics in S/4HANA"

---

## Summary

**Embedded Analytics** transforms how you work:

✅ **Real-time data** (not yesterday's data)
✅ **No separate BI system** (lower cost)
✅ **Self-service** (business users create reports)
✅ **Predictive** (prevent problems before they occur)
✅ **Unified UI** (Fiori for ops + analytics)

**Key Technologies:**
- CDS Views (data modeling)
- Query Browser (ad-hoc reporting)
- KPI Tiles (real-time metrics)
- APF (guided exploration)
- Predictive Analytics (ML-powered insights)

You now have the tools to make **data-driven decisions in real-time**!
