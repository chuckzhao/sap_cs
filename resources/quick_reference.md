# SAP CS Quick Reference Guide

## Essential Transaction Codes

### Service Notifications
```
IW21    Create Service Notification
IW22    Change Service Notification
IW23    Display Service Notification
IW24    List Editing of Notifications
IW25    Create QM Notification
IW26    Change QM Notification
IW27    Display QM Notification
IW28    Service Notification List
IW29    Display Notification Structure
```

### Service Orders
```
IW31    Create Service Order
IW32    Change Service Order
IW33    Display Service Order
IW34    Create Service Req./Order
IW35    Change Service Req./Order
IW36    Display Service Req./Order
IW37    Create Service Req./Order (List)
IW38    Service Order List
IW39    Display Order Structure
```

### Order Confirmation
```
IW41    Enter Confirmation
IW42    Change Confirmation
IW43    Display Confirmation
IW44    Confirmation List
IW45    Overall Confirmation
IW47    Settlement
IW48    Collective Confirmation
IW49    Cancel Confirmation
```

### Service Contracts
```
VA41    Create Service Contract
VA42    Change Service Contract
VA43    Display Service Contract
VA45    Service Contract List
VA46    Release Service Contract
```

### Master Data - Equipment
```
IE01    Create Equipment
IE02    Change Equipment
IE03    Display Equipment
IE04    Equipment Changes
IE05    Equipment List
IE06    Display Equipment List
IE07    Edit Equipment List
IH08    Equipment Where-Used List
```

### Master Data - Functional Location
```
IL01    Create Functional Location
IL02    Change Functional Location
IL03    Display Functional Location
IL04    Functional Location Changes
IL05    Functional Location List
IL06    Display Functional Location List
```

### Master Data - Customer
```
XD01    Create Customer
XD02    Change Customer
XD03    Display Customer
XD04    Customer Changes
XD05    Block/Unblock Customer
XD06    Flagging for Deletion
VD51    Create Customer (Sales)
```

### Master Data - Material
```
MM01    Create Material
MM02    Change Material
MM03    Display Material
MM04    Display Material Changes
MM06    Flagging Material for Deletion
MM50    Extended Material List
```

### Reporting & Analysis
```
IW38    Service Order List
IW28    Notification List
COOIS   Order Information System
IH08    Equipment List
CN43N   Cost Reports
S_ALR_87012993  Service Order Report
```

### Configuration (IMG)
```
SPRO    SAP Reference IMG
OMS1    Service Order Types
OICO    Notification Types
V/08    Pricing Procedures
OIAA    Status Profile
OPL8    Partner Determination
```

### System Utilities
```
/n      End current transaction
/nex    Exit SAP (log off)
/o      Create new session
/i      Delete current session
/nend   Return to SAP Easy Access
SESSION_MANAGER  Session Manager
```

## Function Keys

### Standard Functions
```
F1      Field Help
F2      Pick (select from list)
F3      Back
F4      Search Help / Value Help
F5      Refresh
F6      Insert Row
F8      Execute / Continue
F9      Select All
F11     Save
F12     Cancel
```

### Extended Functions
```
Ctrl+S      Save
Ctrl+F      Find
Ctrl+G      Go to Line
Ctrl+Y      Favorites
Ctrl+/      Insert Comment
Ctrl+C      Copy
Ctrl+V      Paste
Ctrl+X      Cut
Ctrl+Z      Undo
Shift+F1    Application Help
Shift+F4    Multiple Selection
```

## Status Codes

### System Status
```
CRE     Created
REL     Released
PREL    Partially Released
GMPS    Goods Movements Posted
CNF     Confirmed
PCNF    Partially Confirmed
MANU    Manual Completion
DLV     Delivered
TECO    Technically Complete
CLSD    Closed
DLFL    Deletion Flag
```

### User Status (Examples)
```
PLAN    Planning
APPR    Approved
ASGN    Assigned
PROG    In Progress
HOLD    On Hold
WAIT    Waiting for Parts
TEST    Testing
COMP    Complete
```

## Priority Codes
```
1       Very High / Immediate
2       High / Urgent
3       Medium / Normal
4       Low / Can Wait
5       Very Low / When Possible
```

## Common Field Names

### Header Fields
```
AUFNR   Order Number
QMNUM   Notification Number
MATNR   Material Number
EQUNR   Equipment Number
KUNNR   Customer Number
WERKS   Plant
KOSTL   Cost Center
BUKRS   Company Code
```

### Date Fields
```
ERDAT   Created On
GSTRP   Basic Start Date
GLTRP   Basic Finish Date
IDAT1   Actual Start
IDAT2   Actual Finish
AEDAT   Changed On
```

### Text Fields
```
KTEXT   Short Text / Description
LTXA1   Long Text
STTXT   Status Text
```

## Navigation Tips

