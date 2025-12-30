# Module 2.1: Customer Master Data

## Introduction

Customer master data is the foundation of SAP CS. Every service you provide is linked to a customer, making it essential to understand how customer data works.

## What is Customer Master Data?

The **Customer Master** contains all information about your customers:
- Who they are (name, address, contact)
- Where they're located (address, geography)
- How to bill them (payment terms, pricing)
- Credit information (credit limit, payment history)
- Service-specific data (equipment owned, service contracts)

## Customer Master Structure

### Three-Level Structure

SAP customer master has three organizational levels:

```
Customer Master
├── General Data (Company-wide)
│   └── Valid for all company codes
├── Company Code Data (Accounting)
│   └── Specific to each company code
└── Sales Area Data (Sales/Service)
    └── Specific to each sales org + distribution channel + division
```

### Why Three Levels?

**Example: Global Corporation**
```
Customer: ACME Corp (Customer # 1000)

General Data (applies everywhere):
├── Name: ACME Corporation
├── Address: 123 Main St, New York
├── Contact: John Smith
└── Phone: 555-0123

Company Code Data (US Operations - CC 1000):
├── Reconciliation Account: 140000
├── Payment Terms: Net 30
├── Credit Limit: $100,000
└── Payment Method: Bank Transfer

Company Code Data (UK Operations - CC 2000):
├── Reconciliation Account: 240000
├── Payment Terms: Net 45
├── Credit Limit: £75,000
└── Payment Method: Direct Debit

Sales Area Data (Service Division):
├── Pricing Procedure: ZSERV01
├── Service Terms: Premium Support
└── Discount Group: Corporate
```

## Customer Master Transactions

### Core Transactions

| Transaction | Description | When to Use |
|-------------|-------------|-------------|
| **XD01** | Create Customer (General) | Create new customer (all levels) |
| **XD02** | Change Customer (General) | Modify existing customer |
| **XD03** | Display Customer (General) | View customer details |
| **VD01** | Create Customer (Sales) | Create customer for sales/service |
| **VD02** | Change Customer (Sales) | Modify sales data |
| **VD03** | Display Customer (Sales) | View sales-specific data |
| **XD05** | Block/Unblock Customer | Block customer for credit issues |
| **XD06** | Mark for Deletion | Flag customer for deletion |

### Best Practice:
💡 Use **VD01/VD02/VD03** for service-related customer management
💡 Use **XD01/XD02/XD03** when you need to maintain all levels

## Practice Example 1: Displaying a Customer

### Step-by-Step: Display Customer Master (XD03)

**Scenario:** You need to view customer "TechCorp Inc." details before creating a service order.

**Step 1: Execute Transaction**
```
Command: /nXD03
Press: Enter
```

**Step 2: Initial Screen**
```
┌──────────────────────────────────────────────┐
│ Display Customer: Initial Screen             │
├──────────────────────────────────────────────┤
│                                              │
│ Customer:          [1000567    ] 🔍         │
│ Company Code:      [1000       ] 🔍         │
│ Sales Organization:[1000       ] 🔍         │
│ Distribution Chan: [10         ] 🔍         │
│ Division:          [01         ] 🔍         │
│                                              │
│ [✓] General Data                            │
│ [✓] Company Code Data                       │
│ [✓] Sales Area Data                         │
│                                              │
│         [Execute]  [Cancel]                  │
└──────────────────────────────────────────────┘
```

**Important Notes:**
⚠️ **Customer number is required** - Use F4 if you don't know it
💡 **Company code optional** - Leave blank to see general data only
💡 **Sales org optional** - Fill in to see sales area data

