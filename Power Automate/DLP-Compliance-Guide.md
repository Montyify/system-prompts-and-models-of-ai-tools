# ⚠️ DLP Compliance Guide - FAB AUX Platform Flow

## 🚨 CRITICAL: READ THIS BEFORE BUILDING THE FLOW

This guide is specifically designed to help you avoid **Data Loss Prevention (DLP) policy violations** when building the FAB AUX Platform flow. DLP violations can prevent your flow from running or get it suspended by IT.

---

## 📋 Table of Contents

- [Understanding DLP in Power Automate](#understanding-dlp-in-power-automate)
- [Common DLP Violations](#common-dlp-violations)
- [Critical Settings to Avoid DLP Issues](#critical-settings-to-avoid-dlp-issues)
- [DLP Pre-Flight Checklist](#dlp-pre-flight-checklist)
- [Verifying DLP Compliance](#verifying-dlp-compliance)
- [What to Do If DLP Blocks Your Flow](#what-to-do-if-dlp-blocks-your-flow)
- [Working with IT Department](#working-with-it-department)
- [Alternative Approaches](#alternative-approaches)

---

## Understanding DLP in Power Automate

### What is DLP?

**Data Loss Prevention (DLP)** policies are organizational rules that control:
- Which connectors can be used together
- Who can trigger flows
- What data can flow between systems
- External data sharing permissions

### Why Organizations Use DLP

Banks like FAB implement strict DLP policies to:
- ✅ Prevent sensitive data from leaving the organization
- ✅ Control external API access
- ✅ Ensure compliance with banking regulations
- ✅ Protect customer information
- ✅ Prevent unauthorized data sharing

### How DLP Affects Power Automate

DLP policies can:
- ❌ Block certain connector combinations
- ❌ Prevent flows from saving
- ❌ Suspend flows automatically
- ❌ Restrict HTTP trigger permissions
- ❌ Block external endpoints
- ❌ Prevent premium connector usage

---

## Common DLP Violations

### 🚨 VIOLATION #1: HTTP Trigger Set to "Anyone"

**MOST COMMON ISSUE - THIS IS LIKELY WHAT CAUSED YOUR PREVIOUS PROBLEM**

#### ❌ WRONG Configuration:
```
When a HTTP request is received
  Method: POST
  Who can trigger the flow: Anyone ← THIS CAUSES DLP VIOLATION!
```

**Why This Is Blocked:**
- "Anyone" allows external/unauthenticated access
- Creates security risk for banking data
- Violates data protection policies
- IT security flags this immediately

#### ✅ CORRECT Configuration:
```
When a HTTP request is received
  Method: POST
  Who can trigger the flow: Only users in my tenant ← MUST USE THIS!
```

**Why This Works:**
- Requires FAB authentication
- Keeps data within organizational boundary
- Complies with security policies
- IT security approves this setting

---

### 🚨 VIOLATION #2: Blocked Connector Combinations

Some organizations block certain connector combinations:

#### Common Blocked Combinations:
- ❌ HTTP (Premium) + SharePoint (if in different data groups)
- ❌ HTTP + External databases
- ❌ HTTP + Email (in some policies)
- ❌ HTTP + SQL Server
- ❌ Any premium connector + non-business connectors

#### Our Flow Uses:
- ✅ HTTP Request (Premium) connector
- ✅ SharePoint connector
- ✅ Data Operations (Parse JSON)
- ✅ Control (Condition)
- ✅ Request (Response)

**These should be allowed when using "Only users in my tenant"**

---

### 🚨 VIOLATION #3: External Data Endpoints

#### ❌ BLOCKED:
- Public HTTP endpoints
- External APIs
- Third-party services
- Anonymous webhook URLs
- Endpoints outside tenant

#### ✅ ALLOWED:
- Internal SharePoint
- Internal applications (with authentication)
- Same-tenant services
- Authenticated endpoints
- HTML forms hosted on internal network

---

### 🚨 VIOLATION #4: Premium Connector Without License

#### ❌ BLOCKED:
- Using HTTP connector without Premium license
- Using premium features on free plan

#### ✅ ALLOWED:
- HTTP connector with Power Automate Premium license
- HTTP connector with Power Automate per-user license
- Verify you have proper licensing

---

## Critical Settings to Avoid DLP Issues

### ⚠️ MANDATORY SETTINGS - DO NOT SKIP

#### 1. HTTP Trigger Configuration

**Step-by-Step:**

1. Add "When a HTTP request is received" trigger
2. **IMMEDIATELY** configure permission:
   - Click **Show advanced options**
   - Find **"Who can trigger the flow"**
   - Select: **"Only users in my tenant"**
   - ⚠️ DO NOT select "Anyone"
   - ⚠️ DO NOT select "Specific users in my organization" (may cause issues)

3. Verify setting is saved:
   - Save the flow
   - Reopen the trigger
   - Confirm "Only users in my tenant" is still selected

**Visual Verification:**
```
✅ Correct:
┌─────────────────────────────────────┐
│ When a HTTP request is received     │
│ Method: POST                        │
│ Who can trigger: Only users in my   │
│                  tenant             │  ← THIS IS CRITICAL
└─────────────────────────────────────┘

❌ Wrong:
┌─────────────────────────────────────┐
│ When a HTTP request is received     │
│ Method: POST                        │
│ Who can trigger: Anyone             │  ← CAUSES DLP VIOLATION!
└─────────────────────────────────────┘
```

---

#### 2. SharePoint Connection Configuration

**Use Organizational Connection:**

1. When adding SharePoint actions, use your FAB account
2. Do NOT use personal Microsoft account
3. Do NOT use external SharePoint sites
4. Only use: `https://bankfab.sharepoint.com/sites/FABInbound`

**Connection Settings:**
- ✅ Sign in with: `yourname@bankfab.com` (or your FAB email)
- ✅ Use: Organizational account
- ❌ Do NOT use: Personal Microsoft account
- ❌ Do NOT use: External guest account

---

#### 3. Response Actions Configuration

Both Response actions should use:
- ✅ Content-Type: application/json
- ✅ Standard HTTP status codes (200, 401)
- ✅ Simple JSON responses (no external URLs)
- ❌ Do NOT include external links in responses
- ❌ Do NOT redirect to external sites

---

#### 4. Data Handling

**Within Flow:**
- ✅ Data stays between HTTP → SharePoint
- ✅ No external API calls
- ✅ No external database connections
- ✅ No email to external addresses
- ✅ All data remains in FAB tenant

**Do NOT add these (will trigger DLP):**
- ❌ Send to external APIs
- ❌ Email to non-FAB addresses
- ❌ Store in external databases
- ❌ Post to external webhooks
- ❌ Any connector that moves data outside tenant

---

## DLP Pre-Flight Checklist

### ✅ Before Saving the Flow

Use this checklist BEFORE clicking Save:

#### HTTP Trigger
- [ ] Method is POST
- [ ] "Who can trigger the flow" is set to **"Only users in my tenant"**
- [ ] Request Body JSON Schema is configured (18 properties)
- [ ] No external URLs in configuration

#### SharePoint Actions
- [ ] Using FAB SharePoint site: `https://bankfab.sharepoint.com/sites/FABInbound`
- [ ] Connected with FAB organizational account
- [ ] Both lists exist: AUX_Platform_PINs and AUX_Adjustments_2026
- [ ] No external SharePoint sites referenced

#### Data Flow
- [ ] Data flows only: HTTP → Parse JSON → SharePoint → Response
- [ ] No external connectors added
- [ ] No email actions to external addresses
- [ ] No API calls to external services
- [ ] No data export to external systems

#### Licensing
- [ ] You have Power Automate Premium OR per-user license
- [ ] License is active and not expired
- [ ] Using organizational/work license (not personal)

#### Environment
- [ ] Building in correct environment (FAB production or approved environment)
- [ ] Not using personal/trial environment
- [ ] Flow owner has proper permissions

---

### ✅ After Saving the Flow

- [ ] Flow saved without errors
- [ ] No DLP warning messages appeared
- [ ] Flow status shows "On" (not suspended)
- [ ] HTTP URL was generated successfully
- [ ] Can see the flow in "My flows" list
- [ ] No email from IT about policy violation

---

## Verifying DLP Compliance

### Method 1: Check Flow Properties

1. Open your flow
2. Click on the three dots (⋯) → Properties
3. Look for any warnings or policy messages
4. Check "Connection References" section:
   - All connections should show as "Connected"
   - No warning icons
   - No "Blocked" status

### Method 2: Check DLP Impact

1. Go to Power Automate portal
2. Click on your flow
3. Look for banner messages at the top:
   - ✅ No banner = Compliant
   - ⚠️ Yellow banner = Warning (review settings)
   - ❌ Red banner = Violation (flow blocked)

### Method 3: Test the Flow

1. Save the flow
2. Turn it ON
3. Send a test HTTP request
4. Check run history:
   - ✅ Run appears in history = Working
   - ❌ No run appears = May be blocked by DLP

### Method 4: Check with IT (Recommended)

**Before full deployment:**

1. Build the flow with correct settings
2. Do NOT turn it on yet
3. Send flow details to IT:
   - Flow name: "FAB AUX Platform - Submit"
   - Connectors used: HTTP Request, SharePoint, Data Operations, Control, Request
   - Purpose: Internal AUX adjustment processing
   - Data classification: Internal FAB data only
   - HTTP trigger permission: "Only users in my tenant"
4. Wait for IT approval
5. Turn on flow only after approval

---

## What to Do If DLP Blocks Your Flow

### Scenario 1: Flow Won't Save

**Error Message:** "This flow violates DLP policy" or similar

**Solution:**
1. ✅ Check HTTP trigger → Change to "Only users in my tenant"
2. ✅ Remove any external connectors
3. ✅ Ensure SharePoint connection uses FAB account
4. ✅ Try saving again

---

### Scenario 2: Flow Suspended After Saving

**Symptoms:**
- Flow shows "Suspended" status
- Email from IT about policy violation
- Flow won't turn on

**Solution:**
1. ✅ Do NOT try to force it on
2. ✅ Open flow and review settings
3. ✅ Check HTTP trigger permission
4. ✅ Contact IT with flow ID
5. ✅ Wait for IT guidance before modifying

---

### Scenario 3: Flow Runs But Gets Blocked Later

**Symptoms:**
- Flow worked initially
- Stopped working after a few runs
- "Access denied" or "Forbidden" errors

**Solution:**
1. ✅ Check if DLP policy changed recently
2. ✅ Verify all connections are still active
3. ✅ Review run history for specific error
4. ✅ Contact IT with error details
5. ✅ May need to re-authenticate connections

---

### Scenario 4: HTTP Requests Timing Out

**Symptoms:**
- Sending request but no response
- Form shows network error
- No runs in flow history

**Possible Causes:**
- DLP blocking external access
- Network/firewall blocking the HTTP URL
- Authentication required but not provided

**Solution:**
1. ✅ Test from within FAB network (not external)
2. ✅ Ensure user is signed into FAB account in browser
3. ✅ Verify HTTP trigger permission is correct
4. ✅ Test with Postman from FAB computer
5. ✅ Contact IT if still blocked

---

## Working with IT Department

### How to Present Your Flow to IT

**Email Template to IT:**

```
Subject: Power Automate Flow Review Request - AUX Adjustment Platform

Dear IT Team,

I am building a Power Automate cloud flow to digitize our AUX adjustment
process for the contact center (315+ agents). I want to ensure full DLP
compliance before deployment.

Flow Details:
- Name: FAB AUX Platform - Submit
- Purpose: Process AUX adjustment requests from internal HTML form
- Data Flow: HTML Form → Power Automate → SharePoint (FABInbound site)

Connectors Used:
1. HTTP Request (Premium) - Trigger permission: "Only users in my tenant"
2. SharePoint - Site: https://bankfab.sharepoint.com/sites/FABInbound
3. Data Operations (Parse JSON)
4. Control (Condition)
5. Request (Response)

Security Features:
- PIN-based authentication (SHA-256 hashed)
- All data remains within FAB tenant
- No external API calls
- No data export outside organization
- IP address and device tracking for audit

DLP Compliance:
- HTTP trigger set to "Only users in my tenant" (NOT "Anyone")
- No external connectors
- No cross-tenant data sharing
- Internal SharePoint only

Licensing:
- I have Power Automate [Premium/Per-user] license

Questions:
1. Are these connector combinations allowed under current DLP policy?
2. Is there anything else I should configure for compliance?
3. Do I need specific approval before turning on this flow?

Please let me know if you need any additional information or if I should
schedule a meeting to discuss.

Thank you,
[Your Name]
RTM Officer
```

---

### Information IT May Request

Be prepared to provide:
1. **Flow export** (as package)
2. **List of connectors** used
3. **Data flow diagram** (HTTP → SharePoint)
4. **SharePoint site URL** and list names
5. **Business justification** (digitizing manual process)
6. **Data classification** (internal operational data)
7. **User authentication method** (PIN-based)
8. **Audit trail** (IP, device info logged)

---

## Alternative Approaches

### If Standard HTTP Trigger Is Blocked

#### Option 1: Power Apps Front-End
Instead of HTML form:
- Build Power Apps form
- Directly trigger Power Automate from Power Apps
- No HTTP trigger needed
- Better DLP compliance

**Pros:**
- ✅ Native Power Platform integration
- ✅ Better DLP compliance
- ✅ No HTTP trigger permission issues

**Cons:**
- ❌ Requires Power Apps license
- ❌ More complex to build
- ❌ Users need Power Apps access

---

#### Option 2: SharePoint Form
Instead of HTML form:
- Create Power Apps form embedded in SharePoint
- Use SharePoint form → Flow trigger
- "When an item is created" trigger

**Pros:**
- ✅ No HTTP trigger needed
- ✅ Native SharePoint integration
- ✅ Better DLP compliance

**Cons:**
- ❌ Users need SharePoint access
- ❌ Different workflow
- ❌ May need form customization

---

#### Option 3: Microsoft Forms + Flow
Instead of HTML form:
- Use Microsoft Forms for data collection
- Trigger: "When a new response is submitted"
- Validate PIN in flow logic

**Pros:**
- ✅ No HTTP trigger
- ✅ Easy to build form
- ✅ Good DLP compliance
- ✅ No coding needed

**Cons:**
- ❌ No real-time PIN validation
- ❌ Less control over form design
- ❌ May need post-submission validation

---

#### Option 4: Request IT to Whitelist
If HTTP trigger is needed:
- Submit formal request to IT
- Explain business need
- Provide security details
- Request exception to DLP policy

**Process:**
1. Document business need
2. Show security measures (PIN auth, audit trail)
3. Prove data stays within tenant
4. Request specific approval
5. Wait for policy exception

---

## Best Practices for DLP Compliance

### 1. Design with DLP in Mind

**Before Building:**
- ✅ Check what connectors are allowed
- ✅ Verify licensing
- ✅ Understand data classification
- ✅ Plan data flow to stay internal

---

### 2. Use Minimal Connectors

**Only Use What's Needed:**
- ✅ Fewer connectors = Lower DLP risk
- ✅ Avoid "nice to have" features if they add connectors
- ✅ Keep it simple

---

### 3. Document Everything

**Maintain Records:**
- ✅ Flow purpose and business justification
- ✅ Data flow diagram
- ✅ Connector list with reasons
- ✅ Security measures implemented
- ✅ DLP compliance verification

---

### 4. Regular Compliance Checks

**Monthly:**
- ✅ Verify flow still compliant
- ✅ Check for new DLP policies
- ✅ Review connection status
- ✅ Monitor for warnings

---

### 5. Communicate with IT

**Maintain Relationship:**
- ✅ Inform IT of major changes
- ✅ Report issues immediately
- ✅ Follow IT guidance
- ✅ Document approvals

---

## DLP Policy Changes

### What to Do When IT Updates DLP Policies

1. **Check Email:**
   - IT usually announces policy changes
   - Read change notifications carefully

2. **Review Your Flows:**
   - Check if your flow is affected
   - Look for warning messages
   - Test flow functionality

3. **Update if Needed:**
   - Modify settings to comply with new policy
   - Re-test thoroughly
   - Document changes

4. **Contact IT if Blocked:**
   - Don't try to circumvent policies
   - Explain business impact
   - Request guidance or exception

---

## Red Flags That Will Trigger DLP Review

### 🚨 Immediate IT Attention

These will get flagged:
- ❌ HTTP trigger set to "Anyone"
- ❌ External API calls
- ❌ Email to external domains
- ❌ Data export to external databases
- ❌ Cross-tenant data sharing
- ❌ Premium connector on free license
- ❌ Suspicious data volumes
- ❌ External webhook URLs

---

## Success Indicators

### ✅ Your Flow is DLP Compliant When:

1. **It saves without warnings**
   - No error messages
   - No policy warnings
   - No confirmation dialogs about policy

2. **It runs successfully**
   - Runs appear in history
   - No "blocked" messages
   - Actions complete normally

3. **IT hasn't contacted you**
   - No emails about violations
   - No suspension notices
   - No audit inquiries

4. **Flow stays on**
   - Doesn't get auto-suspended
   - Runs continuously without issues
   - No intermittent failures

5. **All connections active**
   - Green checkmarks on connections
   - No re-authentication required
   - No "connection broken" errors

---

## Final DLP Compliance Summary

### 🎯 The Golden Rules

1. **ALWAYS** set HTTP trigger to "Only users in my tenant"
2. **NEVER** use "Anyone" trigger permission
3. **ONLY** use connectors approved by IT
4. **KEEP** data within FAB tenant
5. **VERIFY** settings before saving
6. **TEST** in compliance with policies
7. **COMMUNICATE** with IT department

---

### 🔒 Your Flow is Safe When:

```
✅ HTTP Trigger: "Only users in my tenant"
✅ SharePoint: bankfab.sharepoint.com only
✅ No external connectors
✅ Data stays internal
✅ Proper licensing
✅ IT aware/approved
✅ No warning messages
✅ Flow runs successfully
```

---

## Emergency Contact

### If You Get DLP Violation Notice:

1. **STOP** - Don't modify the flow immediately
2. **READ** - Understand the exact violation
3. **CHECK** - Review this guide's solutions
4. **FIX** - Apply correct settings (usually HTTP trigger)
5. **TEST** - Verify it works
6. **DOCUMENT** - Record what was wrong and fix applied

### If Still Blocked:

**Contact IT with:**
- Flow name and ID
- Error message (screenshot)
- What you've tried
- Business urgency
- This DLP compliance document

---

## Additional Resources

### Internal FAB Resources
- IT Service Desk
- Power Platform Admin
- Security Team
- Compliance Team

### Microsoft Documentation
- Power Automate DLP Policies: https://docs.microsoft.com/power-platform/admin/wp-data-loss-prevention
- HTTP Connector Best Practices: https://docs.microsoft.com/connectors/custom-connectors/

---

## Appendix: DLP Violation Examples

### Real-World Examples (What NOT to Do)

#### ❌ Example 1: Wrong HTTP Trigger Setting
```
Violation: HTTP trigger set to "Anyone"
Result: Flow suspended immediately
Fix: Change to "Only users in my tenant"
Time to fix: 2 minutes
```

#### ❌ Example 2: External API Call
```
Violation: Added HTTP action to call external API
Result: Flow blocked by DLP
Fix: Remove external HTTP action, keep data internal
Time to fix: 10 minutes
```

#### ❌ Example 3: External Email
```
Violation: Send email to gmail.com
Result: Flow suspended
Fix: Remove email action or send only to @bankfab.com
Time to fix: 5 minutes
```

---

**Document Version:** 1.0
**Last Updated:** 2026-01-23
**Related Documents:**
- [Main Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)
- [Quick Troubleshooting Guide](Quick-Troubleshooting-Guide.md)

---

## 📢 REMEMBER

### The #1 cause of DLP violations in Power Automate:

# ⚠️ HTTP TRIGGER SET TO "ANYONE" ⚠️

### Always use:
# ✅ "Only users in my tenant" ✅

---

*This guide is specifically written to prevent DLP issues based on real-world experience. Follow these guidelines carefully to avoid having your flow blocked by IT.*

**End of DLP Compliance Guide**
