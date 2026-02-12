# Testing Guide - Asset Management Portal

## Overview
This document provides comprehensive testing procedures for all components of the Asset Management Portal.

---

## Test Environment Setup

### Prerequisites
- Asset Management Portal installed and configured
- System Administrator access
- Test user accounts created
- Email system configured (for scheduled job testing)

---

## Test Plan

### 1. Table and Field Testing

#### Test Case 1.1: Create Asset Record
**Objective**: Verify all fields accept and save data correctly

**Steps**:
1. Navigate to **Asset Inventory** table
2. Click **New**
3. Fill in all fields:
   - Asset Name: `Test Laptop 001`
   - Type: `Laptop`
   - Assigned To: Select a user
   - Status: `Available`
   - Purchase Date: `2024-01-15`
   - Warranty Expire: `2027-01-15`
   - Number: `AST-001`
4. Click **Submit**

**Expected Result**: 
- ✅ Record created successfully
- ✅ All fields populated correctly
- ✅ Record visible in Asset Inventory list

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 1.2: Validate Field Types
**Objective**: Ensure field validations work correctly

**Test Data**:
| Field | Test Input | Expected Behavior |
|-------|-----------|-------------------|
| Asset Name | Empty | Should allow (not mandatory) |
| Type | Invalid choice | Should restrict to dropdown |
| Assigned To | Invalid user | Should only accept valid users |
| Status | Custom value | Should restrict to defined choices |
| Purchase Date | Future date | Should accept any date |
| Warranty Expire | Past date | Should accept (for expired warranties) |

**Status**: ⬜ Pass | ⬜ Fail

---

### 2. UI Actions Testing

#### Test Case 2.1: Mark As Lost Button

**Pre-conditions**: Asset record with status NOT "Lost"

**Steps**:
1. Open an asset record with status "Available"
2. Verify "Mark As Lost" button is visible
3. Click "Mark As Lost"
4. Verify page refresh/redirect

**Expected Results**:
- ✅ Button visible when status ≠ Lost
- ✅ Status changes to "Lost"
- ✅ Record updates successfully
- ✅ Page redirects to updated record

**Test Data**:
| Initial Status | Button Visible? | Final Status |
|---------------|-----------------|--------------|
| Available | Yes | Lost |
| Assigned | Yes | Lost |
| Damaged | Yes | Lost |
| Lost | No | Lost |

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 2.2: Mark As Repaired Button

**Pre-conditions**: Asset record with status "Damaged" or "Lost"

**Steps**:
1. Create asset with status "Damaged"
2. Open the record
3. Verify "Mark As Repaired" button is visible
4. Click "Mark As Repaired"
5. Verify status change

**Expected Results**:
- ✅ Button visible when status = Damaged OR Lost
- ✅ Button NOT visible when status = Available or Assigned
- ✅ Status changes to "Available"
- ✅ Record updates successfully

**Test Data**:
| Initial Status | Button Visible? | Final Status |
|---------------|-----------------|--------------|
| Damaged | Yes | Available |
| Lost | Yes | Available |
| Available | No | Available |
| Assigned | No | Assigned |

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 2.3: Mark As Damaged Button

**Pre-conditions**: Asset record with status NOT "Damaged"

**Steps**:
1. Open asset with status "Available"
2. Verify "Mark As Damaged" button is visible
3. Click "Mark As Damaged"
4. Verify status change

**Expected Results**:
- ✅ Button visible when status ≠ Damaged
- ✅ Status changes to "Damaged"
- ✅ Record updates successfully

**Test Data**:
| Initial Status | Button Visible? | Final Status |
|---------------|-----------------|--------------|
| Available | Yes | Damaged |
| Assigned | Yes | Damaged |
| Lost | Yes | Damaged |
| Damaged | No | Damaged |

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 2.4: Button Condition Testing

**Objective**: Verify conditional display logic works correctly