### Quick Navigation
```
/nXXXX      Go to transaction XXXX
/oXXXX      Open transaction XXXX in new session
/nend       Back to main menu
/nex        Log off
/i          Delete session
```

### Search Wildcards
```
*           Multiple characters (e.g., Smith*)
+           Single character (e.g., 10+5)
#           Exclude (e.g., #Smith)
```

### Efficient Searching
```
= Value     Exact match
<> Value    Not equal
< Value     Less than
> Value     Greater than
... Value   Ends with
Value ...   Starts with
*Value*     Contains
```

## Important Tables

### Master Data Tables
```
EQUI        Equipment Master
ILOA        Functional Locations
KNA1        Customer Master (General)
KNB1        Customer Master (Company Code)
MARA        Material Master (General)
MARC        Material Master (Plant)
```

### Transaction Tables
```
AUFK        Order Master Data
QMEL        Notification Master
VBAK        Sales Document Header
VBAP        Sales Document Item
AFKO        Order Header
AFPO        Order Item
```

### Configuration Tables
```
T003O       Order Types
TQ80        Notification Types
T005        Countries
T006        Units of Measurement
T001        Company Codes
T001K       Valuation Area
```

## Common User Exits & BAdIs
```
IWO10001    Order Creation
IWO10009    Order Save
QMEL0001    Notification Creation
QMEL0004    Status Change
```

## Useful Reports
```
CN43N       Display Cost Reports
CS15        Material BOM
IH06        Equipment List
IW37N       Multi-level Order Display
MB52        Warehouse Stocks
VA05        Sales Order List
VF05        Billing List
```

## Authorization Objects
```
I_AUART     Order Type
I_QMART     Notification Type
I_EQUI      Equipment
I_IWERK     Plant
I_KOSTL     Cost Center
```

## Message Types
```
S   Success (Green)
W   Warning (Yellow)
E   Error (Red)
I   Information (Blue)
A   Abnormal End
X   Exit
```

## Service Order Lifecycle
```
1. Create (IW31)
   ↓
2. Release
   ↓
3. Confirm (IW41)
   ↓
4. Technical Complete (TECO)
   ↓
5. Settlement (IW47)
   ↓
6. Close (CLSD)
```

## Integration Points

### With SD (Sales & Distribution)
```
- Customer Master
- Sales Orders → Service Orders
- Returns Processing
- Billing Documents
- Pricing
```

### With MM (Materials Management)
```
- Material Master
- Goods Issue/Receipt
- Stock Management
- Purchase Requisitions
- Vendor Management
```

### With FI/CO (Finance/Controlling)
```
- Cost Centers
- Cost Allocation
- Settlement Rules
- Revenue Recognition
- Profitability Analysis
```

### With PM (Plant Maintenance)
```
- Equipment Master
- Functional Locations
- Maintenance Plans
- Technical Objects
- Work Centers
```

## Best Practices

### Creating Orders
✅ Always link to equipment for history
✅ Use descriptive operation text
✅ Add expected materials upfront
✅ Set realistic dates
✅ Assign work center
✅ Reference notifications when applicable

### Data Quality
✅ Maintain master data accuracy
✅ Use standard codes/catalogs
✅ Document thoroughly
✅ Clean up old data regularly
✅ Follow naming conventions

### Performance
✅ Use variants for frequent reports
✅ Limit selection criteria
✅ Archive old orders
✅ Use appropriate indexes
✅ Avoid custom code when possible

## Common Errors & Solutions

### Error: Material not found
**Solution**: Check material number, verify plant

### Error: No authorization
**Solution**: Request authorization from administrator

### Error: Number range exhausted
**Solution**: Contact basis team to extend range

### Error: Equipment locked
**Solution**: Check who has equipment open (SM12)

### Error: Settlement failed
**Solution**: Check settlement rule, cost center validity

## Keyboard Shortcuts Summary
```
Enter       Next screen
F3          Back
F4          Search help
F8          Execute
F11         Save
F12         Cancel
Ctrl+S      Save
Ctrl+F      Find
Ctrl+Y      Favorites
```

## Emergency Procedures

### System Down
1. Check SAP service status
2. Contact basis team
3. Document error messages
4. Have fallback process

### Data Loss
1. Don't panic
2. Check recent changes (SE16)
3. Restore from backup if needed
4. Document incident

### Go-Live Issues
1. Prioritize critical processes
2. Have key users on standby
3. Document all issues
4. Escalate appropriately

## Resources

### Official SAP
- help.sap.com
- support.sap.com
- learning.sap.com
- community.sap.com

### Community
- Reddit: r/SAP
- LinkedIn SAP Groups
- Twitter: #SAP
- YouTube SAP Channels

### Training
- SAP Learning Hub
- openSAP (free)
- Udemy SAP courses
- LinkedIn Learning

---

**Pro Tip**: Print this quick reference and keep it handy while working in SAP! 📋
