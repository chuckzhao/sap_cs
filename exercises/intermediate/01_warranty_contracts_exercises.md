# Intermediate Exercise Set 1: Warranty Claims Processing

## Overview

Master the complete warranty claim process from validation to settlement. This exercise set covers real-world warranty scenarios with different claim types, documentation requirements, and settlement processes.

## Exercise Duration
- **Time Required:** 6-8 hours
- **Difficulty:** Intermediate
- **Prerequisites:** Completed beginner exercises, understand service orders

## Exercise 1: Standard Warranty Claim - Laptop Screen Repair

### Objective
Process a complete manufacturer warranty claim including validation, documentation, repair, and settlement.

### Scenario Background

**Customer Information:**
- Company: TechStart Solutions Inc.
- Customer #: 1000789
- Contact: Mike Chen, IT Manager
- Phone: 555-0234
- Email: mchen@techstart.com

**Equipment Information:**
- Equipment: Laptop Model ProBook X500
- Serial Number: LT-2024-8934
- Manufacturer: ComputerCorp Inc.
- Purchase Date: 06/15/2023
- Warranty: 2-year manufacturer warranty
- Warranty End: 06/14/2025

**Problem Reported:**
"Laptop screen has developed dead pixels and flickering. Started 3 days ago. Getting worse. User cannot work effectively. Laptop is 18 months old, should be under warranty."

### Part A: Warranty Validation (30 minutes)

**Step 1: Verify Equipment and Warranty**

1. Display the equipment (`IE03`)
   - Equipment number: (search for laptop with serial LT-2024-8934)
   - Document the following:
   ```
   Equipment Number: ____________
   Description: ____________
   Serial Number: ____________
   Manufacturer: ____________
   Model Number: ____________

   Warranty Information:
   Start Date: ____________
   End Date: ____________
   Days Remaining: ____________
   Warranty Type: ____________
   Status: [ ] Valid  [ ] Expired
   ```

2. Check warranty coverage details:
   ```
   Parts Covered: [ ] YES  [ ] NO
   Labor Covered: [ ] YES  [ ] NO
   Travel Covered: [ ] YES  [ ] NO
   Max Labor Hours: ____________
   Pre-Approval Required: [ ] YES  [ ] NO
   Deductible: ____________
   ```

**Step 2: Create Warranty Notification**

1. Transaction: `/nIW51` (Create Warranty Notification)
   - Or `/nIW21` with notification type for warranty

2. Fill in notification:
   ```
   Notification Type: [Warranty claim type]
   Priority: [2 - High] (customer cannot work)
   Equipment: [from step 1]
   Serial Number: LT-2024-8934

   Description:
   "Laptop screen dead pixels and flickering - WARRANTY CLAIM"

   Customer Data:
   Customer: 1000789
   Contact: Mike Chen
   Phone: 555-0234
   Email: mchen@techstart.com

   Warranty Info:
   Claim Type: Manufacturer Warranty
   Warranty Valid Until: 06/14/2025
   Defect Date: [3 days ago]
   ```

3. Long Text - Document thoroughly:
   ```
   PROBLEM DESCRIPTION:
   Screen developed dead pixels (approximately 15-20 pixels)
   Screen flickers intermittently
   Started 3 days ago
   Getting progressively worse
   User: Sarah Williams, Marketing Manager

   IMPACT:
   User cannot work effectively
   Affects color accuracy for design work
   Urgent replacement needed

   WARRANTY STATUS:
   Purchase Date: 06/15/2023
   Warranty: 2-year manufacturer warranty
   Valid until: 06/14/2025
   Serial verified: LT-2024-8934

   VERIFICATION CHECKLIST:
   [✓] Serial number verified
   [✓] Warranty dates confirmed
   [✓] Coverage confirmed (parts + labor)
   [✓] No physical damage (warranty not void)
   [✓] No liquid damage

   REQUIRED DOCUMENTATION:
   [ ] Photos of defect (to be attached)
   [ ] Diagnostic report
   [ ] Serial number verification
   [ ] Manufacturer claim form

   NEXT STEPS:
   1. Take photos of screen defect
   2. Run manufacturer diagnostics
   3. Get manufacturer claim approval
   4. Order replacement screen
   5. Schedule repair
   ```

