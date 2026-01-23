# Power Automate - FAB AUX Platform Documentation

## Overview

This directory contains comprehensive documentation for building a **Power Automate cloud flow** for First Abu Dhabi Bank's AUX (Auxiliary time) adjustment platform. This system digitizes the paper-based process for managing 315+ contact center agents' AUX time adjustments.

---

## 🚨 START HERE: DLP Compliance

**⚠️ If you've experienced DLP (Data Loss Prevention) issues before, read this FIRST:**

### [DLP Compliance Guide](DLP-Compliance-Guide.md) ⭐ **MUST READ**

This guide specifically addresses how to avoid DLP policy violations. The **#1 cause** of Power Automate flows being blocked by IT is incorrect HTTP trigger settings.

**Critical Rule:** Always use **"Only users in my tenant"** — Never use "Anyone"

Also available:
- **[DLP Pre-Flight Checklist](DLP-PreFlight-Checklist.md)** - Print and use before deployment

---

## 📁 Documentation Structure

### 0. [DLP Compliance Guide](DLP-Compliance-Guide.md) ⚠️ **READ FIRST**
**Critical guide to avoid DLP violations**

Essential reading before building the flow:
- Understanding DLP in Power Automate
- Common DLP violations and how to avoid them
- Critical settings checklist
- What to do if DLP blocks your flow
- IT communication templates
- Alternative approaches if HTTP trigger is blocked

**Best for:** Everyone building this flow - READ THIS FIRST to avoid having your flow suspended

**Length:** ~12,000 words | Critical reference

**Related:** [DLP Pre-Flight Checklist](DLP-PreFlight-Checklist.md) (printable checklist)

---

### 1. [FAB AUX Platform Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)
**Main comprehensive guide** - Start here!

Complete step-by-step instructions for building the Power Automate flow from scratch, including:
- Detailed configuration for each action
- Field mapping instructions
- Dynamic content selection
- Expression syntax
- Security implementation
- Testing procedures
- Maintenance guidelines

**Best for:** Building the flow for the first time, understanding how everything works

**Length:** ~30,000 words | Comprehensive reference

---

### 2. [JSON Schema Reference](JSON-Schema-Reference.md)
**Technical reference** for JSON schemas and data formats

Contains:
- Complete JSON schema for HTTP trigger and Parse JSON
- Field descriptions and validation rules
- Sample test payloads (6 different scenarios)
- PIN hash generation methods (JavaScript, Python, PowerShell, Bash)
- Response format specifications
- Testing with cURL and Postman
- Common mistakes to avoid

**Best for:** Form developers, testing, validation, integration work

**Length:** ~8,000 words | Technical reference

---

### 3. [Quick Troubleshooting Guide](Quick-Troubleshooting-Guide.md)
**Fast problem-solving** reference

Contains:
- Common error messages with instant fixes
- Quick diagnostic procedures
- Copy-paste code snippets
- Emergency recovery steps
- Debug mode checklist
- Performance troubleshooting

**Best for:** When something isn't working, quick fixes, debugging

**Length:** ~5,000 words | Quick reference

---

## 🚀 Quick Start

### For First-Time Builders

1. **⚠️ FIRST:** Read [DLP Compliance Guide](DLP-Compliance-Guide.md) to avoid policy violations
2. **Print:** [DLP Pre-Flight Checklist](DLP-PreFlight-Checklist.md) to use during building
3. **Build:** Follow [FAB AUX Platform Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)
4. **Reference:** Keep [JSON Schema Reference](JSON-Schema-Reference.md) open for copy-paste
5. **Troubleshoot:** Use [Quick Troubleshooting Guide](Quick-Troubleshooting-Guide.md) if issues arise

### For Form Developers

1. **Review:** [JSON Schema Reference](JSON-Schema-Reference.md) - Section "Field Descriptions"
2. **Implement:** PIN hashing using code samples provided
3. **Test:** Use sample payloads for validation

### For Troubleshooting

1. **Start with:** [Quick Troubleshooting Guide](Quick-Troubleshooting-Guide.md)
2. **If needed:** Check detailed explanations in main [Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)

---

## 📊 System Architecture

```
HTML Form → Power Automate Flow → SharePoint Lists
   ↓              ↓                      ↓
PIN Hash    Validate PIN          Store Records
Capture      Check Auth           Audit Trail
Data         Route Logic          Status Tracking
```

