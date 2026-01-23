# ✈️ DLP Pre-Flight Checklist
## FAB AUX Platform - Power Automate Flow

**Before saving and deploying your flow, complete this checklist to avoid DLP violations.**

Print this page and check off each item as you verify it.

---

## 🚨 CRITICAL ITEMS (MUST BE CORRECT)

### HTTP Trigger Configuration

- [ ] **HTTP Trigger added** ("When a HTTP request is received")
- [ ] **Method set to:** POST
- [ ] **"Who can trigger the flow" set to:** "Only users in my tenant"
- [ ] **VERIFIED: NOT set to "Anyone"** (double-check this!)
- [ ] **Request Body JSON Schema configured** (18 properties)

**❌ IF "Who can trigger" is set to "Anyone" → STOP AND CHANGE IT NOW**

---

## ✅ CONNECTOR COMPLIANCE

### Connectors Used (Check each one exists and is configured correctly)

- [ ] **HTTP Request** (Request connector - for trigger only)
- [ ] **Parse JSON** (Data Operations)
- [ ] **SharePoint - Get items** (for Get PINs action)
- [ ] **SharePoint - Create item** (for Create AUX Record action)
- [ ] **Control - Condition** (for PIN validation)
- [ ] **Request - Response** (for Success Response - HTTP 200)
- [ ] **Request - Response** (for Invalid PIN Response - HTTP 401)

**Total connectors: 7 actions (1 trigger + 6 actions)**

### No Blocked Connectors Added

- [ ] **No external HTTP actions** (calling external APIs)
- [ ] **No external database connections**
- [ ] **No email to external addresses**
- [ ] **No third-party connectors**
- [ ] **No custom connectors to external services**

---

## 📍 SHAREPOINT CONFIGURATION

### SharePoint Connection

- [ ] **Site URL is:** `https://bankfab.sharepoint.com/sites/FABInbound`
- [ ] **Connected with FAB organizational account** (yourname@bankfab.com)
- [ ] **NOT using personal Microsoft account**
- [ ] **NOT using external/guest account**

### SharePoint Lists

- [ ] **List 1 configured:** AUX_Platform_PINs
- [ ] **List 2 configured:** AUX_Adjustments_2026
- [ ] **Both lists exist in SharePoint**
- [ ] **Permissions verified** (you have Edit access)

---

## 🔒 DATA FLOW COMPLIANCE

### Internal Data Only

- [ ] **Data flows:** HTML Form → HTTP → Parse JSON → SharePoint → Response
- [ ] **No data sent outside FAB tenant**
- [ ] **No external API calls**
- [ ] **No data export to external systems**
- [ ] **All data remains internal**

---

## 🎫 LICENSING & ENVIRONMENT

### License Verification

- [ ] **Power Automate Premium** OR **Power Automate per-user license** (confirmed)
- [ ] **License is active** (not trial or expired)
- [ ] **Using organizational/work license** (not personal)

### Environment

- [ ] **Building in correct FAB environment** (not personal/trial)
- [ ] **Environment has proper permissions**
- [ ] **Flow owner has necessary access**

---

## 🔍 PRE-SAVE VERIFICATION

### Before Clicking "Save"

- [ ] **All 7 actions configured correctly**
- [ ] **HTTP trigger permission: "Only users in my tenant"** (verify again!)
- [ ] **SharePoint site URL correct**
- [ ] **No warning messages visible**
- [ ] **No external connectors present**

### Visual Check

- [ ] **Flow name:** FAB AUX Platform - Submit
- [ ] **Flow type:** Cloud flow
- [ ] **Trigger:** When a HTTP request is received
- [ ] **Actions count:** 6 actions after trigger

---

## 💾 POST-SAVE VERIFICATION

### After Clicking "Save"

- [ ] **Flow saved successfully** (no error messages)
- [ ] **No DLP warning messages appeared**
- [ ] **No policy violation alerts**
- [ ] **HTTP POST URL generated** (visible in trigger)
- [ ] **Flow status shows "Off"** (ready to turn on)

---

