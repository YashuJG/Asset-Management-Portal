# User Guide - Asset Management Portal

## Welcome to the Asset Management Portal! 👋

This guide will help you understand how to use the Asset Management Portal to track and manage organizational assets.

---

## Table of Contents
1. [Getting Started](#getting-started)
2. [Accessing the Portal](#accessing-the-portal)
3. [Viewing Assets](#viewing-assets)
4. [Creating New Assets](#creating-new-assets)
5. [Updating Asset Status](#updating-asset-status)
6. [Using Quick Actions](#using-quick-actions)
7. [Viewing Reports](#viewing-reports)
8. [FAQs](#faqs)
9. [Getting Help](#getting-help)

---

## Getting Started

### What is the Asset Management Portal?

The Asset Management Portal is a centralized system for tracking all organizational assets including laptops, desktops, monitors, printers, phones, and tablets.

### What can you do?

✅ View all organizational assets  
✅ Create new asset records  
✅ Update asset information  
✅ Track asset status (Available, Assigned, Damaged, Lost)  
✅ Monitor warranty expiration dates  
✅ Generate asset reports  
✅ Receive automated warranty alerts  

---

## Accessing the Portal

### Step 1: Log In
1. Open your web browser
2. Navigate to your ServiceNow instance URL
3. Enter your username and password
4. Click **Login**

### Step 2: Navigate to Asset Inventory
1. In the left navigation panel, type: **"Asset Inventory"**
2. Click on **Asset Inventory** under the dropdown
3. You'll see the list of all assets

**Quick Tip**: Bookmark the Asset Inventory page for faster access!

---

## Viewing Assets

### List View

When you open Asset Inventory, you'll see:

| Column | Description |
|--------|-------------|
| **Number** | Unique asset identifier (e.g., AST-0001) |
| **Asset Name** | Name/description of the asset |
| **Type** | Category (Laptop, Desktop, etc.) |
| **Status** | Current status |
| **Assigned To** | Person currently using the asset |
| **Purchase Date** | When asset was acquired |
| **Warranty Expire** | Warranty expiration date |

### Filtering Assets

**Find specific assets:**
1. Use the search box at the top
2. Type asset name, number, or assigned person
3. Press Enter

**Filter by status:**
1. Click the filter icon (funnel)
2. Select "Status"
3. Choose status (Available, Assigned, etc.)
4. Click **Run**

**Filter by type:**
1. Click the filter icon
2. Select "Type"
3. Choose asset type
4. Click **Run**

### Sorting Assets

Click on any column header to sort:
- Click once: Sort ascending (A-Z, 0-9)
- Click twice: Sort descending (Z-A, 9-0)

---

## Creating New Assets

### When to Create an Asset Record

Create a new asset record when:
- New equipment is purchased
- Equipment is received as a donation
- Previously untracked equipment needs to be added

### How to Create an Asset

**Step 1: Open New Asset Form**
1. Navigate to Asset Inventory
2. Click **New** button (top-right)
3. New asset form opens

**Step 2: Fill in Required Information**

| Field | What to Enter | Example |
|-------|---------------|---------|
| **Asset Name*** | Descriptive name | "Dell Laptop - Finance Dept" |
| **Type*** | Select from dropdown | Laptop |
| **Status*** | Current status | Available |
| **Assigned To** | Person using it (if assigned) | John Smith |
| **Purchase Date** | Date of acquisition | 2024-01-15 |
| **Warranty Expire** | Warranty end date | 2027-01-15 |
| **Number** | Auto-generated or manual | AST-0025 |

*Required fields

**Step 3: Save the Asset**
- Click **Submit** to save and close
- OR click **Submit and Stay** to save and continue editing

**Success!** Your asset is now in the system. ✅

---

## Updating Asset Status

### Manual Status Update

1. Open the asset record
2. Click the **Status** dropdown
3. Select new status:
   - **Available**: Ready for assignment
   - **Assigned**: Currently in use
   - **Damaged**: Needs repair
   - **Lost**: Cannot be located
4. Click **Save** or **Update**

### Updating Assignment

**To assign an asset:**
1. Open the asset record
2. Click **Assigned To** field
3. Search for employee name
4. Select the employee
5. Change **Status** to "Assigned"
6. Click **Save**

**To unassign an asset:**
1. Open the asset record
2. Clear the **Assigned To** field
3. Change **Status** to "Available"
4. Click **Save**

---

## Using Quick Actions

Quick action buttons help you change asset status with one click!

### Mark As Lost 🔴

**When to use**: Asset cannot be located

**How to use**:
1. Open the asset record
2. Click **Mark As Lost** button
3. Status automatically changes to "Lost"

**Note**: Button only appears when status is NOT already "Lost"

---

### Mark As Damaged ⚠️

**When to use**: Asset is broken or malfunctioning

**How to use**:
1. Open the asset record
2. Click **Mark As Damaged** button
3. Status automatically changes to "Damaged"

**Note**: Button only appears when status is NOT already "Damaged"

---

### Mark As Repaired ✅

**When to use**: Asset has been fixed and is ready for use

**How to use**:
1. Open the asset record (status must be "Damaged" or "Lost")
2. Click **Mark As Repaired** button
3. Status automatically changes to "Available"

**Note**: Button only appears when status is "Damaged" or "Lost"

---

## Viewing Reports

### Available vs Assigned Assets Report

This report shows how assets are distributed across different statuses.

**How to access:**
1. Navigate to **Reports** in left navigation
2. Search for "Available vs Assigned Assets"
3. Click on the report name
4. Report displays as a pie chart

**What you'll see:**
- 🟢 **Available**: Assets ready for assignment
- 🔵 **Assigned**: Assets in use
- 🟠 **Damaged**: Assets needing repair
- 🔴 **Lost**: Missing assets

**Using the Report:**
- Hover over sections for exact numbers
- Click sections to filter and drill down
- Use for planning and resource allocation

**Refreshing the Report:**
- Click **Run** button to refresh with latest data
- Data updates automatically each time you open it

---

## Common Workflows

### Workflow 1: New Employee Onboarding

**Scenario**: New employee needs a laptop

1. Go to Asset Inventory
2. Filter status = "Available"
3. Filter type = "Laptop"
4. Open suitable laptop record
5. Set **Assigned To** = New employee name
6. Change **Status** to "Assigned"
7. Click **Save**

✅ Done! Employee assigned to asset.

---

### Workflow 2: Equipment Return

**Scenario**: Employee returns laptop

1. Find the asset (search by employee name)
2. Open the asset record
3. Clear **Assigned To** field
4. Change **Status** to "Available"
5. Click **Save**

✅ Asset available for next assignment!

---

### Workflow 3: Reporting Damaged Equipment

**Scenario**: Laptop screen is cracked

1. Find and open the laptop record
2. Click **Mark As Damaged** button
3. Add note in **Work Notes** (optional):
   - Click **Work Notes** tab
   - Enter: "Screen cracked, needs replacement"
4. Click **Save**

✅ IT team will be notified!

---

### Workflow 4: Lost Equipment

**Scenario**: Cannot locate company phone

1. Find and open the phone record
2. Click **Mark As Lost** button
3. Add details in **Work Notes**:
   - Last known location
   - Date last seen
4. Click **Save**

✅ Asset marked as lost for investigation.

---

## Warranty Management

### Understanding Warranty Alerts

The system automatically monitors warranty expiration dates.

**How it works:**
- Every day at 12:00 PM, system checks all warranties
- If warranty expires within 30 days, email sent to IT Support
- Proactive notification prevents service gaps

**What you need to do:**
- Keep **Warranty Expire** dates accurate
- Update dates when warranties are renewed
- Review warranty status before assigning assets

**Checking Warranty Status:**
1. Open asset record
2. Look at **Warranty Expire** field
3. Green = Valid, Red = Expired (visual indicator)

---

## FAQs

### General Questions

**Q: Who can access the Asset Management Portal?**  
A: All employees with ServiceNow access can view assets. Creating and updating may require specific permissions.

**Q: How do I know if an asset is available?**  
A: Check the Status field. "Available" means ready for assignment.

**Q: Can I assign an asset to myself?**  
A: Yes! Open the asset, set Assigned To = Your name, change Status to Assigned, and save.

**Q: What if I make a mistake?**  
A: Simply open the record and correct the information. All changes are tracked in the system.

---

### Technical Questions

**Q: Why don't I see the quick action buttons?**  
A: Buttons only appear when relevant. For example, "Mark As Lost" doesn't show if status is already "Lost".

**Q: How do I search for assets?**  
A: Use the search box at the top of the Asset Inventory list. Search by name, number, or assigned person.

**Q: Can I export the asset list?**  
A: Yes! Click the context menu (≡) in the list view and select "Export" > Choose format (Excel, PDF, etc.)

**Q: How often do reports update?**  
A: Reports show real-time data. Click "Run" to refresh with latest information.

---

### Workflow Questions

**Q: What's the difference between "Available" and "Assigned"?**  
A: 
- **Available**: Asset is not currently assigned to anyone
- **Assigned**: Asset is currently in use by a specific person

**Q: When should I use "Mark As Damaged" vs "Mark As Lost"?**  
A:
- **Damaged**: Asset is physically present but not working
- **Lost**: Asset cannot be located

**Q: Can a damaged asset be assigned?**  
A: Not recommended. Use "Mark As Repaired" first to change status to "Available", then assign.

**Q: How do I transfer an asset between employees?**  
A:
1. Open the asset record
2. Change "Assigned To" to new employee
3. Status remains "Assigned"
4. Click Save

---

## Best Practices

### Do's ✅

✅ **Keep information current** - Update asset records promptly  
✅ **Use descriptive names** - Make assets easy to identify  
✅ **Document changes** - Add notes explaining status changes  
✅ **Check warranty dates** - Verify before assigning assets  
✅ **Use quick actions** - Faster than manual status updates  
✅ **Report issues promptly** - Mark damaged/lost assets immediately  

### Don'ts ❌

❌ **Don't delete assets** - Mark as Lost instead  
❌ **Don't leave fields blank** - Complete all relevant information  
❌ **Don't assign damaged assets** - Repair first  
❌ **Don't ignore warranty alerts** - Act on notifications  
❌ **Don't create duplicate records** - Search first  

---

## Getting Help

### Need Assistance?

**Option 1: IT Support Team**
- Email: it-support@company.com
- Phone: (123) 456-7890
- Hours: Monday-Friday, 8:00 AM - 5:00 PM

**Option 2: ServiceNow Help**
- Click the **?** icon in ServiceNow
- Search knowledge base
- Submit a support ticket

**Option 3: Manager/Supervisor**
- Contact your direct supervisor
- They can help with asset-related questions

---

## Quick Reference Card

### Common Tasks Cheat Sheet

| Task | Quick Steps |
|------|-------------|
| View all assets | Navigate to Asset Inventory |
| Find specific asset | Use search box, enter name/number |
| Create new asset | Click New → Fill form → Submit |
| Assign asset | Open record → Set Assigned To → Status = Assigned → Save |
| Return asset | Open record → Clear Assigned To → Status = Available → Save |
| Report damaged | Open record → Click "Mark As Damaged" |
| Report lost | Open record → Click "Mark As Lost" |
| Mark repaired | Open record → Click "Mark As Repaired" |
| View reports | Navigate to Reports → Select report |

---

## Keyboard Shortcuts

Speed up your work with these shortcuts:

| Shortcut | Action |
|----------|--------|
| **Ctrl + Alt + G** | Go to (quick navigation) |
| **Ctrl + S** | Save record |
| **Ctrl + Shift + J** | Jump to field |
| **/** | Focus search box |

---

## Tips for Success

💡 **Bookmark frequently used pages** for quick access

💡 **Set up email notifications** for assets you manage

💡 **Use filters** to find assets quickly

💡 **Check reports weekly** to stay informed

💡 **Keep records updated** for accurate reporting

💡 **Document everything** in Work Notes

---

## Glossary

| Term | Definition |
|------|------------|
| **Asset** | Physical or digital item tracked in the system |
| **Status** | Current state of the asset (Available, Assigned, etc.) |
| **Warranty** | Period when manufacturer provides support/replacement |
| **Assigned To** | Person currently responsible for the asset |
| **UI Action** | Button that performs an action (e.g., Mark As Lost) |
| **Record** | Individual entry in the Asset Inventory table |

---

## Feedback

We value your input! Help us improve the Asset Management Portal:

- Share suggestions with IT Support
- Report issues or bugs
- Request new features
- Provide usability feedback

**Your feedback makes the system better for everyone!**

---

**Last Updated**: February 2026  
**Version**: 1.0  
**Questions?** Contact: it-support@company.com