4. Add catalog codes:
   ```
   Damage Code: [E-SCREEN] Screen/Display
   Object Part: [LCD] LCD Display
   Defect Code: [PIXEL] Dead Pixels
   Cause Code: [MFG-DEFECT] Manufacturing Defect
   ```

5. Save notification and note number:
   ```
   Notification Number: ____________
   Status: ____________
   ```

**Step 3: Documentation and Photos**

Simulate gathering required documentation:

1. Create checklist:
   ```
   DOCUMENTATION CHECKLIST:
   ────────────────────────────────
   [ ] Photo 1: Overall laptop view with serial number visible
   [ ] Photo 2: Screen showing dead pixels (close-up)
   [ ] Photo 3: Screen showing flickering (if possible)
   [ ] Photo 4: No physical damage (edges, corners)
   [ ] Photo 5: No liquid damage indicators
   [ ] Diagnostic report from manufacturer's tool
   [ ] User statement/complaint
   [ ] Original purchase receipt (if required)
   [ ] Warranty registration proof
   ```

2. In real system, attach files to notification:
   - Transaction: `IW22` (Change Notification)
   - Use attachment function
   - Add all photos and documents

**Expected Results:**
✓ Warranty validated and confirmed active
✓ Notification created with warranty type
✓ Complete documentation gathered
✓ Serial number verified
✓ Ready for manufacturer claim submission

### Part B: Manufacturer Claim Process (45 minutes)

**Step 4: Get Manufacturer Pre-Approval**

1. Prepare claim information:
   ```
   CLAIM SUBMISSION TO MANUFACTURER:
   ════════════════════════════════════

   Claim Information:
   Manufacturer: ComputerCorp Inc.
   Product: ProBook X500
   Serial: LT-2024-8934
   Warranty ID: [from equipment master]

   Defect Details:
   Issue: Screen dead pixels and flickering
   Date Reported: [today]
   Date Started: [3 days ago]
   Severity: High (user cannot work)

   Diagnosis:
   Component: LCD Display Assembly
   Part Number: SCREEN-X500-15.6
   Cause: Manufacturing defect
   Recommendation: Replace LCD assembly

   Cost Estimate:
   Part Cost: $285.00
   Labor: 2.0 hours @ $85/hr = $170.00
   Total Claim: $455.00
   ```

2. In your system, create warranty claim document:
   - Note manufacturer claim number
   - Document approval status
   - Note any restrictions

3. Document approval:
   ```
   MANUFACTURER APPROVAL:
   ──────────────────────
   Claim Number: [WC-2024-123456]
   Approval Date: ____________
   Approved By: ____________
   Approved Amount: $__________

   Restrictions:
   - Must use OEM parts only
   - Labor hours: Maximum 2.5 hours
   - Parts: Pre-approved screen assembly only
   - Completion: Within 5 business days

   Settlement:
   Payment Terms: Net 30 after claim submission
   Claim Submittal: Via manufacturer portal
   Required Docs: Photos, diagnostic, repair confirmation
   ```

**Step 5: Create Warranty Service Order**

1. Transaction: `/nIW31`
   - Order Type: **SM02** (Warranty Service Order)
   - Reference Notification: [from Part A]

2. Fill in order header:
   ```
   Order Type: SM02 - Warranty Service
   Priority: 2 - High
   Equipment: [laptop equipment number]
   Description: "Screen replacement - WARRANTY CLAIM WC-2024-123456"

   Reference:
   Notification: [from Part A]
   Warranty Claim: WC-2024-123456
   ```

3. Customer Data:
   ```
   Sold-To: 1000789 - TechStart Solutions
   Contact: Mike Chen

   Important Note:
   This is WARRANTY work - DO NOT bill customer
   Settlement will be to manufacturer claim
   ```

