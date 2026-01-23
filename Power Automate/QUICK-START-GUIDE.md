# 🚀 Quick Start Guide - Building Your Flow NOW

## What You'll Do

Build the FAB AUX Platform Power Automate flow in **7 actions**, step by step, from scratch.

**Time needed:** 45-60 minutes

---

## ✅ Before You Start - Checklist

- [ ] You have Power Automate Premium or per-user license
- [ ] You have access to https://bankfab.sharepoint.com/sites/FABInbound
- [ ] SharePoint lists exist (AUX_Platform_PINs and AUX_Adjustments_2026)
- [ ] You've read the DLP warning (HTTP trigger must be "Only users in my tenant")
- [ ] You have [DLP Pre-Flight Checklist](DLP-PreFlight-Checklist.md) printed or open

---

## 📋 Phase 1: Preparation (10 minutes)

### Step 1: Verify SharePoint Lists

1. **Go to:** https://bankfab.sharepoint.com/sites/FABInbound
2. Click **Site contents** (left sidebar)
3. **Verify these lists exist:**
   - ✅ AUX_Platform_PINs
   - ✅ AUX_Adjustments_2026

**If lists don't exist:** You need to create them first (see main guide for column details)

---

### Step 2: Add Test Data

**In AUX_Platform_PINs list:**

1. Click **+ New**
2. Enter:
   - **StaffID:** `TEST001`
   - **PINHash:** `03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4`
   - (This is the hash for PIN "1234")
3. Click **Save**

**This test data will be used later to test your flow.**

---

### Step 3: Open Supporting Documents

Keep these open in browser tabs:
- This Quick Start Guide
- [DLP Pre-Flight Checklist](DLP-PreFlight-Checklist.md)
- [JSON Schema Reference](JSON-Schema-Reference.md) (for copy-paste)

---

## 🛠️ Phase 2: Build the Flow (45 minutes)

### Step 4: Open Power Automate

1. **Go to:** https://make.powerautomate.com
2. **Sign in** with FAB credentials
3. **Check environment** (top-right corner)
   - Make sure you're in your FAB organization environment
   - NOT "Personal" or "Default"

---

### Step 5: Create New Flow

1. Click **+ Create** (left sidebar)
2. Select **Automated cloud flow**
3. Dialog appears → Click **Skip**
4. You'll see a blank flow designer

---

### Step 6: Add Action #1 - HTTP Trigger

**⚠️ THIS IS THE MOST CRITICAL STEP FOR DLP COMPLIANCE**

#### Add the Trigger:

1. You see "Choose an operation"
2. Search for: `http request`
3. Click **Triggers** tab
4. Select: **When a HTTP request is received**

#### Configure the Trigger:

**A. Set HTTP Method:**

1. Click **Show advanced options** (at bottom of trigger)
2. Find **Method** field
3. Select: **POST**

**B. Set Trigger Permission (🚨 CRITICAL!):**

1. Look for: **"Who can trigger the flow"**
2. **⚠️ STOP AND READ:**
   ```
   ✅ You MUST select: "Only users in my tenant"
   ❌ NEVER select: "Anyone"

   If you select "Anyone", your flow WILL be blocked by IT!
   ```
3. Click the dropdown
4. Select: **Only users in my tenant**
5. **Double-check:** It says "Only users in my tenant"

**C. Add JSON Schema:**

1. Click in **Request Body JSON Schema** field
2. Copy and paste this entire schema:

```json
{
  "type": "object",
  "properties": {
    "staffId": {
      "type": "string"
    },
    "staffName": {
      "type": "string"
    },
    "lineManager": {
      "type": "string"
    },
    "auxType": {
      "type": "string"
    },
    "category": {
      "type": "string"
    },
    "systemIssue": {
      "type": "string"
    },
    "fromTime": {
      "type": "string"
    },
    "toTime": {
      "type": "string"
    },
    "duration": {
      "type": "number"
    },
    "ticketNumber": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "wfoWfh": {
      "type": "string"
    },
    "comments": {
      "type": "string"
    },
    "approver": {
      "type": "string"
    },
    "submittedBy": {
      "type": "string"
    },
    "ipAddress": {
      "type": "string"
    },
    "deviceInfo": {
      "type": "string"
    },
    "pinHash": {
      "type": "string"
    }
  }
}
```