### Components

**Frontend:**
- HTML form with PIN authentication
- Client-side SHA-256 PIN hashing
- Form validation and submission

**Backend:**
- Power Automate cloud flow
- HTTP trigger (POST)
- PIN validation logic
- SharePoint integration

**Data Storage:**
- SharePoint List 1: `AUX_Platform_PINs` (authentication)
- SharePoint List 2: `AUX_Adjustments_2026` (records)

---

## 🔐 Security Features

- **PIN Security:** SHA-256 hashed (never plain text)
- **Authentication:** Server-side validation
- **Audit Trail:** IP address and device tracking
- **Access Control:** Tenant-restricted HTTP trigger
- **Data Privacy:** Sensitive data secured in SharePoint

---

## 📋 Flow Overview

### Actions Sequence

1. **HTTP Trigger** - Receive POST request with 18 fields
2. **Parse JSON** - Extract data from request body
3. **Get PINs** - Lookup agent's stored PIN hash from SharePoint
4. **Condition** - Validate submitted PIN hash against stored hash
5. **If Valid:**
   - Create record in AUX_Adjustments_2026
   - Return HTTP 200 success response
6. **If Invalid:**
   - Return HTTP 401 unauthorized response

### Key Features

- ✅ PIN-based authentication
- ✅ Automated record creation
- ✅ JSON response handling
- ✅ Audit trail logging
- ✅ DLP policy compliant
- ✅ No-code solution

---

## 🛠️ Technical Requirements

### Power Platform
- Power Automate Premium or per-user license
- SharePoint Online access
- "Only users in my tenant" flow permission

### SharePoint Setup
- Site: `https://bankfab.sharepoint.com/sites/FABInbound`
- Two lists configured with specific columns (see Flow Building Guide)
- Indexed columns for performance

### Skills Required
- Basic Power Automate knowledge
- Understanding of HTTP requests
- SharePoint list management
- JSON format understanding

---

## 📖 Document Comparison

| Feature | DLP Guide | Flow Building Guide | JSON Schema | Troubleshooting |
|---------|-----------|-------------------|-------------|----------------|
| **Purpose** | Avoid DLP violations | Complete implementation | Technical specs | Problem solving |
| **Priority** | ⚠️ READ FIRST | Primary guide | Reference | As needed |
| **Depth** | Compliance focused | Very detailed | Moderate | Quick reference |
| **Length** | ~12,000 words | ~30,000 words | ~8,000 words | ~5,000 words |
| **Best For** | Preventing IT blocks | Learning & building | Development | Fixing issues |
| **Critical?** | **YES - Essential** | Yes | No | As needed |
| **When to Read** | Before starting | During building | During testing | When errors occur |

---

## 🎯 Use Cases by Role

### RTM Officer / Project Manager
**Read:** Flow Building Guide (full understanding)
**Focus:** Architecture, security, maintenance sections

### Power Automate Developer
**Read:** Flow Building Guide (implementation)
**Reference:** JSON Schema Reference, Troubleshooting Guide

### HTML/Web Developer
**Read:** JSON Schema Reference
**Focus:** Field descriptions, sample payloads, PIN hashing code

### IT Support
**Read:** Quick Troubleshooting Guide
**Reference:** Flow Building Guide for detailed explanations

### Quality Assurance / Tester
**Read:** JSON Schema Reference (test payloads)
**Use:** Testing section in Flow Building Guide

---

## 🧪 Testing Resources

### Sample Test Data

**Test Staff ID:** `TEST001`
**Test PIN:** `1234`
**Test PIN Hash:** `03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4`

### Test Tools
- Postman (API testing)
- cURL (command-line testing)
- Browser Developer Tools (form testing)

### Test Payloads
See [JSON Schema Reference](JSON-Schema-Reference.md) for 6 complete sample payloads covering all AUX types.

---

## 📞 Support & Resources

### Documentation
- All guides in this directory
- SharePoint site documentation
- Power Automate official docs

### External Resources
- **Power Automate:** https://make.powerautomate.com
- **JSON Validator:** https://jsonlint.com
- **SHA-256 Generator:** https://emn178.github.io/online-tools/sha256.html
- **SharePoint Site:** https://bankfab.sharepoint.com/sites/FABInbound

---

## 📝 Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-01-23 | Initial documentation release |