4. Add Operations:
   ```
   OPERATIONS FOR WARRANTY ORDER:
   ══════════════════════════════

   Op 0010: Verify warranty and serial number
   ├── Description: Warranty validation and documentation
   ├── Work Center: SERVICE-TECH
   ├── Duration: 0.5 hours
   ├── Activity Type: 1410 - Service (Warranty Rate)
   └── Notes: Serial verification mandatory

   Op 0020: Backup user data
   ├── Description: Backup user files before repair
   ├── Work Center: SERVICE-TECH
   ├── Duration: 0.5 hours
   ├── Activity Type: 1410 - Service
   └── Notes: Customer data protection

   Op 0030: Disassemble laptop
   ├── Description: Remove screen assembly
   ├── Work Center: SERVICE-TECH
   ├── Duration: 1.0 hours
   ├── Activity Type: 1410 - Service
   └── Notes: Follow manufacturer procedures

   Op 0040: Install replacement screen
   ├── Description: Install new LCD assembly
   ├── Work Center: SERVICE-TECH
   ├── Duration: 1.5 hours
   ├── Activity Type: 1410 - Service
   └── Notes: OEM part only - verify part number

   Op 0050: Test and validate
   ├── Description: Full functionality testing
   ├── Work Center: SERVICE-TECH
   ├── Duration: 0.5 hours
   ├── Activity Type: 1410 - Service
   └── Notes: Dead pixel test, color test, etc.

   Op 0060: Document completion
   ├── Description: Photos and warranty documentation
   ├── Work Center: SERVICE-TECH
   ├── Duration: 0.5 hours
   ├── Activity Type: 1410 - Service
   └── Notes: Required for claim submission

   Op 0070: Customer acceptance
   ├── Description: User testing and sign-off
   ├── Work Center: SERVICE-TECH
   ├── Duration: 0.5 hours
   ├── Activity Type: 1410 - Service
   └── Notes: Get customer signature

   ────────────────────────────────
   Total Duration: 5.0 hours
   Warranty Limit: 2.5 hours covered
   ────────────────────────────────

   IMPORTANT NOTES:
   ⚠️ Only operations 30, 40, 50 billable to warranty (4.0 hrs)
   ⚠️ Operations 10, 20, 60, 70 absorbed by service center
   ⚠️ Total claimed: 2.5 hrs (within warranty limit)
   ```

5. Add Components (Materials):
   ```
   MATERIALS FOR WARRANTY ORDER:
   ═════════════════════════════

   Item 0010: LCD Screen Assembly
   ├── Material: SCREEN-X500-15.6
   ├── Description: 15.6" LCD Assembly ProBook X500
   ├── Quantity: 1 EA
   ├── Part Source: MANUFACTURER OEM
   ├── Part Number: Must match: [specific OEM number]
   ├── Cost: $285.00
   └── ⚠️ CRITICAL: Must be OEM part or warranty void!

   Item 0020: Screen Adhesive
   ├── Material: ADHESIVE-LCD-01
   ├── Description: LCD Adhesive Strips
   ├── Quantity: 1 SET
   ├── Part Source: OEM approved
   ├── Cost: $5.00
   └── Note: Must use approved adhesive

   Item 0030: Cleaning Kit
   ├── Material: CLEAN-LCD
   ├── Description: LCD Cleaning Kit
   ├── Quantity: 1 EA
   ├── Part Source: Generic OK
   ├── Cost: $3.00
   └── Note: Not claimed on warranty

   ────────────────────────────────
   Total Material Cost: $293.00
   Warranty Claim: $290.00 (items 10, 20 only)
   Absorbed: $3.00 (cleaning kit)
   ────────────────────────────────
   ```

6. Settlement Rules:
   ```
   SETTLEMENT CONFIGURATION:
   ═════════════════════════

   Warranty Costs (to Manufacturer):
   ├── Labor: 2.5 hrs @ $85/hr = $212.50
   ├── Parts: Screen + Adhesive = $290.00
   └── Total Warranty Claim: $502.50

   Settlement Rule 1:
   ├── Receiver: Vendor 50000 (ComputerCorp)
   ├── Settlement Type: Warranty Claim
   ├── Claim Number: WC-2024-123456
   └── Amount: $502.50

   Company Absorbed Costs:
   ├── Setup/Documentation: 2.5 hrs @ $100/hr = $250.00
   ├── Cleaning supplies: $3.00
   └── Total Absorbed: $253.00

   Settlement Rule 2:
   ├── Receiver: Cost Center 1000-SERVICE
   ├── Settlement Type: Internal Cost
   └── Amount: $253.00

   Customer Charge: $0.00 (WARRANTY)
   ════════════════════════════════
   ```

