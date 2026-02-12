# Installation Guide - Asset Management Portal

## Prerequisites

Before you begin, ensure you have:
- ✅ Access to a ServiceNow instance (Personal Developer Instance or Enterprise)
- ✅ System Administrator role
- ✅ Basic familiarity with ServiceNow navigation

## Installation Steps

### Part 1: Create Custom Table

#### Step 1: Access Table Creation
1. Log in to your ServiceNow instance
2. In the **Application Navigator** (left panel), type: `tables`
3. Click on **System Definition > Tables**
4. Click the **New** button

#### Step 2: Configure Table Properties
Fill in the following details:
- **Label**: `Asset Inventory`
- **Name**: Will auto-populate as `u_asset_inventory`
- Leave other settings as default

#### Step 3: Save the Table
- Click **Submit**
- Your custom table is now created!

---

### Part 2: Create Custom Fields

After saving the table, you'll be redirected to the table record. Now create the following fields:

#### Field 1: Assigned To
1. Scroll down to the **Columns** section
2. Click **New** in the Columns tab
3. Configure:
   - **Type**: Reference
   - **Column label**: Assigned To
   - **Column name**: u_assigned_to
   - **Reference**: User [sys_user]
4. Click **Submit**

#### Field 2: Status
1. Click **New** in the Columns tab
2. Configure:
   - **Type**: Choice
   - **Column label**: Status
   - **Column name**: u_status
3. Click **Submit**
4. After submission, add choices:
   - Available
   - Assigned
   - Damaged
   - Lost

#### Field 3: Purchase Date
1. Click **New** in the Columns tab
2. Configure:
   - **Type**: Date
   - **Column label**: Purchase Date
   - **Column name**: u_purchase_date
3. Click **Submit**

#### Field 4: Warranty Expire
1. Click **New** in the Columns tab
2. Configure:
   - **Type**: Date
   - **Column label**: Warranty Expire
   - **Column name**: u_warranty_expire
3. Click **Submit**

#### Field 5: Asset Name
1. Click **New** in the Columns tab
2. Configure:
   - **Type**: String
   - **Column label**: Asset Name
   - **Column name**: u_asset_name
   - **Max length**: 100
3. Click **Submit**

#### Field 6: Type
1. Click **New** in the Columns tab
2. Configure:
   - **Type**: Choice
   - **Column label**: Type
   - **Column name**: u_type
3. Click **Submit**
4. After submission, add choices:
   - Laptop
   - Desktop
   - Monitor
   - Printer
   - Phone
   - Tablet

#### Field 7: Number
1. Click **New** in the Columns tab
2. Configure:
   - **Type**: String
   - **Column label**: Number
   - **Column name**: u_number
   - **Max length**: 40
3. Click **Submit**

---

### Part 3: Create UI Actions

#### UI Action 1: Mark As Lost

1. Navigate to **System Definition > UI Actions**
2. Click **New**
3. Fill in the details:

```
Name: Mark As Lost
Table: Asset Inventory [u_asset_inventory]
Action name: mark_as_lost
Active: ✓ (checked)
Show insert: □ (unchecked)
Show update: ✓ (checked)
Form button: ✓ (checked)
Form context menu: □ (unchecked)

Condition:
current.u_status != 'Lost'

Script:
(function() {
    current.u_status = 'Lost';
    current.update();
    action.setRedirectURL(current);
})();
```

4. Click **Submit**

#### UI Action 2: Mark As Repaired

1. Navigate to **System Definition > UI Actions**
2. Click **New**
3. Fill in the details:

```
Name: Mark As Repaired
Table: Asset Inventory [u_asset_inventory]
Action name: mark_as_repaired
Active: ✓ (checked)
Show insert: □ (unchecked)
Show update: ✓ (checked)
Form button: ✓ (checked)
Form context menu: □ (unchecked)

Condition:
current.u_status == 'Damaged' || current.u_status == 'Lost'

Script:
(function() {
    current.u_status = 'Available';
    current.update();
    action.setRedirectURL(current);
})();
```

4. Click **Submit**

#### UI Action 3: Mark As Damaged

1. Navigate to **System Definition > UI Actions**
2. Click **New**
3. Fill in the details:

```
Name: Mark As Damaged
Table: Asset Inventory [u_asset_inventory]
Action name: mark_as_damaged
Active: ✓ (checked)
Show insert: □ (unchecked)
Show update: ✓ (checked)
Form button: ✓ (checked)
Form context menu: □ (unchecked)

Condition:
current.u_status != 'Damaged'

Script:
(function() {
    current.u_status = 'Damaged';
    current.update();
    action.setRedirectURL(current);
})();
```

