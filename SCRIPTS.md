# Scripts Documentation - Asset Management Portal

This document contains all scripts used in the Asset Management Portal project.

---

## Table of Contents
1. [UI Actions](#ui-actions)
2. [Scheduled Jobs](#scheduled-jobs)
3. [Background Scripts](#background-scripts)
4. [Utility Scripts](#utility-scripts)

---

## UI Actions

### 1. Mark As Lost

**Purpose**: Changes asset status to "Lost"

**Configuration**:
- **Name**: Mark As Lost
- **Table**: Asset Inventory (u_asset_inventory)
- **Action Name**: mark_as_lost
- **Form Button**: ✓ Checked
- **Condition**: `current.u_status != 'Lost'`

**Script**:
```javascript
(function() {
    // Update the status to Lost
    current.u_status = 'Lost';
    
    // Save the record
    current.update();
    
    // Redirect to the updated record
    action.setRedirectURL(current);
})();
```

**Usage**: 
- Available on asset form when status is not "Lost"
- Click button to mark asset as lost
- Status updates and form refreshes

---

### 2. Mark As Repaired

**Purpose**: Changes asset status from "Damaged" or "Lost" to "Available"

**Configuration**:
- **Name**: Mark As Repaired
- **Table**: Asset Inventory (u_asset_inventory)
- **Action Name**: mark_as_repaired
- **Form Button**: ✓ Checked
- **Condition**: `current.u_status == 'Damaged' || current.u_status == 'Lost'`

**Script**:
```javascript
(function() {
    // Update the status to Available
    current.u_status = 'Available';
    
    // Save the record
    current.update();
    
    // Redirect to the updated record
    action.setRedirectURL(current);
})();
```

**Usage**: 
- Available on asset form when status is "Damaged" or "Lost"
- Click button to mark asset as repaired
- Status changes to "Available"

---

### 3. Mark As Damaged

**Purpose**: Changes asset status to "Damaged"

**Configuration**:
- **Name**: Mark As Damaged
- **Table**: Asset Inventory (u_asset_inventory)
- **Action Name**: mark_as_damaged
- **Form Button**: ✓ Checked
- **Condition**: `current.u_status != 'Damaged'`

**Script**:
```javascript
(function() {
    // Update the status to Damaged
    current.u_status = 'Damaged';
    
    // Save the record
    current.update();
    
    // Redirect to the updated record
    action.setRedirectURL(current);
})();
```

**Usage**: 
- Available on asset form when status is not "Damaged"
- Click button to mark asset as damaged
- Status updates immediately

---

## Scheduled Jobs

### 1. Warranty Expiry Alert

**Purpose**: Sends email alerts for assets with warranties expiring within 30 days

**Configuration**:
- **Name**: Warranty Expiry Alert
- **Active**: ✓ Checked
- **Run**: Daily
- **Time**: 12:00:00
- **Time Zone**: (Your timezone)

**Script**:
```javascript
(function() {
    // Scheduled Job: Warranty Expiry Alert
    
    // Initialize GlideRecord for Asset Inventory table
    var grAsset = new GlideRecord('u_asset_inventory');
    
    // Get today's date
    var today = new GlideDateTime();
    
    // Calculate date 30 days from now
    var futureDate = new GlideDateTime();
    futureDate.addDays(30);
    
    // Query assets with warranty expiring between today and next 30 days
    grAsset.addQuery('u_warranty_expire', '>=', today);
    grAsset.addQuery('u_warranty_expire', '<=', futureDate);
    grAsset.query();
    
    // Loop through all matching assets
    while (grAsset.next()) {
        // Get asset details
        var assetName = grAsset.getValue('u_asset_name');
        var assetType = grAsset.getValue('u_type');
        var warrantyExpiry = grAsset.getValue('u_warranty_expire');
        var assetNumber = grAsset.getValue('u_number');
        
        // Compose email
        var email = new GlideEmailOutbound();
        email.setTo('it-support@company.com'); // Update with your email
        email.setSubject("Warranty Expiry Alert: " + assetName);
        
        // Email body with asset details
        var emailBody = "Dear IT Support Team,\n\n";
        emailBody += "This is an automated notification regarding an upcoming warranty expiration.\n\n";
        emailBody += "Asset Details:\n";
        emailBody += "- Asset Name: " + assetName + "\n";
        emailBody += "- Asset Number: " + assetNumber + "\n";
        emailBody += "- Type: " + assetType + "\n";
        emailBody += "- Warranty Expiration Date: " + warrantyExpiry + "\n\n";
        emailBody += "Please take necessary action to renew the warranty or plan for replacement.\n\n";
        emailBody += "Best regards,\n";
        emailBody += "Asset Management System";
        
        email.setBody(emailBody);
        email.send();
        
        // Log for confirmation
        gs.info("Warranty expiry email sent for asset: " + assetName + " (expires: " + warrantyExpiry + ")");
    }
    
    // Log total count
    gs.info("Warranty expiry alert job completed. Total alerts sent: " + grAsset.getRowCount());
    
})();
```

**Key Features**:
- Runs daily at 12:00 PM
- Checks warranties expiring in next 30 days
- Sends email notification for each asset
- Logs all activities

---

## Background Scripts

### 1. Test Warranty Expiry Job

**Purpose**: Manually test the warranty expiry alert logic

**Location**: System Definition > Scripts - Background

**Script**:
```javascript
// Test script for Warranty Expiry Alert
var grAsset = new GlideRecord('u_asset_inventory');
var today = new GlideDateTime();
var futureDate = new GlideDateTime();
futureDate.addDays(30);

// Query assets
grAsset.addQuery('u_warranty_expire', '>=', today);
grAsset.addQuery('u_warranty_expire', '<=', futureDate);
grAsset.query();

gs.info("=== Warranty Expiry Test Results ===");
gs.info("Assets found: " + grAsset.getRowCount());

while (grAsset.next()) {
    var assetName = grAsset.getValue('u_asset_name');
    var assetType = grAsset.getValue('u_type');
    var warrantyExpiry = grAsset.getValue('u_warranty_expire');
    
    gs.info("Asset: " + assetName + " | Type: " + assetType + " | Expires: " + warrantyExpiry);
    
    // Uncomment below to send actual emails
    /*
    var email = new GlideEmailOutbound();
    email.setTo('it-support@company.com');
    email.setSubject("Warranty Expiry Alert: " + assetName);
    email.setBody(
        "The warranty for asset '" + assetName + 
        "' (Type: " + assetType + ") is expiring soon on " + 
        warrantyExpiry + ". Please take necessary action."
    );
    email.send();
    */
    
    gs.info("Email would be sent for asset: " + assetName);
}

gs.info("=== Test Complete ===");
```

**Usage**:
1. Paste script in Scripts - Background
2. Click "Run Script"
3. Check output for results

---

### 2. Create Sample Data

**Purpose**: Populate Asset Inventory with test data

**Script**:
```javascript
// Create sample assets for testing
var assetTypes = ['Laptop', 'Desktop', 'Monitor', 'Printer', 'Phone', 'Tablet'];
var statusTypes = ['Available', 'Assigned', 'Damaged', 'Lost'];

for (var i = 1; i <= 10; i++) {
    var gr = new GlideRecord('u_asset_inventory');
    gr.initialize();
    
    gr.u_asset_name = 'Test Asset ' + i;
    gr.u_number = 'AST-' + String(i).padStart(4, '0');
    gr.u_type = assetTypes[Math.floor(Math.random() * assetTypes.length)];
    gr.u_status = statusTypes[Math.floor(Math.random() * statusTypes.length)];
    
    // Set purchase date to random date in past year
    var purchaseDate = new GlideDateTime();
    purchaseDate.addDays(-Math.floor(Math.random() * 365));
    gr.u_purchase_date = purchaseDate;
    
    // Set warranty expiry to 3 years from purchase
    var warrantyDate = new GlideDateTime(purchaseDate);
    warrantyDate.addYears(3);
    gr.u_warranty_expire = warrantyDate;
    
    // Random assignment
    if (gr.u_status == 'Assigned') {
        // Assign to a random user
        var userGR = new GlideRecord('sys_user');
        userGR.setLimit(1);
        userGR.query();
        if (userGR.next()) {
            gr.u_assigned_to = userGR.sys_id;
        }
    }
    
    var sysId = gr.insert();
    gs.info("Created asset: " + gr.u_asset_name + " (sys_id: " + sysId + ")");
}

gs.info("Sample data creation complete!");
```

---

### 3. Clean Up Test Data

**Purpose**: Remove all test assets from the system

**Script**:
```javascript
// WARNING: This will delete all test assets
// Use with caution!

var gr = new GlideRecord('u_asset_inventory');
gr.addQuery('u_asset_name', 'STARTSWITH', 'Test Asset');
gr.query();

var count = 0;
while (gr.next()) {
    gs.info("Deleting: " + gr.u_asset_name);
    gr.deleteRecord();
    count++;
}

gs.info("Deleted " + count + " test assets");
```

---

## Utility Scripts

### 1. Check Asset Status Distribution

**Purpose**: Display count of assets by status

**Script**:
```javascript
var statuses = ['Available', 'Assigned', 'Damaged', 'Lost'];

gs.info("=== Asset Status Distribution ===");

for (var i = 0; i < statuses.length; i++) {
    var gr = new GlideRecord('u_asset_inventory');
    gr.addQuery('u_status', statuses[i]);
    gr.query();
    
    gs.info(statuses[i] + ": " + gr.getRowCount());
}

gs.info("=== End Report ===");
```

---

### 2. Find Assets Expiring Soon

**Purpose**: List all assets with warranties expiring in specified days

**Script**:
```javascript
// Configurable: Change days as needed
var daysAhead = 30;

var today = new GlideDateTime();
var futureDate = new GlideDateTime();
futureDate.addDays(daysAhead);

var gr = new GlideRecord('u_asset_inventory');
gr.addQuery('u_warranty_expire', '>=', today);
gr.addQuery('u_warranty_expire', '<=', futureDate);
gr.orderBy('u_warranty_expire');
gr.query();

gs.info("=== Assets with Warranty Expiring in " + daysAhead + " Days ===");

while (gr.next()) {
    var daysRemaining = GlideDateTime.subtract(
        new GlideDateTime(gr.u_warranty_expire),
        today
    ).getDayPart();
    
    gs.info(
        gr.u_asset_name + " | " +
        gr.u_type + " | " +
        "Expires: " + gr.u_warranty_expire + " | " +
        "Days Remaining: " + daysRemaining
    );
}

gs.info("Total assets: " + gr.getRowCount());
gs.info("=== End Report ===");
```

---

### 3. Update Multiple Assets

**Purpose**: Bulk update asset status

**Script**:
```javascript
// Example: Mark all "Lost" assets as "Available"
// Modify query and update as needed

var gr = new GlideRecord('u_asset_inventory');
gr.addQuery('u_status', 'Lost');
gr.query();

var count = 0;
while (gr.next()) {
    gr.u_status = 'Available';
    gr.update();
    count++;
}

gs.info("Updated " + count + " assets from Lost to Available");
```

---

### 4. Asset Report by Type

**Purpose**: Count assets grouped by type

**Script**:
```javascript
var types = ['Laptop', 'Desktop', 'Monitor', 'Printer', 'Phone', 'Tablet'];

gs.info("=== Asset Count by Type ===");

var total = 0;
for (var i = 0; i < types.length; i++) {
    var gr = new GlideRecord('u_asset_inventory');
    gr.addQuery('u_type', types[i]);
    gr.query();
    
    var count = gr.getRowCount();
    total += count;
    
    gs.info(types[i] + ": " + count);
}

gs.info("Total Assets: " + total);
gs.info("=== End Report ===");
```

---

## Best Practices

### Script Writing Guidelines

1. **Always use try-catch blocks for error handling**:
```javascript
try {
    // Your code here
} catch (e) {
    gs.error("Error in script: " + e.message);
}
```

2. **Use GlideRecord query limits when testing**:
```javascript
gr.setLimit(10); // Limit results to 10
gr.query();
```

3. **Log important actions**:
```javascript
gs.info("Action performed successfully");
gs.error("Error occurred: " + errorMessage);
gs.warn("Warning: Check this condition");
```

4. **Validate data before updates**:
```javascript
if (current.u_status.nil() || current.u_status == '') {
    gs.addErrorMessage('Status cannot be empty');
    return false;
}
```

5. **Use proper date handling**:
```javascript
var gdt = new GlideDateTime();
gdt.setDisplayValue('2024-12-31 00:00:00');
```

---

## Debugging Tips

### Enable Debug Logging

1. Navigate to **System Diagnostics > Debug Log**
2. Add your session to debug logging
3. Run your script
4. Check logs in **System Logs > System Log**

### Test in Background Scripts First

Before deploying:
1. Test all logic in Scripts - Background
2. Verify expected output
3. Check for errors
4. Then implement in production

### Common Issues and Fixes

| Issue | Solution |
|-------|----------|
| Script doesn't execute | Check active checkbox |
| Condition not working | Verify field names (use u_ prefix) |
| Email not sending | Check email configuration |
| Date comparison failing | Use GlideDateTime class |
| Record not updating | Call .update() method |

---

## Script Maintenance

### Regular Tasks

1. **Monthly Review**:
   - Check scheduled job logs
   - Verify email delivery
   - Review error logs

2. **Quarterly Updates**:
   - Update email addresses
   - Review time schedules
   - Optimize query performance

3. **Annual Audit**:
   - Review all custom scripts
   - Update documentation
   - Remove unused code

---

**Last Updated**: February 2026  
**Version**: 1.0  
**Maintained by**: [Your Name]
