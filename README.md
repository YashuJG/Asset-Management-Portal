# Asset Management Portal

A comprehensive ServiceNow-based solution for streamlined tracking, management, and allocation of physical and digital assets across organizations.

## 📋 Table of Contents
- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Features](#features)
- [Technical Architecture](#technical-architecture)
- [Installation Guide](#installation-guide)
- [Configuration](#configuration)
- [Testing](#testing)
- [Screenshots](#screenshots)
- [Conclusion](#conclusion)
- [Future Enhancements](#future-enhancements)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

The Asset Management Portal streamlines the entire asset lifecycle management process in ServiceNow, enabling organizations to:
- Track physical and digital assets efficiently
- Automate asset allocation and tracking
- Monitor asset status in real-time
- Generate automated maintenance alerts
- Produce comprehensive utilization reports

## 📝 Problem Statement

Organizations face challenges in managing assets effectively, including:
- Manual tracking leading to errors and asset loss
- Lack of visibility into asset utilization
- Delayed maintenance and replacement cycles
- Inefficient asset allocation processes
- Difficulty in generating accurate reports

**Solution:** The Asset Management Portal centralizes asset management, automates workflows, and provides real-time visibility into asset status, ensuring optimal performance and reducing operational costs.

## ✨ Features

### 1. Custom Asset Inventory Table
- **Asset Name**: Unique identifier for each asset
- **Type**: Categorization (Laptop, Desktop, Monitor, etc.)
- **Assigned To**: Employee assignment tracking
- **Status**: Real-time status monitoring (Available, Assigned, Damaged, Lost)
- **Purchase Date**: Acquisition tracking
- **Warranty Expiry**: Automated warranty monitoring
- **Asset Number**: Unique asset identification

### 2. UI Actions (Form Buttons)
- **Mark As Lost**: Quick status update for lost assets
- **Mark As Repaired**: Restore damaged/lost assets to available status
- **Mark As Damaged**: Flag assets requiring maintenance

### 3. Automated Workflows
- **Warranty Expiry Alert**: Daily scheduled job monitoring warranties expiring within 30 days
- Automatic email notifications to IT support team
- Proactive maintenance planning

### 4. Reporting & Analytics
- **Available vs Assigned Assets**: Real-time pie chart visualization
- Asset utilization metrics
- Status distribution analysis

## 🏗️ Technical Architecture

### Components
```
Asset Management Portal
│
├── Custom Tables
│   └── Asset Inventory (u_asset_inventory)
│
├── UI Actions
│   ├── Mark As Lost
│   ├── Mark As Repaired
│   └── Mark As Damaged
│
├── Scheduled Jobs
│   └── Warranty Expiry Alert (Daily at 12:00)
│
└── Reports
    └── Available vs Assigned Assets (Pie Chart)
```

### Technology Stack
- **Platform**: ServiceNow
- **Scripting**: Glide API (Server-side JavaScript)
- **Automation**: Scheduled Jobs
- **Reporting**: ServiceNow Reporting Engine

## 🚀 Installation Guide

### Prerequisites
- ServiceNow instance (Personal Developer Instance or Enterprise)
- System Administrator role
- Basic understanding of ServiceNow platform

### Step-by-Step Setup

#### 1. Create Custom Table

1. Navigate to **System Definition > Tables**
2. Click **New**
3. Configure table:
   - **Name**: Asset Inventory
   - **Label**: Asset Inventory
4. Click **Submit**

#### 2. Create Custom Fields

After saving the table, add the following fields:

| Field Name | Type | Configuration |
|------------|------|---------------|
| Assigned To | Reference | Table: User (sys_user) |
| Status | Choice | Options: Available, Assigned, Damaged, Lost |
| Purchase Date | Date | - |
| Warranty Expire | Date | - |
| Asset Name | String | Max length: 100 |
| Type | Choice | Options: Laptop, Desktop, Monitor, Printer, etc. |
| Number | String | Max length: 40 |

#### 3. Configure UI Actions

**UI Action 1: Mark As Lost**
```javascript
// Navigate to System Definition > UI Actions > New
Name: Mark As Lost
Table: Asset Inventory
Action name: mark_as_lost
Condition: current.u_status != 'Lost'
Script:
current.u_status = 'Lost';
current.update();
action.setRedirectURL(current);

☑ Form button
```

**UI Action 2: Mark As Repaired**
```javascript
Name: Mark As Repaired
Table: Asset Inventory
Action name: mark_as_repaired
Condition: current.u_status == 'Damaged' || current.u_status == 'Lost'
Script:
current.u_status = 'Available';
current.update();
action.setRedirectURL(current);

☑ Form button
```

**UI Action 3: Mark As Damaged**
```javascript
Name: Mark As Damaged
Table: Asset Inventory
Action name: mark_as_damaged
Condition: current.u_status != 'Damaged'
Script:
current.u_status = 'Damaged';
current.update();
action.setRedirectURL(current);

☑ Form button
```

#### 4. Create Scheduled Job

Navigate to **System Definition > Scheduled Jobs > New**

```javascript
Name: Warranty Expiry Alert
Run: Daily
Time: 12:00:00

Script:
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
```

#### 5. Create Report

Navigate to **Reports > Create New**

Configuration:
- **Name**: Available vs Assigned Assets
- **Source Type**: Table
- **Table**: Asset Inventory
- **Type**: Pie Chart
- **Group By**: Status
- **Aggregation**: Count

Click **Save** and **Run**

## 🧪 Testing

### UI Actions Testing

1. **Create Test Asset**
   - Navigate to Asset Inventory
   - Click **New**
   - Fill in details:
     - Asset Name: Laptop
     - Type: Laptop
     - Assigned To: Abel Tutor
     - Status: Available
     - Purchase Date: (select date)
     - Warranty Expire: (select future date)
   - Click **Submit**

2. **Test Mark As Lost**
   - Open the created record
   - Click **Mark As Lost** button
   - Verify status changes to "Lost"

3. **Test Mark As Repaired**
   - With status as "Lost" or "Damaged"
   - Click **Mark As Repaired**
   - Verify status changes to "Available"

4. **Test Mark As Damaged**
   - With status not "Damaged"
   - Click **Mark As Damaged**
   - Verify status changes to "Damaged"

### Scheduled Job Testing

1. Navigate to **System Definition > Scripts - Background**
2. Paste the scheduled job script
3. Click **Run Script**
4. Check output for: `*** Script: Email sent for asset: Laptop`

### Report Testing

1. Create multiple assets with different statuses
2. Navigate to the "Available vs Assigned Assets" report
3. Verify pie chart displays accurate status distribution

## 📸 Screenshots

*Add screenshots of:*
- Asset Inventory table with records
- UI Actions in action (before/after status changes)
- Scheduled job execution logs
- Report visualization

## 🎓 Conclusion

The Asset Management Portal successfully demonstrates:

✅ **Centralized Asset Tracking**: All assets managed in a single, accessible platform

✅ **Automation Excellence**: Scheduled jobs and automated workflows reduce manual effort

✅ **Real-time Visibility**: Instant status updates and comprehensive reporting

✅ **Proactive Management**: Warranty alerts prevent service disruptions

✅ **Improved Efficiency**: Streamlined processes reduce asset loss and optimize utilization

### Business Impact
- **Reduced Asset Loss**: Improved tracking and accountability
- **Cost Optimization**: Proactive maintenance and warranty management
- **Enhanced Productivity**: Automated workflows free up IT resources
- **Data-Driven Decisions**: Real-time reports enable strategic planning

## 🚀 Future Enhancements

- [ ] Asset check-in/check-out workflow automation
- [ ] QR code integration for asset scanning
- [ ] Mobile app for asset management
- [ ] Integration with procurement systems
- [ ] Advanced analytics and predictive maintenance
- [ ] Asset depreciation tracking
- [ ] Multi-location asset management
- [ ] Integration with CMDB for IT assets

## 🤝 Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs and feature requests.

## 📄 License

This project is created for educational purposes as part of ServiceNow training and certification.

---

**Developed by**: [Your Name]  
**Platform**: ServiceNow  
**Date**: February 2026  
**Contact**: [Your Email]