4. Click **Submit**

---

### Part 4: Create Scheduled Job

1. Navigate to **System Definition > Scheduled Jobs**
2. Click **New**
3. Fill in the details:

```
Name: Warranty Expiry Alert
Active: ✓ (checked)
Run: Daily
Run this time: 12:00:00
Time Zone: (Select your timezone)
```

4. In the **Script** field, paste:

```javascript
(function() {
    // Scheduled Job: Warranty Expiry Alert
    var grAsset = new GlideRecord('u_asset_inventory');
    var today = new GlideDateTime();
    var futureDate = new GlideDateTime();
    futureDate.addDays(30); // Check warranties expiring in next 30 days
    
    // Query assets with warranty expiring between today and next 30 days
    grAsset.addQuery('u_warranty_expire', '>=', today);
    grAsset.addQuery('u_warranty_expire', '<=', futureDate);
    grAsset.query();
    
    while (grAsset.next()) {
        var assetName = grAsset.getValue('u_asset_name');
        var assetType = grAsset.getValue('u_type');
        var warrantyExpiry = grAsset.getValue('u_warranty_expire');
        
        // Compose email
        var email = new GlideEmailOutbound();
        email.setTo('it-support@company.com'); // Update with your email
        email.setSubject("Warranty Expiry Alert: " + assetName);
        email.setBody(
            "The warranty for asset '" + assetName + 
            "' (Type: " + assetType + ") is expiring soon on " + 
            warrantyExpiry + ". Please take necessary action."
        );
        email.send();
        
        // Log for confirmation
        gs.info("Email sent for asset: " + assetName);
    }
})();
```

5. Click **Submit**

**Important**: Update the email address `it-support@company.com` to your actual IT support email.

---

### Part 5: Create Report

1. Navigate to **Reports > View/Run**
2. Click **Create New**
3. Configure the report:

```
Report Name: Available vs Assigned Assets
Source Type: Table
Table: Asset Inventory [u_asset_inventory]
Type: Pie Chart
Group by: Status
Aggregation: Count
```

4. Click **Save**
5. Click **Run** to view the report

---

## Verification Steps

### 1. Verify Table Creation
- Navigate to **Asset Inventory** table
- Confirm all fields are visible
- Create a test record to ensure all fields work

### 2. Verify UI Actions
- Create an asset with status "Available"
- Open the record
- Verify all three buttons appear:
  - Mark As Lost
  - Mark As Damaged
  - Mark As Repaired (should not appear for "Available" status)

### 3. Verify Scheduled Job
- Navigate to **System Definition > Scripts - Background**
- Paste the scheduled job script
- Click **Run Script**
- Check logs for execution confirmation

### 4. Verify Report
- Ensure multiple assets with different statuses exist
- Open the report
- Confirm pie chart displays correctly

---

## Troubleshooting

### Issue: UI Actions not appearing
**Solution**: 
- Check that "Form button" is checked
- Verify the table is correctly set to "Asset Inventory"
- Check condition syntax

### Issue: Scheduled Job not running
**Solution**:
- Verify the job is set to "Active"
- Check the time zone settings
- Review logs in **System Logs > System Log > All**

### Issue: Fields not showing on form
**Solution**:
- Configure form layout: Right-click form header > Configure > Form Layout
- Add missing fields to the form

### Issue: Email not sending
**Solution**:
- Verify email configuration in **System Properties > Email**
- Check if email address is valid
- Review email logs

---

## Post-Installation Configuration

### Optional: Create Application Menu
1. Navigate to **System Applications > Studio**
2. Create a new application for Asset Management
3. Add the Asset Inventory table to the application

### Optional: Configure Access Controls
1. Set up roles for different user types
2. Configure read/write permissions
3. Restrict UI action visibility based on roles

### Optional: Customize Form Layout
1. Right-click on Asset Inventory form
2. Select **Configure > Form Layout**
3. Arrange fields as needed
4. Add sections for better organization

---

## Next Steps

After successful installation:
1. ✅ Create test data
2. ✅ Test all UI actions
3. ✅ Run scheduled job manually
4. ✅ Generate reports
5. ✅ Configure email notifications
6. ✅ Set up user training
7. ✅ Go live!

---

## Support

For issues or questions:
- Review ServiceNow documentation
- Check community forums
- Contact your ServiceNow administrator

**Installation Complete!** 🎉