## 🔄 BEFORE TURNING ON

### Final Checks

- [ ] **Test data prepared** (test staff ID, PIN hash)
- [ ] **Test record added to AUX_Platform_PINs**
- [ ] **Ready to test with Postman/cURL**
- [ ] **HTML form ready** (if applicable)

### IT Communication (Recommended)

- [ ] **IT department notified** (optional but recommended)
- [ ] **Business justification documented**
- [ ] **Security measures explained** (PIN auth, audit trail)
- [ ] **Approval received** (if required by policy)

---

## ✅ DEPLOYMENT CHECKLIST

### Ready to Deploy When:

- [ ] **All above items checked**
- [ ] **Test completed successfully** (with valid & invalid PIN)
- [ ] **Record created in SharePoint** (verified)
- [ ] **Responses working** (200 for success, 401 for invalid PIN)
- [ ] **No errors in run history**
- [ ] **No DLP warnings received**
- [ ] **Flow runs consistently** (multiple tests passed)

---

## 🚨 RED FLAGS - STOP IF YOU SEE:

**DO NOT PROCEED if you see any of these:**

- ❌ Error: "This flow violates DLP policy"
- ❌ Banner: "This flow may be blocked by DLP"
- ❌ HTTP trigger shows "Anyone" instead of "Only users in my tenant"
- ❌ Warning: "Connector combination not allowed"
- ❌ Flow status: "Suspended"
- ❌ Email from IT about policy violation
- ❌ Cannot save the flow
- ❌ External connectors present

**If you see any red flag → STOP and review [DLP-Compliance-Guide.md](DLP-Compliance-Guide.md)**

---

## 📞 EMERGENCY CONTACTS

### If DLP Issue Occurs:

1. **DO NOT modify the flow randomly**
2. **CHECK:** HTTP trigger permission setting first
3. **REVIEW:** [DLP-Compliance-Guide.md](DLP-Compliance-Guide.md)
4. **CONTACT:** IT Service Desk with flow name and error message
5. **PROVIDE:** This checklist showing what you verified

---

## 📝 DEPLOYMENT SIGN-OFF

**Complete this section when deploying to production:**

| Item | Details |
|------|---------|
| **Flow Name** | FAB AUX Platform - Submit |
| **Deployed By** | _________________________ |
| **Date** | _________________________ |
| **Environment** | _________________________ |
| **HTTP URL** | (Keep secure - don't write here) |
| **IT Ticket #** | _________________________ |
| **All checks passed?** | YES ☐  NO ☐ |

**Signature:** _________________________  **Date:** _____________

---

## 🎯 THE GOLDEN RULE

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│  The #1 cause of DLP violations:                   │
│                                                     │
│  ❌ HTTP Trigger set to "Anyone"                    │
│                                                     │
│  The solution:                                      │
│                                                     │
│  ✅ Always use "Only users in my tenant"           │
│                                                     │
│  Check this THREE times before saving:             │
│  1. When adding the trigger                        │
│  2. Before saving the flow                         │
│  3. After saving (reopen and verify)               │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## 📚 RELATED DOCUMENTS

- **Complete DLP Guide:** [DLP-Compliance-Guide.md](DLP-Compliance-Guide.md)
- **Flow Building Guide:** [FAB-AUX-Platform-Flow-Guide.md](FAB-AUX-Platform-Flow-Guide.md)
- **Troubleshooting:** [Quick-Troubleshooting-Guide.md](Quick-Troubleshooting-Guide.md)

---

**Document Version:** 1.0
**Last Updated:** 2026-01-23
**Purpose:** Pre-deployment DLP compliance verification

---

## ✅ COMPLETION STATUS

**All critical items checked?** YES ☐  NO ☐

**DLP compliance verified?** YES ☐  NO ☐

**Ready to deploy?** YES ☐  NO ☐

**If all YES → Proceed with deployment**
**If any NO → Review relevant guide and fix issues**

---

*Print this checklist and keep it with your flow documentation for future reference and audits.*

**End of DLP Pre-Flight Checklist**
