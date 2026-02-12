# Quick Start Guide - Asset Management Portal

Get up and running in 30 minutes! ⚡

---

## 🚀 Overview

This guide will help you deploy the Asset Management Portal quickly. For detailed instructions, see [INSTALLATION.md](INSTALLATION.md).

---

## ✅ Prerequisites Checklist

Before you begin, ensure you have:

- [ ] ServiceNow instance access (PDI or Enterprise)
- [ ] System Administrator role
- [ ] 30 minutes of time
- [ ] Basic ServiceNow knowledge

---

## 📝 Installation in 5 Steps

### Step 1: Create Table (5 minutes)

1. Navigate to **System Definition > Tables**
2. Click **New**
3. Set **Label**: `Asset Inventory`
4. Click **Submit**

**✓ Checkpoint**: Table created with name `u_asset_inventory`

---

### Step 2: Add Fields (10 minutes)

Copy and paste this script in **Scripts - Background**:

```javascript
// Quick field creation script
var table = 'u_asset_inventory';

// Create fields
var fields = [
    {label: 'Asset Name', type: 'string', length: 100},
    {label: 'Type', type: 'choice'},
    {label: 'Status', type: 'choice'},
    {label: 'Assigned To', type: 'reference', reference: 'sys_user'},
    {label: 'Purchase Date', type: 'date'},
    {label: 'Warranty Expire', type: 'date'},
    {label: 'Number', type: 'string', length: 40}
];

fields.forEach(function(field) {
    var f = new GlideRecord('sys_dictionary');
    f.initialize();
    f.name = table;
    f.column_label = field.label;
    f.internal_type = field.type;
    if (field.length) f.max_length = field.length;
    if (field.reference) f.reference = field.reference;
    f.insert();
    gs.info('Created field: ' + field.label);
});
```