---

## 🔄 Maintenance

### Regular Updates Needed
- **Annually:** Update SharePoint list name (e.g., AUX_Adjustments_2027)
- **Quarterly:** Review and update documentation
- **Monthly:** Verify flow performance and run history
- **As Needed:** Update when process changes

### Documentation Updates
When making changes to the flow, update:
1. Flow Building Guide (if steps change)
2. JSON Schema Reference (if fields change)
3. Troubleshooting Guide (if new issues discovered)
4. This README (if structure changes)

---

## ⚠️ Important Notes

1. **PIN Security:** Never store or transmit plain text PINs
2. **DLP Compliance:** Always use "Only users in my tenant" setting
3. **Testing:** Thoroughly test before production deployment
4. **Backup:** Export flow regularly for disaster recovery
5. **Monitoring:** Review run history weekly for issues

---

## 🤝 Contributing

If you discover issues or have improvements:
1. Document the issue with screenshots
2. Note the error message and run ID
3. Check troubleshooting guide first
4. Update documentation if new solution found

---

## 📄 Related Documents

### In This Repository
- [Main Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)
- [JSON Schema Reference](JSON-Schema-Reference.md)
- [Quick Troubleshooting Guide](Quick-Troubleshooting-Guide.md)

### External Links
- Power Automate Documentation: https://docs.microsoft.com/power-automate/
- SharePoint REST API: https://docs.microsoft.com/sharepoint/dev/sp-add-ins/
- JSON Schema Specification: https://json-schema.org/

---

## 📊 Statistics

- **Total Documentation:** ~43,000 words
- **Actions in Flow:** 7 (1 trigger + 6 actions)
- **JSON Fields:** 18 properties
- **SharePoint Lists:** 2
- **Response Codes:** 2 (200 success, 401 unauthorized)
- **Estimated Build Time:** 2-3 hours for first-time builders
- **Estimated Read Time:** 2 hours (all documents)

---

## 🎓 Learning Path

### Beginner (New to Power Automate)
1. Read Flow Building Guide introduction and architecture
2. Watch a Power Automate basics tutorial (external)
3. Follow Flow Building Guide step-by-step
4. Test thoroughly using JSON Schema Reference
5. Keep Troubleshooting Guide handy

### Intermediate (Familiar with Power Automate)
1. Skim Flow Building Guide architecture
2. Follow step-by-step instructions
3. Reference JSON Schema Reference for testing
4. Use Troubleshooting Guide as needed

### Advanced (Power Automate Expert)
1. Review architecture diagram
2. Copy JSON schema and expressions
3. Build flow using Quick Reference sections
4. Test with sample payloads

---

## 🔍 Keywords for Search

Power Automate, Cloud Flow, HTTP Trigger, SharePoint Integration, Parse JSON, Condition Logic, Response Action, PIN Authentication, SHA-256 Hashing, Contact Center, AUX Adjustment, Workforce Management, FAB, First Abu Dhabi Bank, RTM, Real-Time Management, DLP Policy, JSON Schema, REST API

---

## ✅ Pre-Launch Checklist

Before deploying to production:

- [ ] Flow built following guide exactly
- [ ] All 7 actions configured correctly
- [ ] SharePoint lists created with correct columns
- [ ] Test staff ID added to AUX_Platform_PINs
- [ ] Tested with valid PIN (success case)
- [ ] Tested with invalid PIN (error case)
- [ ] HTML form integrated and tested
- [ ] HTTP URL updated in form
- [ ] Content-Type headers on both responses
- [ ] Flow turned ON
- [ ] Permissions verified
- [ ] Documentation reviewed by team
- [ ] Backup export created
- [ ] Monitoring alerts configured
- [ ] User training completed

---

## 📧 Contact

**Project:** FAB AUX Platform
**Department:** Real-Time Management (RTM)
**Organization:** First Abu Dhabi Bank
**Documentation Version:** 1.0
**Last Updated:** 2026-01-23

---

*This documentation is part of the FAB digital transformation initiative for contact center operations.*

---

## 📚 Additional Resources in This Repository

This Power Automate documentation is part of a larger repository containing system prompts and configurations for various AI tools and platforms. Explore other directories for more technical documentation.

**Repository:** system-prompts-and-models-of-ai-tools
**License:** See repository root for licensing information

---

**End of README**