7. Dates and Scheduling:
   ```
   SCHEDULING:
   ───────────
   Customer Required: ASAP (user cannot work)

   Planned Schedule:
   ├── Parts Order: Today
   ├── Parts Arrival: Tomorrow (overnight)
   ├── Repair Scheduled: Day after tomorrow, 09:00
   ├── Estimated Duration: 5 hours
   ├── Expected Completion: Same day, 15:00
   └── Customer Pickup: Same day, 16:00

   SLA Compliance:
   ├── Warranty Response: 24 hours ✓
   ├── Repair Completion: 72 hours ✓
   └── Status: Within SLA
   ```

8. Save and Release Order:
   ```
   Save Order: ____________
   Order Number: ____________
   Release Order: YES
   Status after release: REL (Released)
   Materials Reserved: YES
   Technician Notified: YES
   ```

**Expected Results:**
✓ Manufacturer claim approved
✓ Warranty order created with correct type (SM02)
✓ Operations detailed with warranty rates
✓ OEM parts specified
✓ Settlement rules configured (manufacturer + cost center)
✓ No customer billing
✓ Order released and scheduled

### Part C: Repair Execution and Confirmation (45 minutes)

**Step 6: Parts Management**

1. Check material availability:
   ```
   Transaction: MMBE (Stock Overview)
   Material: SCREEN-X500-15.6

   Stock Status:
   ├── Plant 1000, Storage Location 0001
   ├── Available: 0 EA ⚠️ NOT IN STOCK
   └── Action: Create purchase requisition
   ```

2. Create Purchase Requisition (if needed):
   ```
   Transaction: ME51N

   Purchase Requisition:
   ├── Material: SCREEN-X500-15.6
   ├── Quantity: 1 EA
   ├── Delivery Date: Tomorrow
   ├── Vendor: 50000 (ComputerCorp - manufacturer)
   ├── Price: $285.00
   ├── Delivery: Overnight shipping
   └── Reference: Service Order [your order number]

   Account Assignment:
   ├── Service Order: [your order number]
   ├── Settlement: To warranty claim
   └── Cost Element: Material costs

   Purchase Req Number: ____________
   ```

3. Simulate parts receipt:
   ```
   Transaction: MIGO (Goods Receipt)

   Purchase Order: [created from requisition]
   Movement Type: 101 (GR for purchase order)
   Quantity: 1 EA
   Storage Location: 0001

   GR Document: ____________
   Material Doc: ____________

   Stock Now Available: 1 EA ✓
   ```

4. Material withdrawal to order:
   ```
   Transaction: MB1A (Goods Issue)

   Movement Type: 261 (Goods issue for order)
   Service Order: [your order number]
   Material: SCREEN-X500-15.6
   Quantity: 1 EA
   Storage Location: 0001

   Material Document: ____________

   Result:
   ├── Stock reduced: 1 EA
   ├── Cost posted to order: $285.00
   └── Reservation fulfilled: YES ✓
   ```

**Step 7: Work Confirmation**

1. Transaction: `/nIW41` (Enter Confirmation)