**Step 3: Search for Customer (if you don't know number)**
```
Click on 🔍 (F4) next to Customer field

Search Screen:
┌──────────────────────────────────────────────┐
│ Customer Search Help                         │
├──────────────────────────────────────────────┤
│ Customer Name:     [TechCorp*  ]            │
│ City:              [          ]             │
│ Postal Code:       [          ]             │
│ Country:           [US        ]             │
│                                              │
│         [Execute F8]                         │
└──────────────────────────────────────────────┘

Results:
┌────────────────────────────────────────────────────────┐
│ Customer │ Name              │ City      │ Country    │
├──────────┼───────────────────┼───────────┼────────────┤
│ 1000567  │ TechCorp Inc.     │ New York  │ US         │
│ 1000789  │ TechCorp Ltd.     │ London    │ UK         │
│ 1001234  │ TechCorp GmbH     │ Berlin    │ DE         │
└────────────────────────────────────────────────────────┘

Double-click on the customer you want
```

**Search Tips:**
- Use `*` for wildcard: `Tech*` finds all starting with "Tech"
- Search by city, postal code, or country
- Use search term field for quick lookup

**Step 4: View General Data**
```
┌──────────────────────────────────────────────────────────┐
│ Display Customer: General Data                           │
├──────────────────────────────────────────────────────────┤
│ Customer:  1000567          TechCorp Inc.               │
│                                                          │
│ [Address]  [Control Data]  [Payment Trans]  [Contact]   │
├──────────────────────────────────────────────────────────┤
│ Address Tab:                                             │
│                                                          │
│ Name:          TechCorp Inc.                            │
│ Search Term:   TECHCORP                                 │
│ Street:        456 Technology Drive                      │
│ House Number:  Suite 100                                │
│ Postal Code:   10001                                    │
│ City:          New York                                 │
│ Country:       US - United States                        │
│ Region:        NY - New York                            │
│                                                          │
│ Communication:                                           │
│ Telephone:     +1 212-555-0100                          │
│ Mobile:        +1 212-555-0199                          │
│ Fax:           +1 212-555-0101                          │
│ Email:         service@techcorp.com                      │
│ Website:       www.techcorp.com                          │
│                                                          │
│ Language:      EN - English                             │
│ Time Zone:     EST - Eastern Standard Time              │
└──────────────────────────────────────────────────────────┘
```

**What to Look For:**
✓ **Correct address** - Important for service dispatch
✓ **Contact information** - Phone, email for communication
✓ **Language** - For communication and documents
✓ **Search term** - Quick lookup code

**Step 5: View Company Code Data**
```
Click on "Company Code Data" button

┌──────────────────────────────────────────────────────────┐
│ Display Customer: Company Code Data (CC 1000)            │
├──────────────────────────────────────────────────────────┤
│ [Account Management]  [Payment Trans]  [Correspondence]  │
├──────────────────────────────────────────────────────────┤
│ Account Management Tab:                                  │
│                                                          │
│ Company Code:        1000 - US Operations               │
│ Reconciliation Acct: 140000 - Accounts Receivable       │
│ Sort Key:            001 - Posting date                 │
│                                                          │
│ Payment Transactions:                                    │
│ Payment Terms:       Z030 - Net 30 Days                │
│ Payment Methods:     [ T ] Bank Transfer               │
│                      [   ] Check                        │
│                      [   ] Credit Card                  │
│                                                          │
│ Credit Management:                                       │
│ Credit Limit:        100,000.00 USD                     │
│ Credit Exposure:      23,450.00 USD (23%)               │
│ Available Credit:     76,550.00 USD                     │
│ Credit Control Area: 1000                               │
│                                                          │
│ Status:              ⚫ Active                           │
│                      [ ] Blocked for Posting            │
│                      [ ] Blocked for Payment            │
└──────────────────────────────────────────────────────────┘
```

**Critical Information:**
✓ **Credit limit** - Can they afford this service?
✓ **Credit exposure** - How much do they owe?
✓ **Blocked status** - Can we create orders/invoices?
✓ **Payment terms** - When will they pay?

**Important Notes:**
⚠️ **BLOCKED CUSTOMER** - Cannot create new orders!
💡 **Credit exposure** - Outstanding invoices + new orders
💡 **Payment terms** - Affects invoice due date

**Step 6: View Sales Area Data**
```
Click on "Sales Area Data" button

┌──────────────────────────────────────────────────────────┐
│ Display Customer: Sales Area Data                        │
├──────────────────────────────────────────────────────────┤
│ Sales Org:     1000 - North America                     │
│ Dist. Channel: 10 - Service                             │
│ Division:      01 - Equipment Service                    │
│                                                          │
│ [Sales]  [Shipping]  [Billing]  [Partner Functions]     │
├──────────────────────────────────────────────────────────┤
│ Sales Tab:                                               │
│                                                          │
│ Sales District:      US-EAST                            │
│ Sales Office:        NYC                                │
│ Sales Group:         SERVICE-TECH                        │
│ Customer Group:      CORPORATE                          │
│                                                          │
│ Pricing:                                                 │
│ Price List:          10 - Standard Service Pricing      │
│ Price Group:         01 - Corporate Pricing             │
│ Pricing Procedure:   ZSERV01                            │
│ Customer Discount:   5% - Corporate Discount            │
│                                                          │
│ Service Terms:                                           │
│ Service Level:       PREMIUM - Premium Support          │
│ Response Time SLA:   4 hours                            │
│ Resolution SLA:      24 hours                           │
│                                                          │
│ Shipping:                                                │
│ Delivery Priority:   02 - High Priority                │
│ Shipping Conditions: 01 - Standard                      │
└──────────────────────────────────────────────────────────┘
```

**Service-Critical Information:**
✓ **Service level** - What response time promised?
✓ **Pricing procedure** - How to price services?
✓ **Customer discount** - Automatic discount applies
✓ **Sales office/group** - Who manages this customer?

## Practice Example 2: Creating a Customer

### Step-by-Step: Create Customer Master (VD01)

**Scenario:** Create new customer "SmartDevices LLC" for service division.

**Step 1: Execute Transaction**
```
Command: /nVD01
Press: Enter
```

**Step 2: Initial Screen**
```
┌──────────────────────────────────────────────┐
│ Create Customer: Initial Screen              │
├──────────────────────────────────────────────┤
│                                              │
│ Customer:          [________  ] (leave blank)│
│ Sales Organization:[1000      ] *           │
│ Distribution Chan: [10        ] *           │
│ Division:          [01        ] *           │
│                                              │
│ Account Group:     [KUNA      ] * 🔍       │
│                                              │
│         [Continue]  [Cancel]                 │
└──────────────────────────────────────────────┘
```

**Field Explanations:**
- **Customer**: Leave blank - system assigns number
- **Sales Organization**: Your service organization (required)
- **Distribution Channel**: Service channel (required)
- **Division**: Service division (required)
- **Account Group**: Customer type (KUNA = Sold-to-party)

**Important Notes:**
⚠️ **Account group determines fields** - Different groups = different fields
💡 **Number range automatic** - Based on account group
💡 **External numbering** - You assign number (if configured)

**Step 3: Enter General Data - Address**
```
┌──────────────────────────────────────────────────────────┐
│ Create Customer: General Data - Address                  │
├──────────────────────────────────────────────────────────┤
│ Customer:  (will be assigned)                           │
│                                                          │
│ [Address]  [Control Data]  [Payment Trans]              │
├──────────────────────────────────────────────────────────┤
│ Title:        [ ] Company                                │
│                                                          │
│ Name 1:       [SmartDevices LLC              ] *        │
│ Name 2:       [_____________________________]           │
│ Search Term:  [SMARTDEV                     ] *        │
│                                                          │
│ Street:       [789 Innovation Blvd           ]          │
│ House Number: [Building 3                   ]          │
│ Postal Code:  [94102                        ]          │
│ City:         [San Francisco                ] *        │
│ Country:      [US                           ] * 🔍     │
│ Region:       [CA                           ] 🔍       │
│                                                          │
│ Telephone:    [+1 415-555-0200              ]          │
│ Mobile:       [+1 415-555-0299              ]          │
│ Email:        [support@smartdevices.com     ]          │
│                                                          │
│ Language:     [EN] * 🔍  Time Zone: [PST] 🔍          │
└──────────────────────────────────────────────────────────┘
```

**Field Tips:**
✓ **Name 1** - Required, company legal name
✓ **Search term** - Short code for quick search (auto-populated)
✓ **Country** - Required, affects tax calculation
✓ **City** - Required for address
✓ **Phone/Email** - Highly recommended for service coordination
✓ **Language** - Determines document language
✓ **Time zone** - Important for service scheduling

**Common Mistakes:**
❌ Forgetting to fill required fields (marked with *)
❌ Entering inconsistent address format
❌ Wrong country code (affects taxes!)
❌ Missing contact information

**Step 4: Control Data**
```
Click on "Control Data" tab

┌──────────────────────────────────────────────────────────┐
│ Create Customer: Control Data                            │
├──────────────────────────────────────────────────────────┤
│ Corporate Data:                                          │
│ Industry:         [TECH   ] Technology                  │
│ Industry Code 1:  [IT-HW  ] IT Hardware                 │
│                                                          │
│ Authorization:                                           │
│ Authorization:    [________] (optional)                 │
│                                                          │
│ Data Communication:                                      │
│ Telebox Number:   [________]                            │
│ Telephone Number: [+1 415-555-0200]                     │
│                                                          │
│ Tax Information:                                         │
│ Tax Number 1:     [12-3456789  ]                        │
│ Tax Number 2:     [________]                            │
│ Equalization Tax: [ ] (leave unchecked usually)         │
│                                                          │
│ Marketing:                                               │
│ Nielsen ID:       [________]                            │
│ Annual Sales:     [1000000.00] USD                      │
│ Employees:        [50]                                  │
└──────────────────────────────────────────────────────────┘
```

**What to Fill:**
✓ **Industry** - Customer's business type (helps reporting)
✓ **Tax numbers** - Required for invoicing
✓ **Annual sales** - Optional, for analytics
✓ **Employees** - Optional, for customer categorization

**Step 5: Company Code Data**
```
System automatically navigates to Company Code screen

┌──────────────────────────────────────────────────────────┐
│ Create Customer: Company Code Data (1000)                │
├──────────────────────────────────────────────────────────┤
│ [Account Management]  [Payment Transactions]             │
├──────────────────────────────────────────────────────────┤
│ Account Management:                                      │
│                                                          │
│ Company Code:        1000 - US Operations               │
│ Reconciliation Acct: [140000  ] * 🔍                    │
│ Head Office:         [________]                         │
│ Sort Key:            [001     ] 🔍                      │
│                                                          │
│ Payment Transactions:                                    │
│ Payment Terms:       [Z030    ] * 🔍 (Net 30 Days)     │
│ Payment Methods:     [T       ] Bank Transfer           │
│ Tolerance Group:     [____    ]                         │
│                                                          │
│ Account Management - Accounting:                         │
│ Previous Account No: [________]                         │
│                                                          │
│ [Save]  [Back]  [Cancel]                                │
└──────────────────────────────────────────────────────────┘
```

**Critical Fields:**
✓ **Reconciliation account** - Required! GL account for AR (usually 140000)
✓ **Payment terms** - How soon customer pays (Net 30, Net 45, etc.)
✓ **Payment methods** - Bank transfer, check, credit card
✓ **Sort key** - How items sorted in account display

**Important Notes:**
⚠️ **Wrong reconciliation account** - Invoice posting fails!
💡 **Payment terms default** - Can override on individual invoices
💡 **Ask accounting** - If unsure about GL accounts

**Step 6: Payment Transactions Tab**
```
Click on "Payment Transactions" tab

┌──────────────────────────────────────────────────────────┐
│ Payment Transactions                                     │
├──────────────────────────────────────────────────────────┤
│ Payment Terms:        Z030 - Net 30 Days                │
│ Check Cashing Time:   [10] days                         │
│ Payment Methods:                                         │
│   [✓] T - Bank Transfer                                 │
│   [ ] C - Check                                         │
│   [ ] D - Direct Debit                                  │
│                                                          │
│ Payment History:                                         │
│ Previous Master Record: [________]                       │
│ Payment Block:          [    ] (leave blank)            │
│                                                          │
│ Automatic Payment Transactions:                          │
│ Paying Company Code:    [1000]                          │
│ Paying Bank Type:       [____]                          │
└──────────────────────────────────────────────────────────┘
```

**Step 7: Sales Area Data**
```
System navigates to Sales Area screen

┌──────────────────────────────────────────────────────────┐
│ Create Customer: Sales Data                              │
├──────────────────────────────────────────────────────────┤
│ [Sales]  [Shipping]  [Billing]  [Partner Functions]     │
├──────────────────────────────────────────────────────────┤
│ Sales Tab:                                               │
│                                                          │
│ Sales Data:                                              │
│ Customer Group:      [Z1      ] * 🔍 Corporate          │
│ Price List Type:     [10      ] Standard                │
│ Price Group:         [01      ] Corporate Pricing       │
│ Account Assign Grp:  [01      ]                         │
│                                                          │
│ Sales Office/Group:                                      │
│ Sales Office:        [SF01    ] 🔍 San Francisco        │
│ Sales Group:         [SERV    ] Service Team            │
│ Sales District:      [US-WEST ]                         │
│                                                          │
│ Pricing/Statistics:                                      │
│ Currency:            [USD     ]                         │
│ Customer Stat Grp:   [1       ]                         │
│ ABC Classification:  [A       ] (A=Best, C=Least)       │
└──────────────────────────────────────────────────────────┘
```

**Service-Specific Fields:**
✓ **Customer group** - Service customer type
✓ **Price list** - Which service price list applies
✓ **Sales office** - Local service office responsible
✓ **ABC classification** - A=VIP, B=Standard, C=Basic

**Step 8: Shipping Tab**
```
Click on "Shipping" tab

┌──────────────────────────────────────────────────────────┐
│ Shipping                                                 │
├──────────────────────────────────────────────────────────┤
│ Shipping Conditions:  [01] * Standard                   │
│ Delivery Priority:    [02] High                         │
│ Ship-to Party:        [same as customer]                │
│                                                          │
│ Service Location:                                        │
│ Unloading Point:      [DOCK-A] Main Entrance            │
│ Receiving Hours:      [0800-1700]                       │
│                                                          │
│ Goods Receiving:                                         │
│ Contact Person:       [John Davis]                      │
│ Contact Phone:        [+1 415-555-0250]                 │
└──────────────────────────────────────────────────────────┘
```

**Why This Matters for Service:**
- Field service technicians need access information
- Parts delivery for repairs
- Equipment pickup/drop-off locations

**Step 9: Billing Tab**
```
Click on "Billing" tab

┌──────────────────────────────────────────────────────────┐
│ Billing                                                  │
├──────────────────────────────────────────────────────────┤
│ Billing:                                                 │
│ Invoice List Type:    [____]                            │
│ Invoice Dates:        [01] First of Month               │
│ Payment Terms:        [Z030] Net 30 Days                │
│                                                          │
│ Tax Classification:                                      │
│ Tax Classification:   [1] * Taxable                     │
│ Tax Category:         [MWST]                            │
│                                                          │
│ Accounting:                                              │
│ Account Assignment:   [01]                              │
│ Sort Criterion:       [001]                             │
│                                                          │
│ Pricing:                                                 │
│ Pricing Procedure:    [ZSERV01] Service Pricing         │
│ Terms of Payment:     [Z030]                            │
│ Incoterms:           [DAP] Delivered at Place           │
└──────────────────────────────────────────────────────────┘
```

**Critical for Service Billing:**
✓ **Tax classification** - MUST be correct for invoicing
✓ **Pricing procedure** - Determines how services are priced
✓ **Payment terms** - When invoice is due
✓ **Incoterms** - Delivery terms (DAP, FOB, etc.)

**Step 10: Partner Functions**
```
Click on "Partner Functions" tab

┌──────────────────────────────────────────────────────────┐
│ Partner Functions                                        │
├──────────────────────────────────────────────────────────┤
│ Partner│ Description    │ Partner Number│ Name          │
├───────┼────────────────┼───────────────┼───────────────┤
│ AG    │ Sold-To Party  │ (auto-filled) │ SmartDevices  │
│ WE    │ Ship-To Party  │ (same)        │ SmartDevices  │
│ RE    │ Bill-To Party  │ (same)        │ SmartDevices  │
│ RG    │ Payer          │ (same)        │ SmartDevices  │
│ AP    │ Contact Person │ [________] 🔍│               │
└───────┴────────────────┴───────────────┴───────────────┘
```

**Partner Functions Explained:**
- **AG (Sold-To)**: Main customer (required)
- **WE (Ship-To)**: Where to deliver (can be different)
- **RE (Bill-To)**: Who to invoice (can be different)
- **RG (Payer)**: Who pays (usually same as Bill-To)
- **AP (Contact)**: Primary contact person

**Example - Different Partners:**
```
Company HQ in New York (Sold-To + Payer)
Branch Office in Boston (Ship-To)
Accounting Dept in Chicago (Bill-To)
```

**Step 11: Save**
```
Click [Save] button or press Ctrl+S

System validates and saves:
✓ Checking all required fields filled
✓ Validating GL accounts exist
✓ Checking number range
✓ Verifying tax codes

Success Message:
┌──────────────────────────────────────────────┐
│ ✓ Customer 1002345 has been created         │
└──────────────────────────────────────────────┘
```

**Post-Creation Steps:**
1. Note the customer number (1002345)
2. Verify by displaying (VD03)
3. Test by creating a quotation or service order
4. Inform relevant teams (sales, service, accounting)

## Customer Master Important Fields Reference

### Must-Know Fields

| Field | Technical Name | Importance | Notes |
|-------|---------------|------------|-------|
| Customer Number | KUNNR | ⭐⭐⭐⭐⭐ | Unique identifier |
| Name | NAME1 | ⭐⭐⭐⭐⭐ | Customer legal name |
| Search Term | SORTL | ⭐⭐⭐⭐ | Quick search code |
| Country | LAND1 | ⭐⭐⭐⭐⭐ | Affects tax |
| Reconciliation Account | AKONT | ⭐⭐⭐⭐⭐ | Must be correct! |
| Payment Terms | ZTERM | ⭐⭐⭐⭐ | When payment due |
| Credit Limit | KLIMK | ⭐⭐⭐⭐ | Blocks if exceeded |
| Pricing Procedure | KALKS | ⭐⭐⭐⭐ | Service pricing |
| Tax Classification | TAXKD | ⭐⭐⭐⭐⭐ | Required for invoicing |

## Common Customer-Related Issues

### Issue 1: Cannot Create Service Order for Customer

**Error Message:**
```
"Customer 1000567 is blocked for sales area 1000/10/01"
```

**Solution:**
1. Display customer (VD03)
2. Check sales area data
3. Look for blocking indicators
4. Contact sales/credit management to unblock

**Prevention:**
- Set up alerts for credit limit approaching
- Regular credit reviews
- Clear payment terms communication

### Issue 2: Wrong Pricing on Service Order

**Problem:** Service order shows wrong prices

**Investigation:**
```
1. Display customer (VD03)
2. Check Sales Area → Billing tab
3. Verify pricing procedure: Should be ZSERV01 (or your service pricing)
4. Check price list type and price group
```

**Solution:**
- Update pricing procedure in customer master (VD02)
- Re-create service order to apply new pricing

### Issue 3: Cannot Invoice Customer

**Error Message:**
```
"Tax code missing for customer 1000567"
```

**Solution:**
```
1. Go to VD02 (Change Customer)
2. Company Code data
3. Add tax classification
4. Save
5. Re-attempt billing
```

## Practice Exercises

### Exercise 1: Display Customer Deep Dive

**Task:** Display customer 1000567 and document:
- Full name and address
- Payment terms
- Credit limit and exposure
- Pricing procedure
- Any blocking indicators

**Deliverable:** Screenshot or notes of key information

### Exercise 2: Search for Customers

**Task:** Use search help (F4) to find:
- All customers in New York
- All customers with name starting with "Tech"
- All customers in postal code 10001

**Learn:** Different search strategies

### Exercise 3: Create Test Customer

**Task:** Create a test customer with these details:
```
Name: Test Services Inc.
City: Your local city
Payment Terms: Net 30
Customer Group: Corporate
Price List: Standard Service
```

**Tip:** Use "TEST" in the name so you can identify it later

### Exercise 4: Change Customer

**Task:** Change your test customer to:
- Add an email address
- Change payment terms to Net 45
- Add a second address line

**Learn:** How to modify existing customer (VD02)

### Exercise 5: Customer Relationships

**Task:** For customer 1000567:
- Find all equipment they own (IH08)
- Find all service orders (IW38)
- Find all open invoices (FBL5N)

**Learn:** How customer links to other data

## Customer Master Best Practices

### Data Quality

✅ **Complete Information**
- Always fill contact details
- Include phone and email
- Add relevant contacts

✅ **Consistent Formatting**
- Standard address format
- Consistent naming conventions
- Proper capitalization

✅ **Regular Maintenance**
- Review and update quarterly
- Remove obsolete customers
- Verify credit limits

### Security

✅ **Authorization Control**
- Not everyone can create customers
- Separate create/change/display rights
- Critical fields (credit limit) extra protection

✅ **Audit Trail**
- System logs all changes
- Track who changed what when
- Use change documents (VD04)

### Integration

✅ **Coordinate with Other Departments**
- Accounting: GL accounts, payment terms
- Credit Management: Credit limits
- Sales: Pricing, discounts
- Service: Service levels, SLAs

## Summary

In this module, you learned:

✅ Customer master three-level structure
✅ How to display customers (XD03/VD03)
✅ How to search for customers (F4)
✅ How to create customers (VD01)
✅ Critical fields for service operations
✅ Common customer-related issues
✅ Best practices for customer data

## Key Takeaways

🎯 **Customer master is foundation** - Most transactions start with customer
🎯 **Three levels exist** - General, Company Code, Sales Area
🎯 **Blocked customers** - Cannot transact until unblocked
🎯 **Credit limits matter** - System enforces automatically
🎯 **Data quality critical** - Bad data = problems downstream

## Next Module

Now let's learn about materials in SAP CS:
[Module 2.2: Material Master](05_material_master.md)

## Quick Reference Card

```
CUSTOMER MASTER CHEAT SHEET
───────────────────────────────────────
Display:    VD03 / XD03
Create:     VD01 / XD01
Change:     VD02 / XD02
Block:      XD05
Delete:     XD06

Search:     F4 on customer field
           Use * for wildcard

Critical Fields:
- Reconciliation Account (AKONT)
- Payment Terms (ZTERM)
- Tax Classification (TAXKD)
- Pricing Procedure (KALKS)

Check Blocks:
- VD03 → Look for 🔒 symbol
- Status field shows blocking

Credit Check:
- VD03 → Company Code Data
- See credit limit vs exposure
───────────────────────────────────────
```
