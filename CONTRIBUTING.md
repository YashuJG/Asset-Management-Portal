# Contributing to Asset Management Portal

Thank you for your interest in contributing to the Asset Management Portal! This document provides guidelines and instructions for contributing.

## Table of Contents
- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Coding Standards](#coding-standards)
- [Submission Guidelines](#submission-guidelines)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Enhancements](#suggesting-enhancements)

---

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive environment for all contributors.

### Expected Behavior

- Be respectful and considerate
- Welcome newcomers and help them get started
- Provide constructive feedback
- Focus on what is best for the community
- Show empathy towards other community members

### Unacceptable Behavior

- Harassment or discrimination of any kind
- Trolling or insulting comments
- Public or private harassment
- Publishing others' private information
- Other conduct which could reasonably be considered inappropriate

---

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues to avoid duplicates.

**Bug Report Template:**
```markdown
**Description:**
A clear description of the bug

**Steps to Reproduce:**
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

**Expected Behavior:**
What you expected to happen

**Actual Behavior:**
What actually happened

**Environment:**
- ServiceNow Version:
- Browser:
- OS:

**Screenshots:**
If applicable, add screenshots

**Additional Context:**
Any other relevant information
```

---

### Suggesting Enhancements

Enhancement suggestions are welcome! Please provide:

**Enhancement Template:**
```markdown
**Feature Description:**
Clear description of the proposed feature

**Use Case:**
Why is this feature needed?

**Proposed Solution:**
How would you implement this?

**Alternatives Considered:**
Other approaches you've thought about

**Additional Context:**
Any other relevant information
```

---

### Code Contributions

1. **Fork the Repository**
   - Click "Fork" on GitHub
   - Clone your fork locally

2. **Create a Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make Your Changes**
   - Write clean, documented code
   - Follow coding standards
   - Add tests if applicable
   - Update documentation

4. **Commit Your Changes**
   ```bash
   git add .
   git commit -m "Add feature: description"
   ```

5. **Push to Your Fork**
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Submit Pull Request**
   - Go to the original repository
   - Click "New Pull Request"
   - Select your branch
   - Fill in the PR template

---

## Development Setup

### Prerequisites

- ServiceNow Personal Developer Instance
- Basic JavaScript knowledge
- Understanding of ServiceNow platform
- Git installed locally

### Setup Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/asset-management-portal.git
   cd asset-management-portal
   ```

2. **Review Documentation**
   - Read README.md
   - Review INSTALLATION.md
   - Check SCRIPTS.md

3. **Set Up Test Instance**
   - Create ServiceNow PDI
   - Import table structure
   - Configure UI Actions
   - Set up Scheduled Jobs

4. **Create Test Data**
   - Use sample data scripts
   - Create diverse test scenarios
   - Verify all functionality

---

## Coding Standards

### JavaScript Style Guide

**General Principles:**
- Use clear, descriptive variable names
- Add comments for complex logic
- Follow ServiceNow best practices
- Keep functions small and focused

**Naming Conventions:**
```javascript
// Variables: camelCase
var assetName = 'Laptop';
var currentStatus = 'Available';

// Functions: camelCase
function updateAssetStatus() {
    // function code
}

// Constants: UPPER_CASE
var MAX_ASSETS = 1000;
var DEFAULT_STATUS = 'Available';

// Classes: PascalCase (if applicable)
var AssetManager = Class.create();
```

**Code Formatting:**
```javascript
// Good
(function() {
    var gr = new GlideRecord('u_asset_inventory');
    gr.addQuery('u_status', 'Available');
    gr.query();
    
    while (gr.next()) {
        // Process record
        gs.info('Processing: ' + gr.u_asset_name);
    }
})();

// Bad
var gr=new GlideRecord('u_asset_inventory');gr.addQuery('u_status','Available');gr.query();while(gr.next()){gs.info('Processing: '+gr.u_asset_name);}
```

**Error Handling:**
```javascript
try {
    // Your code
    current.u_status = 'Lost';
    current.update();
} catch (e) {
    gs.error('Error updating asset: ' + e.message);
    return false;
}
```

---

### ServiceNow Best Practices

**GlideRecord Queries:**
```javascript
// Good - Specific query
var gr = new GlideRecord('u_asset_inventory');
gr.addQuery('u_status', 'Available');
gr.addQuery('u_type', 'Laptop');
gr.setLimit(10);
gr.query();

// Bad - No limits, retrieves all records
var gr = new GlideRecord('u_asset_inventory');
gr.query();
```

**UI Actions:**
```javascript
// Good - Wrapped in IIFE
(function() {
    current.u_status = 'Damaged';
    current.update();
    action.setRedirectURL(current);
})();

// Bad - No function wrapper
current.u_status = 'Damaged';
current.update();
```

**Scheduled Jobs:**
```javascript
// Good - Efficient querying
(function() {
    var today = new GlideDateTime();
    var futureDate = new GlideDateTime();
    futureDate.addDays(30);
    
    var gr = new GlideRecord('u_asset_inventory');
    gr.addQuery('u_warranty_expire', '>=', today);
    gr.addQuery('u_warranty_expire', '<=', futureDate);
    gr.query();
    
    // Process records
})();
```

---

### Documentation Standards

**Inline Comments:**
```javascript
// Good - Explains WHY
// Check warranties expiring in next 30 days to send proactive alerts
futureDate.addDays(30);

// Bad - Explains WHAT (obvious)
// Add 30 days to date
futureDate.addDays(30);
```

**Function Documentation:**
```javascript
/**
 * Updates asset status and sends notification
 * @param {string} assetId - The sys_id of the asset
 * @param {string} newStatus - New status value
 * @returns {boolean} - True if successful, false otherwise
 */
function updateAssetStatus(assetId, newStatus) {
    // Function implementation
}
```

---

## Submission Guidelines

### Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Enhancement
- [ ] Documentation update
- [ ] Code refactoring

## Related Issue
Fixes #(issue number)

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
- [ ] Tested in development instance
- [ ] All existing tests pass
- [ ] Added new tests (if applicable)
- [ ] Tested UI Actions
- [ ] Tested Scheduled Jobs
- [ ] Verified reports

## Screenshots
If applicable, add screenshots

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No console warnings/errors
- [ ] Backward compatible

## Additional Notes
Any additional information
```

---

### Pull Request Process

1. **Before Submitting:**
   - Ensure code follows style guide
   - Run all tests
   - Update documentation
   - Rebase on latest main branch

2. **PR Review:**
   - Maintainers will review within 3-5 days
   - Address all feedback
   - Keep discussion professional
   - Make requested changes

3. **After Approval:**
   - PR will be merged by maintainer
   - Delete your feature branch
   - Update your local fork

---

## Testing Requirements

### Test Your Changes

**Manual Testing:**
- Create test assets
- Test all UI actions
- Verify scheduled jobs
- Check reports
- Test edge cases

**Test Scenarios:**
```
Scenario 1: Create Asset
- Verify all fields save correctly
- Check required field validation
- Confirm auto-numbering works

Scenario 2: UI Actions
- Test all three buttons
- Verify conditional display
- Check status updates
- Confirm redirects work

Scenario 3: Scheduled Job
- Run manually first
- Verify email generation
- Check logging output
- Confirm query accuracy

Scenario 4: Reports
- Verify data accuracy
- Test with various datasets
- Check chart rendering
- Validate export functionality
```

---

## Code Review Criteria

Reviewers will check for:

✅ **Functionality**
- Code works as intended
- Meets requirements
- No breaking changes

✅ **Code Quality**
- Follows style guide
- Well-documented
- Error handling present
- No code duplication

✅ **Testing**
- Adequate test coverage
- Edge cases considered
- Manual testing performed

✅ **Documentation**
- Code comments present
- README updated if needed
- CHANGELOG updated

✅ **Performance**
- Efficient queries
- No unnecessary loops
- Proper use of limits

---

## Getting Help

### Resources

**Documentation:**
- [README.md](README.md) - Project overview
- [INSTALLATION.md](INSTALLATION.md) - Setup guide
- [SCRIPTS.md](SCRIPTS.md) - Script reference
- [USER_GUIDE.md](USER_GUIDE.md) - End-user guide

**Community:**
- GitHub Issues - Ask questions
- Pull Requests - Discuss code
- ServiceNow Community - Platform help

**Contact:**
- Email: [your.email@example.com]
- GitHub: [@yourusername]

---

## Recognition

Contributors will be recognized in:
- README.md contributors section
- Release notes
- Project documentation

Thank you for contributing to Asset Management Portal! 🎉

---

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