2. Confirm each operation:

   **Operation 0010 Confirmation:**
   ```
   Operation: 0010 - Verify warranty
   Actual Work:
   ├── Start Date/Time: [day of repair] 09:00
   ├── End Date/Time: [day of repair] 09:30
   ├── Actual Hours: 0.5
   ├── Employee: TECH-05 (John Smith)
   ├── Final Confirmation: NO (more ops to follow)
   └── Work Performed:
       "Serial number verified: LT-2024-8934
        Warranty confirmed valid
        Photos taken of serial label
        Equipment condition documented"
   ```

   **Operation 0020 Confirmation:**
   ```
   Operation: 0020 - Backup data
   Actual Work:
   ├── Start: 09:30
   ├── End: 10:00
   ├── Actual Hours: 0.5
   ├── Work Performed:
       "User data backed up to external drive
        Documents: 15 GB
        Settings exported
        Backup verified"
   ```

   **Operation 0030 Confirmation:**
   ```
   Operation: 0030 - Disassemble
   Actual Work:
   ├── Start: 10:00
   ├── End: 11:00
   ├── Actual Hours: 1.0
   ├── Work Performed:
       "Laptop disassembled per manufacturer procedure
        Screws organized and labeled
        Old screen removed carefully
        Connectors inspected - all OK
        Photos taken of old screen defect"
   ```

   **Operation 0040 Confirmation:**
   ```
   Operation: 0040 - Install replacement
   Actual Work:
   ├── Start: 11:00
   ├── End: 12:30
   ├── Actual Hours: 1.5
   ├── Work Performed:
       "New OEM screen installed
        Part number verified: [OEM number]
        Connectors seated properly
        Adhesive applied per spec
        Assembly completed
        No issues during installation"
   ```

   **Operation 0050 Confirmation:**
   ```
   Operation: 0050 - Test and validate
   Actual Work:
   ├── Start: 12:30
   ├── End: 13:00
   ├── Actual Hours: 0.5
   ├── Work Performed:
       "Full display testing completed:
        - Dead pixel test: PASSED ✓
        - Color accuracy: PASSED ✓
        - Brightness: PASSED ✓
        - No flickering: PASSED ✓
        - Touch response: PASSED ✓
        - All functions normal"
   ```

   **Operation 0060 Confirmation:**
   ```
   Operation: 0060 - Documentation
   Actual Work:
   ├── Start: 13:00
   ├── End: 13:30
   ├── Actual Hours: 0.5
   ├── Work Performed:
       "Documentation completed:
        - Before photos attached
        - After photos attached
        - Test results documented
        - Warranty claim form completed
        - Serial numbers verified
        - Ready for claim submission"
   ```

   **Operation 0070 Confirmation:**
   ```
   Operation: 0070 - Customer acceptance
   Actual Work:
   ├── Start: 13:30
   ├── End: 14:00
   ├── Actual Hours: 0.5
   ├── Final Confirmation: YES ✓
   ├── Work Performed:
       "User tested laptop:
        - Sarah Williams tested all functions
        - Screen quality excellent
        - No dead pixels visible
        - User satisfied
        - Customer sign-off received
        - Laptop returned to user"
   ```

3. Final Confirmation Summary:
   ```
   CONFIRMATION COMPLETE:
   ══════════════════════

   Total Actual Hours: 5.0 hours
   Planned Hours: 5.0 hours
   Variance: 0.0 hours ✓

   Materials Used:
   ✓ Screen assembly: 1 EA (as planned)
   ✓ Adhesive: 1 SET (as planned)
   ✓ Cleaning kit: 1 EA (as planned)

   Quality:
   ✓ All tests passed
   ✓ Customer satisfied
   ✓ Documentation complete

   Status Changed:
   REL (Released) → CNF (Confirmed) → TECO (Technically Complete)
   ```

**Expected Results:**
✓ All operations confirmed with actual times
✓ Materials withdrawn and posted
✓ Detailed work notes documented
✓ Order status: Technically Complete (TECO)
✓ Ready for settlement

### Part D: Settlement and Claim Submission (30 minutes)

**Step 8: Order Settlement**

1. Transaction: `/nIW47` (Settlement)