**Manual Alternative** (if script doesn't work):
- Add each field one by one through the UI
- See [INSTALLATION.md](INSTALLATION.md) for detailed steps

**Add Choices for Type field:**
- Laptop, Desktop, Monitor, Printer, Phone, Tablet

**Add Choices for Status field:**
- Available, Assigned, Damaged, Lost

**✓ Checkpoint**: 7 fields created

---

### Step 3: Create UI Actions (5 minutes)

Navigate to **System Definition > UI Actions** and create three buttons:

**Button 1: Mark As Lost**
```javascript
Name: Mark As Lost
Table: u_asset_inventory
Action name: mark_as_lost
Condition: current.u_status != 'Lost'
Form button: ✓

Script:
current.u_status = 'Lost';
current.update();
action.setRedirectURL(current);
```

**Button 2: Mark As Damaged**
```javascript
Name: Mark As Damaged
Table: u_asset_inventory
Action name: mark_as_damaged
Condition: current.u_status != 'Damaged'
Form button: ✓

Script:
current.u_status = 'Damaged';
current.update();
action.setRedirectURL(current);
```

**Button 3: Mark As Repaired**
```javascript
Name: Mark As Repaired
Table: u_asset_inventory
Action name: mark_as_repaired
Condition: current.u_status == 'Damaged' || current.u_status == 'Lost'
Form button: ✓

Script:
current.u_status = 'Available';
current.update();
action.setRedirectURL(current);
```

**✓ Checkpoint**: 3 UI actions created

---

### Step 4: Create Scheduled Job (5 minutes)

1. Navigate to **System Definition > Scheduled Jobs**
2. Click **New**
3. Fill in:
   - Name: `Warranty Expiry Alert`
   - Run: `Daily`
   - Time: `12:00:00`
4. Paste this script:

```javascript
var grAsset = new GlideRecord('u_asset_inventory');
var today = new GlideDateTime();
var futureDate = new GlideDateTime();
futureDate.addDays(30);

grAsset.addQuery('u_warranty_expire', '>=', today);
grAsset.addQuery('u_warranty_expire', '<=', futureDate);
grAsset.query();

while (grAsset.next()) {
    var assetName = grAsset.getValue('u_asset_name');
    var assetType = grAsset.getValue('u_type');
    var warrantyExpiry = grAsset.getValue('u_warranty_expire');
    
    var email = new GlideEmailOutbound();
    email.setTo('it-support@company.com'); // UPDATE THIS!
    email.setSubject("Warranty Expiry Alert: " + assetName);
    email.setBody(
        "The warranty for asset '" + assetName + 
        "' (Type: " + assetType + ") is expiring soon on " + 
        warrantyExpiry + ". Please take necessary action."
    );
    email.send();
    
    gs.info("Email sent for asset: " + assetName);
}
```

5. **IMPORTANT**: Update email address in script
6. Click **Submit**

**✓ Checkpoint**: Scheduled job created

---

### Step 5: Create Report (5 minutes)

1. Navigate to **Reports > Create New**
2. Fill in:
   - Name: `Available vs Assigned Assets`
   - Source: `Table`
   - Table: `Asset Inventory`
   - Type: `Pie Chart`
   - Group by: `Status`
   - Aggregation: `Count`
3. Click **Save** and **Run**

**✓ Checkpoint**: Report created and working

---

## 🧪 Quick Test (5 minutes)

### Test Asset Creation

1. Go to **Asset Inventory**
2. Click **New**
3. Fill in:
   - Asset Name: `Test Laptop`
   - Type: `Laptop`
   - Status: `Available`
   - Purchase Date: Today
   - Warranty Expire: 3 years from today
4. Click **Submit**

**✓ Expected**: Asset created successfully

---

### Test UI Actions

1. Open the test asset
2. Click **Mark As Damaged**
3. Verify status = "Damaged"
4. Click **Mark As Repaired**
5. Verify status = "Available"
6. Click **Mark As Lost**
7. Verify status = "Lost"

**✓ Expected**: All buttons work, status changes correctly

---

### Test Scheduled Job

1. Navigate to **Scripts - Background**
2. Paste the scheduled job script
3. Click **Run Script**
4. Check output

**✓ Expected**: `*** Script: Email sent for asset: Test Laptop`

---

### Test Report

1. Create 2-3 more assets with different statuses
2. Open the report
3. Verify pie chart shows all statuses

**✓ Expected**: Chart displays correctly with accurate counts

---

## ✨ You're Done!

**Congratulations!** Your Asset Management Portal is ready to use.

---

## 📚 Next Steps

### Immediate Actions

1. **Create More Test Data**
   - Add 10-20 sample assets
   - Test various scenarios
   - Verify all features work

2. **Configure Email**
   - Update email address in scheduled job
   - Test email delivery
   - Set up distribution lists

3. **Customize**
   - Add company logo
   - Customize form layout
   - Create additional choice values
   - Set up user roles

### Training & Rollout

1. **Create User Accounts**
   - Set up user permissions
   - Assign appropriate roles
   - Test access levels

2. **Train Users**
   - Share [USER_GUIDE.md](USER_GUIDE.md)
   - Conduct training sessions
   - Create quick reference cards
   - Set up help desk support

3. **Go Live**
   - Migrate from test to production
   - Import existing assets
   - Monitor for first week
   - Gather user feedback

---

## 🆘 Troubleshooting

### Common Issues

**Issue**: Fields not appearing on form
- **Solution**: Configure form layout (Right-click > Configure > Form Layout)

**Issue**: UI buttons not showing
- **Solution**: Check "Form button" is checked, verify conditions

**Issue**: Scheduled job not running
- **Solution**: Verify Active checkbox, check time zone settings

**Issue**: Emails not sending
- **Solution**: Check email configuration in System Properties

**Issue**: Report shows no data
- **Solution**: Create some assets first, refresh report

---

## 📖 Full Documentation

For detailed information, see:

- [README.md](README.md) - Complete overview
- [INSTALLATION.md](INSTALLATION.md) - Detailed setup
- [SCRIPTS.md](SCRIPTS.md) - Script reference
- [USER_GUIDE.md](USER_GUIDE.md) - End-user guide
- [TESTING.md](TESTING.md) - Testing procedures

---

## 💬 Get Help

**Questions?**
- Check FAQ in [README.md](README.md)
- Review [USER_GUIDE.md](USER_GUIDE.md)
- Open an issue on GitHub
- Email: [your.email@example.com]

---

## 🎯 Quick Reference

### Table Details
- **Name**: `u_asset_inventory`
- **Label**: Asset Inventory
- **Fields**: 7 custom fields

### UI Actions
- Mark As Lost
- Mark As Damaged
- Mark As Repaired

### Scheduled Job
- Name: Warranty Expiry Alert
- Frequency: Daily at 12:00 PM
- Function: Email alerts for expiring warranties

### Report
- Name: Available vs Assigned Assets
- Type: Pie Chart
- Purpose: Status distribution

---

## ⏱️ Time Breakdown

| Task | Time |
|------|------|
| Create Table | 5 min |
| Add Fields | 10 min |
| Create UI Actions | 5 min |
| Create Scheduled Job | 5 min |
| Create Report | 5 min |
| **Total Setup** | **30 min** |
| Testing | 5 min |
| **Grand Total** | **35 min** |

---

**Ready to get started? Let's go!** 🚀