**Test Matrix**:
| Status | Mark As Lost | Mark As Repaired | Mark As Damaged |
|--------|--------------|------------------|-----------------|
| Available | ✓ Visible | ✗ Hidden | ✓ Visible |
| Assigned | ✓ Visible | ✗ Hidden | ✓ Visible |
| Damaged | ✓ Visible | ✓ Visible | ✗ Hidden |
| Lost | ✗ Hidden | ✓ Visible | ✓ Visible |

**Status**: ⬜ Pass | ⬜ Fail

---

### 3. Scheduled Job Testing

#### Test Case 3.1: Manual Execution via Background Scripts

**Objective**: Verify scheduled job logic works correctly

**Steps**:
1. Create test assets with warranties expiring in 30 days:
   - Asset 1: Warranty expiring in 15 days
   - Asset 2: Warranty expiring in 25 days
   - Asset 3: Warranty expiring in 45 days (should NOT trigger)
   - Asset 4: Warranty expired yesterday (should NOT trigger)

2. Navigate to **System Definition > Scripts - Background**

3. Paste the following script:
```javascript
// Scheduled Job: Warranty Expiry Alert
var grAsset = new GlideRecord('u_asset_inventory');
var today = new GlideDateTime();
var futureDate = new GlideDateTime();
futureDate.addDays(30);

grAsset.addQuery('u_warranty_expire', '>=', today);
grAsset.addQuery('u_warranty_expire', '<=', futureDate);
grAsset.query();

gs.info("Assets found: " + grAsset.getRowCount());

while (grAsset.next()) {
    var assetName = grAsset.getValue('u_asset_name');
    var assetType = grAsset.getValue('u_type');
    var warrantyExpiry = grAsset.getValue('u_warranty_expire');
    
    var email = new GlideEmailOutbound();
    email.setTo('it-support@company.com');
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

4. Click **Run Script**

**Expected Results**:
- ✅ Script executes without errors
- ✅ Correct number of assets identified (2 in test data)
- ✅ Log shows: "Email sent for asset: [Asset Name]"
- ✅ Only assets expiring within 30 days processed
- ✅ Emails sent (check email logs)

**Validation**:
```
Output should show:
*** Script: Assets found: 2
*** Script: Email sent for asset: Asset 1
*** Script: Email sent for asset: Asset 2
```

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 3.2: Scheduled Job Configuration

**Objective**: Verify scheduled job is configured correctly

**Steps**:
1. Navigate to **System Definition > Scheduled Jobs**
2. Search for "Warranty Expiry Alert"
3. Open the job record
4. Verify configuration:
   - Active: ✓
   - Run: Daily
   - Time: 12:00:00
   - Script contains correct logic

**Expected Results**:
- ✅ Job is active
- ✅ Schedule is set correctly
- ✅ Script is present and correct

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 3.3: Email Notification Testing

**Objective**: Verify emails are sent with correct content

**Pre-requisite**: Create asset with warranty expiring in 20 days

**Steps**:
1. Run scheduled job manually (Test Case 3.1)
2. Navigate to **System Mailboxes > Outbox**
3. Locate sent email
4. Verify email content

**Expected Email Content**:
```
To: it-support@company.com
Subject: Warranty Expiry Alert: [Asset Name]
Body: The warranty for asset '[Asset Name]' (Type: [Type]) 
      is expiring soon on [Date]. Please take necessary action.