2. Select your order and execute settlement:
   ```
   SETTLEMENT EXECUTION:
   ═══════════════════════

   Order: [your order number]
   Settlement Type: Full settlement

   Costs Being Settled:
   ────────────────────
   Labor Costs:
   ├── Op 10: 0.5 hrs @ $100/hr = $50.00 (internal)
   ├── Op 20: 0.5 hrs @ $100/hr = $50.00 (internal)
   ├── Op 30: 1.0 hrs @ $85/hr = $85.00 (warranty)
   ├── Op 40: 1.5 hrs @ $85/hr = $127.50 (warranty)
   ├── Op 50: 0.5 hrs @ $85/hr = $42.50 (warranty)
   ├── Op 60: 0.5 hrs @ $100/hr = $50.00 (internal)
   └── Op 70: 0.5 hrs @ $100/hr = $50.00 (internal)

   Material Costs:
   ├── Screen: $285.00 (warranty)
   ├── Adhesive: $5.00 (warranty)
   └── Cleaning: $3.00 (internal)

   Overhead:
   └── 15% on labor = $75.75

   Total Order Cost: $823.75
   ────────────────────────

   Settlement Distribution:
   ════════════════════════

   To Manufacturer Claim (WC-2024-123456):
   ├── Labor (warranty ops): $255.00
   ├── Materials (warranty parts): $290.00
   ├── Overhead allocated: $40.00
   └── Subtotal to Claim: $585.00

   To Cost Center (1000-SERVICE):
   ├── Labor (internal ops): $200.00
   ├── Materials (cleaning): $3.00
   ├── Overhead balance: $35.75
   └── Subtotal Internal: $238.75

   Settlement Documents:
   ├── FI Document: ____________
   ├── CO Document: ____________
   └── Settlement Date: ____________

   Order Status: STL (Settled) ✓
   ```

**Step 9: Manufacturer Claim Submission**

1. Prepare claim package:
   ```
   WARRANTY CLAIM PACKAGE:
   ═══════════════════════

   Claim Information:
   ├── Claim Number: WC-2024-123456
   ├── Serial Number: LT-2024-8934
   ├── Repair Date: ____________
   └── Service Order: ____________

   Cost Breakdown:
   ├── Labor: $255.00
   ├── Parts: $290.00
   └── Total Claim: $545.00

   Required Documents (attach):
   ├── [✓] Before photos (defect visible)
   ├── [✓] After photos (repair complete)
   ├── [✓] Diagnostic report
   ├── [✓] Serial number verification
   ├── [✓] Work order with times
   ├── [✓] Parts receipt (OEM verification)
   ├── [✓] Customer sign-off
   └── [✓] Technician certification

   Submission Method:
   └── Manufacturer warranty portal
       https://warranty.computercorp.com

   Expected Payment:
   ├── Net 30 days after submission
   ├── Payment to: [your company bank account]
   └── Reference: WC-2024-123456
   ```

2. Update notification:
   ```
   Transaction: IW22 (Change Notification)
   Notification: [from Part A]

   Add to Long Text:
   ──────────────────
   REPAIR COMPLETED - [date]

   Service Order: [number]
   Repair Date: [date]
   Technician: John Smith (TECH-05)

   Work Performed:
   - LCD screen assembly replaced
   - OEM part used: [part number]
   - All testing passed
   - Customer satisfied

   Warranty Claim:
   - Claim Number: WC-2024-123456
   - Claim Amount: $545.00
   - Submitted: [date]
   - Expected Payment: [date + 30 days]

   Status: COMPLETE ✓

   Change Status to: NOCO (Notification Completed)
   ```

**Step 10: Final Documentation and Closure**

1. Complete checklist:
   ```
   COMPLETION CHECKLIST:
   ═══════════════════════

   Technical:
   [✓] Notification created and documented
   [✓] Warranty validated
   [✓] Manufacturer approval obtained
   [✓] Service order created (SM02)
   [✓] OEM parts used
   [✓] All operations confirmed
   [✓] Testing passed
   [✓] Customer satisfied
   [✓] Order technically complete
   [✓] Order settled correctly

   Financial:
   [✓] Costs posted correctly
   [✓] Settlement split (manufacturer + cost center)
   [✓] No customer billing
   [✓] Claim submitted to manufacturer

   Documentation:
   [✓] Photos attached
   [✓] Warranty docs complete
   [✓] Customer sign-off
   [✓] Claim package submitted

   System:
   [✓] Notification status: NOCO
   [✓] Order status: TECO/STL
   [✓] All documents linked
   ```

