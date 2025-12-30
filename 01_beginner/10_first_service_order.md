# Module 4.1: Your First Service Order

## Introduction

Now it's time to get hands-on! In this module, you'll learn to create your first service order in SAP CS. This is where theory meets practice.

## What is a Service Order?

A **Service Order** is a document that:
- Records a service to be performed
- Plans resources (labor, materials, tools)
- Tracks costs
- Controls workflow
- Enables billing

Think of it as a "work ticket" for service technicians.

## Service Order vs Service Notification

Understanding the difference is crucial:

| Service Notification | Service Order |
|---------------------|---------------|
| Request for service | Authorization to perform service |
| Customer perspective | Company perspective |
| Simple documentation | Detailed planning |
| No cost tracking | Full cost tracking |
| Quick to create | More comprehensive |

**Typical Flow:**
```
Customer calls → Create Notification (IW21) → Create Service Order (IW31) → Execute Service → Confirm → Bill Customer
```

## Service Order Structure

```
Service Order
├── Header
│   ├── Order Number
│   ├── Order Type
│   ├── Priority
│   ├── Customer
│   └── Equipment
├── Operations
│   ├── Operation 10: Diagnose problem
│   ├── Operation 20: Replace part
│   └── Operation 30: Test equipment
├── Components (Materials)
│   ├── Spare Part 1
│   └── Spare Part 2
├── Costs
│   ├── Labor costs
│   ├── Material costs
│   └── Overhead
└── Dates
    ├── Created on
    ├── Requested start
    └── Scheduled completion
```

## Creating Your First Service Order

### Transaction: IW31

### Step-by-Step Guide

#### Step 1: Access the Transaction

1. Open SAP GUI
2. In command field, type: `/nIW31`
3. Press Enter

#### Step 2: Initial Screen

You'll see the **Create Service Order** screen.

**Required Fields:**
- **Order Type**: Type of service order (e.g., SM01 = Service Order)
- **Priority**: Urgency level (1=Very High, 2=High, 3=Medium, 4=Low)

**Optional but Important:**
- **Functional Location**: Where the equipment is located
- **Equipment**: What equipment needs service
- **Reference**: Reference to notification or other document

#### Step 3: Enter Header Data

```
Create Service Order - Initial Screen
─────────────────────────────────────────
Order Type:         SM01
Priority:           3
Functional Loc:     _____________ [F4]
Equipment:          _____________ [F4]
Reference Notif:    _____________ [F4]
Description:        _________________
```

**Example:**
```
Order Type:         SM01
Priority:           2
Equipment:          10000001
Description:        Repair coffee machine
```

Press **Enter** to continue.

#### Step 4: Complete Header Details

You'll see tabs:
- **General**: Basic order info
- **Dates**: Scheduling information
- **Control**: Workflow settings
- **Customer**: Customer data
- **Settlement**: Billing settings

**General Tab:**
```
Order:              [Auto-generated]
Order Type:         SM01
Description:        Repair coffee machine
Priority:           2
System Status:      CRE (Created)
Work Center:        _________ [F4]
```

**Key Fields:**
- **Work Center**: Which team/person handles this
- **Plant**: Location where work is performed
- **Main Work Center**: Primary resource

#### Step 5: Add Operations

Operations are the individual tasks within the order.

1. Click on **Operations** tab or button
2. Click **New Entries** or press F5

**Operation Entry:**
```
Operation: 0010
Description: Diagnose machine issue
Work Center: SERV-01
Duration: 1.0 hours

Operation: 0020
Description: Replace heating element
Work Center: SERV-01
Duration: 2.0 hours

Operation: 0030
Description: Test and verify repair
Work Center: SERV-01
Duration: 0.5 hours
```

**Operation numbering:**
- Usually increments by 10 (0010, 0020, 0030)
- Allows inserting operations between (0015, 0025)

#### Step 6: Add Components (Spare Parts)

1. Click on **Components** tab
2. Click **New Entries**

**Component Entry:**
```
Item:       0010
Material:   HEATING-ELEM-001
Description: Heating Element
Quantity:   1
Unit:       EA (Each)

Item:       0020
Material:   GASKET-SEAL-55
Description: Gasket Seal
Quantity:   2
Unit:       EA
```

**Where do components come from?**
- Warehouse/stock
- Specified in equipment BOM
- Ordered from vendor (if not in stock)

#### Step 7: Customer Data

1. Click on **Customer** tab

```
Customer Number: 1000567
Contact Person:  John Smith
Phone:          555-0123
Email:          john.smith@company.com
```

This links the service order to the customer for billing.

#### Step 8: Dates and Scheduling

1. Click on **Dates** tab

```
Basic Start Date:    01/15/2024
Basic Finish Date:   01/15/2024
Requested Start:     01/15/2024  08:00
Requested Finish:    01/15/2024  17:00
```

**Date Types:**
- **Basic Dates**: Planned without detailed scheduling
- **Scheduled Dates**: After scheduling with capacity check
- **Actual Dates**: When work actually occurred

#### Step 9: Save the Order

1. Review all entered data
2. Click **Save** button (💾) or press Ctrl+S
3. You'll see a message:

```
✓ Order 600000123 has been saved
```

**Congratulations!** You've created your first service order!

The order number (e.g., 600000123) is auto-generated by SAP.

## Understanding Order Status

### System Status

SAP automatically sets status based on order lifecycle:

| Status | Code | Meaning |
|--------|------|---------|
| Created | CRE | Order just created |
| Released | REL | Ready for work |
| Print Pending | PRNP | Awaiting print |
| Technically Complete | TECO | Work done, awaiting final steps |
| Closed | CLSD | Order completed and closed |