```

**Status**: ⬜ Pass | ⬜ Fail

---

### 4. Report Testing

#### Test Case 4.1: Report Creation and Execution

**Objective**: Verify report displays asset status distribution correctly

**Pre-conditions**: Create test assets with varied statuses:
- 3 Available
- 2 Assigned
- 2 Damaged
- 1 Lost

**Steps**:
1. Navigate to **Reports > View/Run**
2. Search for "Available vs Assigned Assets"
3. Click on the report
4. Verify report runs
5. Verify data accuracy

**Expected Results**:
- ✅ Report displays as Pie Chart
- ✅ All status types shown correctly
- ✅ Count matches actual data:
  - Available: 3
  - Assigned: 2
  - Damaged: 2
  - Lost: 1
- ✅ Percentages calculated correctly

**Status**: ⬜ Pass | ⬜ Fail

---

#### Test Case 4.2: Report Data Refresh

**Objective**: Verify report updates when data changes

**Steps**:
1. Note current report data
2. Create a new asset with status "Available"
3. Refresh the report
4. Verify count increased by 1

**Expected Results**:
- ✅ Report refreshes with new data
- ✅ Count updated accurately
- ✅ Percentages recalculated

**Status**: ⬜ Pass | ⬜ Fail

---

### 5. Integration Testing

#### Test Case 5.1: End-to-End Workflow

**Scenario**: Asset Lifecycle from Purchase to Damage to Repair

**Steps**:
1. Create new asset:
   - Asset Name: "Integration Test Laptop"
   - Status: "Available"
   - Purchase Date: Today
   - Warranty Expire: 3 years from today
   
2. Change status to "Assigned"
   - Manually update status field
   - Save record
   
3. Mark as Damaged:
   - Click "Mark As Damaged" button
   - Verify status = "Damaged"
   
4. Mark as Repaired:
   - Click "Mark As Repaired" button
   - Verify status = "Available"
   
5. Mark as Lost:
   - Click "Mark As Lost" button
   - Verify status = "Lost"
   
6. Verify in Report:
   - Open "Available vs Assigned Assets" report
   - Verify asset counted in "Lost" category

**Expected Results**:
- ✅ All status transitions work
- ✅ UI actions function correctly
- ✅ Report reflects current status

**Status**: ⬜ Pass | ⬜ Fail

---

### 6. Performance Testing

#### Test Case 6.1: Large Dataset Performance

**Objective**: Verify system handles 100+ assets

**Steps**:
1. Create 100 test assets using data import or script
2. Test UI actions on multiple records
3. Run report with full dataset
4. Execute scheduled job

**Expected Results**:
- ✅ Table loads within 3 seconds
- ✅ UI actions respond instantly
- ✅ Report generates within 5 seconds
- ✅ Scheduled job completes within 1 minute

**Status**: ⬜ Pass | ⬜ Fail

---

### 7. Security Testing

#### Test Case 7.1: Access Control

**Objective**: Verify appropriate users can access features

**Test Users**:
- Admin User
- Regular User (no admin rights)

**Access Matrix**:
| Feature | Admin | Regular User |
|---------|-------|--------------|
| View Assets | ✓ | ✓ |
| Create Assets | ✓ | ✓ |
| Update Assets | ✓ | ✓ |
| Delete Assets | ✓ | ✗ |
| View Reports | ✓ | ✓ |
| Modify Scheduled Jobs | ✓ | ✗ |

**Status**: ⬜ Pass | ⬜ Fail

---

## Test Results Summary

| Category | Total Tests | Passed | Failed | Pass Rate |
|----------|-------------|--------|--------|-----------|
| Table & Fields | 2 | | | |
| UI Actions | 4 | | | |
| Scheduled Jobs | 3 | | | |
| Reports | 2 | | | |
| Integration | 1 | | | |
| Performance | 1 | | | |
| Security | 1 | | | |
| **TOTAL** | **14** | | | |

---

## Issue Tracking

| Issue ID | Description | Severity | Status | Resolution |
|----------|-------------|----------|--------|------------|
| | | | | |

**Severity Levels**: Critical | High | Medium | Low

---

## Sign-off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Tester | | | |
| Developer | | | |
| Project Manager | | | |

---

## Testing Checklist

- [ ] All table fields created and validated
- [ ] All UI actions tested and working
- [ ] Scheduled job executes correctly
- [ ] Email notifications sent successfully
- [ ] Reports display accurate data
- [ ] End-to-end workflow validated
- [ ] Performance acceptable
- [ ] Security controls verified
- [ ] Documentation reviewed
- [ ] User acceptance testing completed

**Testing Status**: ⬜ Complete | ⬜ In Progress | ⬜ Not Started