2. Create summary report:
   ```
   WARRANTY CLAIM SUMMARY REPORT:
   ══════════════════════════════

   Customer: TechStart Solutions Inc.
   Equipment: Laptop ProBook X500 (LT-2024-8934)
   Issue: Screen defect (dead pixels, flickering)

   Timeline:
   ├── Reported: [date]
   ├── Validated: Same day
   ├── Approved: Next day
   ├── Parts Ordered: [date]
   ├── Repair Completed: [date]
   └── Total Time: 3 days ✓ (within SLA)

   Financial Summary:
   ├── Total Cost: $823.75
   ├── Claimed from Mfr: $545.00
   ├── Company Absorbed: $238.75
   └── Customer Charged: $0.00

   Documents:
   ├── Notification: [number]
   ├── Service Order: [number]
   ├── Warranty Claim: WC-2024-123456
   └── Settlement Docs: [numbers]

   Result:
   ✓ Repair successful
   ✓ Customer satisfied
   ✓ Warranty claim submitted
   ✓ Expected recovery: $545.00
   ✓ Net cost: $238.75
   ```

### Expected Final Results

✓ **Complete warranty process executed**
✓ **All documentation requirements met**
✓ **Costs properly allocated**
✓ **Customer not charged (warranty)**
✓ **Manufacturer claim submitted**
✓ **All system documents properly created and linked**

### Self-Assessment

Rate your understanding (1-5):

```
Warranty Validation: _____
Order Type Selection: _____
OEM Parts Requirements: _____
Settlement Configuration: _____
Claim Documentation: _____
Cost Allocation: _____

Overall Confidence: _____/5

Ready for next exercise: [ ] YES  [ ] NEED MORE PRACTICE
```

### Common Mistakes to Avoid

❌ **Using wrong order type (SM01 instead of SM02)**
   → Customer gets billed when they shouldn't

❌ **Not verifying warranty before starting work**
   → Warranty expired, company absorbs all costs

❌ **Using aftermarket parts instead of OEM**
   → Warranty claim denied, total loss

❌ **Incomplete documentation**
   → Manufacturer disputes claim, partial payment

❌ **Wrong settlement receiver**
   → Costs go to wrong account, reporting errors

### Practice Variations

Try these variations to build skills:

**Variation 1: Partial Warranty Coverage**
- Labor covered, parts not covered
- How to bill customer for parts only?

**Variation 2: Warranty Expired**
- Warranty expired by 15 days
- Customer wants goodwill coverage
- How to handle?

**Variation 3: Warranty Denied**
- Physical damage found (warranty void)
- How to convert to standard order?
- How to bill customer?

**Variation 4: Extended Warranty**
- Third-party extended warranty
- Different claim process
- How to manage two warranty providers?

---

## Exercise 2: Service Contract Management

### Objective
Master service contract order creation, tracking consumption, and managing contract limits.

### Scenario Background