3. The schema should now be in the field

**✅ Checkpoint:** Your trigger should look like this:
- Method: POST
- Who can trigger: Only users in my tenant
- Request Body JSON Schema: ✓ Configured

---

### Step 7: Add Action #2 - Parse JSON

1. Click **+ New step** (below the HTTP trigger)
2. Search for: `parse json`
3. Select: **Parse JSON** (Data Operations)

#### Configure Parse JSON:

**A. Set Content:**

1. Click in **Content** field
2. Dynamic content panel opens on the right
3. Look for section: **"When a HTTP request is received"**
4. Click on: **Body**
5. "Body" should now appear in the Content field

**B. Set Schema:**

1. Click in **Schema** field
2. Paste the **SAME schema** as Step 6C (the 18-property JSON schema)
3. Schema should now be populated

**✅ Checkpoint:**
- Content: Shows "Body" (from HTTP trigger)
- Schema: Matches HTTP trigger schema

---

### Step 8: Add Action #3 - Get PINs from SharePoint

1. Click **+ New step**
2. Search for: `sharepoint get items`
3. Select: **Get items** (SharePoint connector)

#### Configure Get items:

**A. Rename the action (Important!):**

1. Click three dots (⋯) at top-right of action
2. Select **Rename**
3. Type: `Get PINs`
4. Press Enter
5. **This exact name matters for Step 10**

**B. Set Site Address:**

1. Click **Site Address** field
2. You'll see a dropdown of recent sites
3. If you see "FABInbound", click it
4. If not, select **Enter custom value**
5. Type: `https://bankfab.sharepoint.com/sites/FABInbound`

**C. Set List Name:**