### User Status (Optional)

You can configure custom statuses:
- Waiting for parts
- Technician assigned
- Customer approved
- On hold
- etc.

## Releasing the Order

Before work can begin, the order must be **Released**.

### How to Release

**Method 1: At creation**
- Check "Release immediately" checkbox

**Method 2: After creation**
1. Go to IW32 (Change Service Order)
2. Enter order number
3. Click menu: `Functions → Release`

**What happens when released?**
- ✓ Materials can be withdrawn
- ✓ Work can be confirmed
- ✓ Costs can be posted
- ✓ Status changes to REL

## Viewing Your Service Order

### Transaction: IW33 (Display Order)

1. Execute `/nIW33`
2. Enter order number: `600000123`
3. Press Enter

You'll see the order in display mode (no changes allowed).

### Key Information to Check

**Header:**
- Order number
- Description
- Status
- Priority
- Customer

**Operations:**
- All planned tasks
- Duration
- Work center

**Components:**
- Materials required
- Quantities
- Availability

**Costs:**
- Planned costs
- Actual costs (after confirmation)

## Practical Example: Complete Scenario

### Scenario: Coffee Machine Repair

**Background:**
- Customer calls about broken coffee machine
- Machine: Equipment 10000001
- Location: Office Building A, Floor 3
- Problem: No heating

**Step 1: Create Notification (IW21)**
```
Notification Type: S1 (Service Request)
Equipment: 10000001
Description: Coffee machine not heating water
Priority: 2
```
Result: Notification 100000456 created

**Step 2: Create Service Order (IW31)**
```
Order Type: SM01
Reference Notification: 100000456
Priority: 2
Equipment: 10000001
Description: Repair coffee machine - no heat
```

**Step 3: Add Operations**
```
0010 - Diagnose heating issue (1h)
0020 - Replace heating element (2h)
0030 - Test functionality (0.5h)
0040 - Clean and preventive check (0.5h)
```

**Step 4: Add Components**
```
HEATING-ELEM-001 x1
GASKET-SEAL-55 x2
DESCALING-CHEM x1
```

**Step 5: Schedule**
```
Planned Start: Tomorrow, 08:00
Planned Finish: Tomorrow, 12:00
```

**Step 6: Assign Technician**
```
Work Center: SERV-TECH-01
Responsible: Mike Johnson
```

**Step 7: Save and Release**
```
✓ Order 600000123 saved
✓ Order 600000123 released
```

## Common Mistakes to Avoid

❌ **Not linking to equipment**
- Always link orders to equipment for history tracking

❌ **Skipping operations**
- Operations are essential for planning and tracking

❌ **Wrong priority**
- Be realistic about urgency

❌ **Not releasing the order**
- Unreleased orders can't be worked on

❌ **Missing customer data**
- Needed for billing!

❌ **No components added**
- Add expected parts for planning

## Best Practices

✅ **Always reference a notification** if one exists
✅ **Use descriptive operation text** for clarity
✅ **Add expected materials upfront** for inventory planning
✅ **Set realistic dates** based on resource availability
✅ **Assign work center** to ensure accountability
✅ **Check equipment history** before planning
✅ **Add notes** for technicians in long text
✅ **Release promptly** to avoid delays

## Order Types

Different order types for different purposes:

| Order Type | Purpose |
|------------|---------|
| SM01 | Standard service order |
| SM02 | Warranty service |
| SM03 | Service contract |
| SM04 | Internal service |
| SM05 | Preventive maintenance |

Your organization may have custom order types!

## Quick Reference: Service Order Lifecycle

```
1. CREATE (IW31)
   ↓
2. PLAN (Add operations, materials, schedule)
   ↓
3. RELEASE (Make available for execution)
   ↓
4. CONFIRM (Record work done - covered later)
   ↓
5. TECHNICALLY COMPLETE (All work done)
   ↓
6. SETTLE (Transfer costs - FI integration)
   ↓
7. CLOSE (Final step)
```

## Hands-On Exercise

### Exercise 1: Basic Service Order

Create a service order for:
- **Scenario**: Printer repair in IT department
- **Equipment**: Create fictional equipment if needed
- **Problem**: Paper jam mechanism broken
- **Operations**:
  - Diagnose (30 min)
  - Replace roller (1 hour)
  - Test (15 min)
- **Materials**: Printer roller, cleaning kit

### Exercise 2: Service Order from Notification

1. Create a notification first (IW21)
2. Create order referencing that notification (IW31)
3. Display both documents
4. Check the link between them

### Exercise 3: Explore an Existing Order

If you have access to orders:
1. Execute IW33
2. Use F4 to find any order
3. Explore all tabs
4. Review operations and components
5. Check status and dates

## Summary

You learned how to:

✅ Understand what service orders are
✅ Know the difference between notifications and orders
✅ Create a service order (IW31)
✅ Add header data
✅ Create operations
✅ Add components/materials
✅ Set customer data
✅ Schedule the order
✅ Save and release
✅ Display orders (IW33)
✅ Understand order lifecycle

## Key Transaction Codes

```
IW31 - Create Service Order
IW32 - Change Service Order
IW33 - Display Service Order
IW38 - Service Order List
```

## Next Steps

You've created your first service order! Next, learn about:
[Module 4.2: Service Notifications (IW21/IW22)](11_service_notifications.md)

## Additional Practice

Try creating orders for these scenarios:
1. Air conditioning repair
2. Computer hardware replacement
3. Vehicle service
4. Equipment installation
5. Preventive maintenance check

The more you practice, the more comfortable you'll become! 🎯