**Customer:** GlobalManufacturing Inc. (Customer #1000456)
**Contract:** Annual HVAC Maintenance Contract
**Contract Number:** 40000567
**Contract Period:** 01/01/2024 - 12/31/2024
**Contract Value:** $24,000.00
**Service Frequency:** Quarterly inspections (4 per year)
**Equipment Coverage:** 8 HVAC units in manufacturing facility

### Part A: Review Contract (30 minutes)

1. Display the service contract:
   ```
   Transaction: VA43 (Display Service Contract)
   Contract: 40000567

   Document:
   ├── Contract Header
   ├── Contract Items
   ├── Equipment List
   ├── Service Schedule
   └── Consumption History
   ```

2. Analyze contract:
   ```
   CONTRACT ANALYSIS:
   ══════════════════

   Header Information:
   ├── Customer: 1000456 - GlobalManufacturing
   ├── Valid From: 01/01/2024
   ├── Valid To: 12/31/2024
   ├── Total Value: $24,000.00
   ├── Currency: USD
   └── Status: Active

   Items/Services Covered:
   Item 10: Quarterly HVAC Inspections
   ├── Quantity: 4 visits
   ├── Price per visit: $5,000.00
   ├── Total: $20,000.00
   └── Schedule: Mar, Jun, Sep, Dec

   Item 20: Emergency Call-Out (if needed)
   ├── Included calls: 2
   ├── Price per call: $2,000.00
   ├── Total: $4,000.00
   └── After hours coverage

   Equipment Covered:
   ├── HVAC-001: North Wing Unit 1
   ├── HVAC-002: North Wing Unit 2
   ├── HVAC-003: South Wing Unit 1
   ├── HVAC-004: South Wing Unit 2
   ├── HVAC-005: East Wing Unit 1
   ├── HVAC-006: East Wing Unit 2
   ├── HVAC-007: West Wing Unit 1
   └── HVAC-008: West Wing Unit 2

   Consumption to Date:
   ├── Q1 Visit (March): $5,000.00 ✓ Completed
   ├── Q2 Visit (June): $5,000.00 ✓ Completed
   ├── Q3 Visit (September): Pending
   ├── Q4 Visit (December): Not due
   ├── Emergency Calls: 1 used, 1 remaining
   └── Remaining Value: $12,000.00
   ```

### Part B: Create Contract Order for Q3 Visit (60 minutes)

**Task:** Create service order for September quarterly inspection

1. Create order:
   ```
   Transaction: IW31
   Order Type: SM03 (Service Contract)
   Contract Reference: 40000567
   Contract Item: 10 (Quarterly Inspection)
   ```

2. Complete order with pre-defined operations from contract

3. Schedule all 8 units for inspection

4. Assign technicians

5. Track that this consumes $5,000 from contract

### Part C: Handle Additional Work Found (45 minutes)

**Scenario:** During Q3 inspection, technician finds Unit 3 needs compressor replacement ($3,500) - NOT covered by standard contract.

**Task:**
1. How to document the finding?
2. Should you add to contract order or create separate order?
3. How to get customer approval?
4. How to bill the additional work?

### Expected Results
- Contract order created and linked
- Additional work properly separated
- Customer approval documented
- Billing split correctly

---

## Exercise 3: Complex Multi-Equipment Service Order

### Objective
Handle service order involving multiple pieces of equipment, coordination between technicians, and multi-day execution.

### Scenario
Office relocation requiring disconnect, move, and reconnect of 15 pieces of equipment over 3 days.

**Equipment List:**
- 5 Desktop computers
- 3 Printers
- 2 Servers
- 3 Network switches
- 2 Phone systems

**Requirements:**
- Minimize downtime
- Coordinate with IT team
- Document all serial numbers
- Test all equipment after move
- Complete in 3-day window

### Tasks
1. Plan the operation sequence
2. Create operations for each day
3. Assign multiple technicians
4. Track materials (cables, labels, packing)
5. Confirm work daily
6. Handle issues (damaged equipment found)

---

## Exercise 4: Complaint Processing and Root Cause Analysis

### Objective
Handle customer complaint, perform root cause analysis, implement corrective action, and prevent recurrence.

### Scenario
Customer complains about repeated failures of same equipment (4 times in 2 months).

**Tasks:**
1. Create complaint notification
2. Research history (IW28, IW38)
3. Analyze previous repairs
4. Identify root cause
5. Propose permanent solution
6. Create order for corrective action
7. Document lessons learned

---

## Time Tracking

```
INTERMEDIATE EXERCISE SET 1 TIME LOG:
═════════════════════════════════════

Exercise 1: Warranty Claims
├── Part A (Validation): _______ min (target: 30)
├── Part B (Claim Process): _______ min (target: 45)
├── Part C (Execution): _______ min (target: 45)
├── Part D (Settlement): _______ min (target: 30)
└── Total: _______ min (target: 150 min)

Exercise 2: Service Contracts
└── Total: _______ min

Exercise 3: Multi-Equipment
└── Total: _______ min

Exercise 4: Complaints
└── Total: _______ min

TOTAL SET TIME: _______ hours
```

---

**Continue to Intermediate Exercise Set 2 for more scenarios...**