1. Click **List Name** dropdown
2. Select: **AUX_Platform_PINs**
3. (If you don't see it, verify the site address is correct)

**D. Set Filter Query:**

1. Click **Show advanced options** (at bottom)
2. Find **Filter Query** field
3. Build this filter:
   - Type: `StaffID eq '`
   - Click in the field to open dynamic content
   - Look for **Parse JSON** section
   - Click: **staffId**
   - Type: `'` (closing single quote)
4. Final result: `StaffID eq 'staffId'` (where staffId is blue/highlighted)

**E. Set Top Count:**

1. Still in advanced options
2. Find **Top Count** field
3. Type: `1`

**✅ Checkpoint:**
- Action name: "Get PINs"
- Site: FABInbound
- List: AUX_Platform_PINs
- Filter: StaffID eq 'staffId' (dynamic)
- Top Count: 1

---

### Step 9: Add Action #4 - Condition (PIN Validation)

1. Click **+ New step**
2. Search for: `condition`
3. Select: **Condition** (Control)

#### Configure Condition:

**A. Rename:**

1. Click three dots (⋯) → Rename
2. Type: `Check PIN Valid`
3. Press Enter

**B. Set Left Side (Expression):**

1. Click in the **left field** of the condition
2. At the top, switch from **Dynamic content** to **Expression** tab
3. Copy and paste this expression:
   ```
   body('Get_PINs')?['value']?[0]?['PINHash']
   ```
4. Click **OK**
5. The expression should now be in the left field

**Important:** The expression uses `Get_PINs` (with underscore). Power Automate automatically converts "Get PINs" to "Get_PINs" in expressions.

**C. Set Operator:**

1. Middle dropdown
2. Select: **is equal to**

**D. Set Right Side (Dynamic Content):**

1. Click in the **right field**
2. Switch to **Dynamic content** tab
3. Look for **Parse JSON** section
4. Click: **pinHash**

**✅ Checkpoint:**
- Left: Expression with body('Get_PINs')...
- Operator: is equal to
- Right: pinHash (from Parse JSON)

**Now you have two branches: "If yes" and "If no"**

---

### Step 10: Add Action #5 - Create AUX Record (in "If yes" branch)

**Work inside the "If yes" section:**

1. Click **Add an action** (inside If yes)
2. Search for: `sharepoint create item`
3. Select: **Create item** (SharePoint)

#### Configure Create item:

**A. Rename:**

1. Three dots (⋯) → Rename
2. Type: `Create AUX Record`

**B. Set Site Address:**

1. Select: `https://bankfab.sharepoint.com/sites/FABInbound`
2. (Same as before)

**C. Set List Name:**

1. Select: **AUX_Adjustments_2026**

**D. Fill Field Mappings:**

Once you select the list, fields appear. Fill them from **Parse JSON** dynamic content:

| SharePoint Field | Select from Parse JSON |
|-----------------|----------------------|
| **Title** | staffId |
| **StaffID** | staffId |
| **StaffName** | staffName |
| **LineManager** | lineManager |
| **AUXType** | auxType |
| **Category** | category |
| **Reason** | systemIssue |
| **FromTime** | fromTime |
| **ToTime** | toTime |
| **Duration** | duration |
| **ITTicket** | ticketNumber |
| **Location** | location |
| **WFO_WFH** | wfoWfh |
| **Remarks** | comments |
| **ApprovedBy** | approver |
| **SubmittedBy** | submittedBy |
| **Status** | Type manually: `Pending` |
| **SubmissionDate** | Use expression: `utcNow()` |

**For Status:**
- Click in Status field
- Type: `Pending` (manual text)

**For SubmissionDate:**
- Click in SubmissionDate field
- Switch to **Expression** tab
- Type: `utcNow()`
- Click **OK**

**✅ Checkpoint:** All 18 fields mapped

---

### Step 11: Add Action #6 - Success Response (still in "If yes" branch)

**Stay inside "If yes" branch, below Create AUX Record:**

1. Click **Add an action**
2. Search for: `response`
3. Select: **Response** (Request)

#### Configure Success Response:

**A. Rename:**

1. Three dots (⋯) → Rename
2. Type: `Success Response`

**B. Set Status Code:**

1. Click **Status Code** field
2. Type: `200`

**C. Add Header:**

1. Click **Add new item** under Headers section
2. **Key:** `Content-Type`
3. **Value:** `application/json`

**D. Set Body:**

1. Click in **Body** field
2. Paste this JSON:
   ```json
   {"success": true, "message": "AUX submitted successfully!"}
   ```

**✅ Checkpoint:**
- Status: 200
- Header: Content-Type = application/json
- Body: Success JSON

---

### Step 12: Add Action #7 - Error Response (in "If no" branch)

**Now work inside "If no" branch:**

1. Click **Add an action** (inside If no)
2. Search for: `response`
3. Select: **Response** (Request)

#### Configure Error Response:

**A. Rename:**

1. Three dots (⋯) → Rename
2. Type: `Invalid PIN Response`

**B. Set Status Code:**

1. Type: `401`

**C. Add Header:**

1. Click **Add new item**
2. **Key:** `Content-Type`
3. **Value:** `application/json`

**D. Set Body:**

1. Paste:
   ```json
   {"success": false, "message": "Invalid PIN. Please check and try again."}
   ```

**✅ Checkpoint:**
- Status: 401
- Header: Content-Type = application/json
- Body: Error JSON

---

### Step 13: CRITICAL - Verify HTTP Trigger ONE MORE TIME

**Before saving, go back and verify:**

1. Scroll to the top of the flow
2. Click on "When a HTTP request is received" trigger
3. **Verify:** "Who can trigger the flow" shows **"Only users in my tenant"**
4. **If it shows "Anyone":** CHANGE IT NOW to "Only users in my tenant"

---

### Step 14: Save the Flow

1. Click **Save** (top-right corner)
2. Wait for "Your flow is ready to go" message
3. If you see any errors → read the error and fix
4. If you see DLP warnings → check HTTP trigger permission

**✅ Flow should save successfully with no warnings**

---

### Step 15: Get the HTTP URL

1. After saving, go back to the HTTP trigger
2. You'll now see: **HTTP POST URL**
3. Click the **copy icon** to copy the URL
4. Save this URL somewhere secure (you'll need it for your HTML form)

The URL looks like:
```
https://prod-##.region.logic.azure.com:443/workflows/.../triggers/manual/...
```

---

## 🧪 Phase 3: Test the Flow (15 minutes)

### Step 16: Test with Valid PIN

**Use Postman or cURL to test:**

**cURL command:**
```bash
curl -X POST "YOUR_HTTP_URL_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "staffId": "TEST001",
    "staffName": "Test User",
    "lineManager": "Test Manager",
    "auxType": "AUX-1",
    "category": "System Issue",
    "systemIssue": "Test issue",
    "fromTime": "09:00",
    "toTime": "09:15",
    "duration": 15,
    "ticketNumber": "TEST123",
    "location": "Abu Dhabi",
    "wfoWfh": "WFO",
    "comments": "Test comment",
    "approver": "Test Approver",
    "submittedBy": "Test User",
    "ipAddress": "192.168.1.1",
    "deviceInfo": "Test Device",
    "pinHash": "03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4"
  }'
```

**Expected Response:**
```json
{"success": true, "message": "AUX submitted successfully!"}
```

**What to Check:**
1. Go to Power Automate → Your flow → Run history
2. Should see a successful run (green checkmark)
3. Go to SharePoint → AUX_Adjustments_2026 list
4. Should see new record for TEST001

---

### Step 17: Test with Invalid PIN

**cURL command with wrong PIN:**
```bash
curl -X POST "YOUR_HTTP_URL_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "staffId": "TEST001",
    "staffName": "Test User",
    "lineManager": "Test Manager",
    "auxType": "AUX-1",
    "category": "System Issue",
    "systemIssue": "Test",
    "fromTime": "09:00",
    "toTime": "09:15",
    "duration": 15,
    "ticketNumber": "",
    "location": "Abu Dhabi",
    "wfoWfh": "WFO",
    "comments": "Test",
    "approver": "Test Approver",
    "submittedBy": "Test User",
    "ipAddress": "192.168.1.1",
    "deviceInfo": "Test",
    "pinHash": "wronghash123"
  }'
```

**Expected Response:**
```json
{"success": false, "message": "Invalid PIN. Please check and try again."}
```

**What to Check:**
1. Should get HTTP 401 response
2. NO new record in SharePoint
3. Run history shows False branch executed

---

## ✅ Final Verification Checklist

- [ ] Flow saved without errors
- [ ] HTTP trigger shows "Only users in my tenant"
- [ ] No DLP warnings received
- [ ] Valid PIN test passed (200 response, record created)
- [ ] Invalid PIN test passed (401 response, no record)
- [ ] Run history shows successful runs
- [ ] SharePoint record has all fields populated
- [ ] HTTP URL copied and saved

---

## 🎉 You're Done!

Your flow is now ready to use. Next steps:

1. **Integrate with HTML form** - Use the HTTP URL in your form
2. **Add real agent PINs** to AUX_Platform_PINs list (hashed)
3. **Train users** on how to submit AUX adjustments
4. **Monitor run history** regularly for issues

---

## 🚨 If Something Goes Wrong

### Flow Won't Save
- **Check:** HTTP trigger permission (must be "Only users in my tenant")
- **Check:** JSON schemas are valid
- **Check:** SharePoint lists exist

### Flow Suspended by IT
- **Check:** HTTP trigger permission
- **Contact:** IT with flow name and DLP Compliance Guide
- **Reference:** [DLP-Compliance-Guide.md](DLP-Compliance-Guide.md)

### Test Doesn't Work
- **Check:** Test data exists in AUX_Platform_PINs
- **Check:** HTTP URL is correct
- **Check:** JSON payload is valid
- **Reference:** [Quick-Troubleshooting-Guide.md](Quick-Troubleshooting-Guide.md)

---

## 📚 Reference Documents

- **Full Guide:** [FAB-AUX-Platform-Flow-Guide.md](FAB-AUX-Platform-Flow-Guide.md)
- **DLP Guide:** [DLP-Compliance-Guide.md](DLP-Compliance-Guide.md)
- **JSON Schemas:** [JSON-Schema-Reference.md](JSON-Schema-Reference.md)
- **Troubleshooting:** [Quick-Troubleshooting-Guide.md](Quick-Troubleshooting-Guide.md)
- **DLP Checklist:** [DLP-PreFlight-Checklist.md](DLP-PreFlight-Checklist.md)

---

## ⏱️ Time Breakdown

- **Preparation:** 10 minutes
- **Building:** 45 minutes
- **Testing:** 15 minutes
- **Total:** ~70 minutes

---

**Document Version:** 1.0
**Last Updated:** 2026-01-23
**Purpose:** Quick start guide for first-time builders

**End of Quick Start Guide**
