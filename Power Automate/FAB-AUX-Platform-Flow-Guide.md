# FAB AUX Platform - Power Automate Flow Building Guide

## 📋 Table of Contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Architecture Overview](#architecture-overview)
- [Step-by-Step Flow Building Instructions](#step-by-step-flow-building-instructions)
  - [Step 1: HTTP Trigger Configuration](#step-1-http-trigger-configuration)
  - [Step 2: Parse JSON Action](#step-2-parse-json-action)
  - [Step 3: Get PIN from SharePoint](#step-3-get-pin-from-sharepoint)
  - [Step 4: Validate PIN (Condition)](#step-4-validate-pin-condition)
  - [Step 5A: Create AUX Record (True Branch)](#step-5a-create-aux-record-true-branch)
  - [Step 5B: Success Response (True Branch)](#step-5b-success-response-true-branch)
  - [Step 6: Invalid PIN Response (False Branch)](#step-6-invalid-pin-response-false-branch)
- [Testing the Flow](#testing-the-flow)
- [Troubleshooting Guide](#troubleshooting-guide)
- [Security Considerations](#security-considerations)
- [Maintenance and Updates](#maintenance-and-updates)

---

## Overview

**Project:** FAB AUX Platform - Submit Flow
**Organization:** First Abu Dhabi Bank
**Purpose:** Digitize paper-based AUX adjustment process for 315+ agents
**Flow Type:** Cloud Flow (HTTP-triggered)
**Environment:** Power Automate Cloud (Web version)

### What This Flow Does
1. Receives AUX adjustment requests via HTTP POST from HTML form
2. Authenticates agent using SHA-256 hashed PIN
3. Validates PIN against stored values in SharePoint
4. Creates adjustment record in SharePoint if PIN is valid
5. Returns appropriate JSON response (success or error)

---

## Prerequisites

### Required Access
- ✅ Power Automate Premium or per-user license
- ✅ SharePoint access to `https://bankfab.sharepoint.com/sites/FABInbound`
- ✅ Permission to create cloud flows
- ✅ DLP policy compliance (using "Only users in my tenant" setting)

### SharePoint Lists Setup

#### List 1: AUX_Platform_PINs
**Purpose:** Store agent PIN hashes for authentication

**Required Columns:**
| Column Name | Type | Description |
|------------|------|-------------|
| Title | Single line of text | Auto-generated |
| StaffID | Single line of text | Agent staff ID (indexed for performance) |
| PINHash | Multiple lines of text | SHA-256 hashed PIN |
| CreatedDate | Date and time | Auto-generated |

**Important:** Create an index on the `StaffID` column for faster lookups:
- Go to List Settings → Indexed columns → Create a new index → Select StaffID

#### List 2: AUX_Adjustments_2026
**Purpose:** Store all AUX adjustment records

**Required Columns:**
| Column Name | Type | Description |
|------------|------|-------------|
| Title | Single line of text | Staff ID (will be auto-populated) |
| StaffID | Single line of text | Agent staff ID |
| StaffName | Single line of text | Agent full name |
| LineManager | Single line of text | Manager name |
| AUXType | Choice | AUX-1, AUX-2, AUX-3, AUX-4, AUX-5, AUX-6 |
| Category | Single line of text | Reason category |
| Reason | Multiple lines of text | System issue details |
| FromTime | Single line of text | Start time |
| ToTime | Single line of text | End time |
| Duration | Number | Duration in minutes |
| ITTicket | Single line of text | IT ticket number |
| Location | Choice | Abu Dhabi, Al Ain |
| WFO_WFH | Choice | WFO, WFH |
| Remarks | Multiple lines of text | Additional comments |
| ApprovedBy | Single line of text | Approver name |
| SubmittedBy | Single line of text | Submitter name |
| Status | Choice | Pending, Approved, Rejected |
| SubmissionDate | Date and time | Submission timestamp |

---

## Architecture Overview

```
┌─────────────────┐
│   HTML Form     │
│  (with PIN)     │
└────────┬────────┘
         │ HTTP POST
         │ (JSON payload)
         ▼
┌─────────────────────────────────────────────────────────┐
│              Power Automate Cloud Flow                   │
│                                                          │
│  1. HTTP Trigger (Receive request)                      │
│  2. Parse JSON (Extract form data)                      │
│  3. Get PINs from SharePoint (Lookup agent PIN)         │
│  4. Condition (Validate PIN hash)                       │
│     │                                                    │
│     ├─ TRUE ──► 5A. Create AUX Record in SharePoint    │
│     │           5B. Return Success (200)                │
│     │                                                    │
│     └─ FALSE ─► 6. Return Invalid PIN Error (401)      │
│                                                          │
└─────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│              SharePoint Lists                            │
│                                                          │
│  • AUX_Platform_PINs (authentication)                   │
│  • AUX_Adjustments_2026 (records)                       │
└─────────────────────────────────────────────────────────┘
```

---

## Step-by-Step Flow Building Instructions

### Getting Started

1. **Open Power Automate**
   - Navigate to https://make.powerautomate.com
   - Sign in with your FAB credentials
   - Ensure you're in the correct environment

2. **Create New Flow**
   - Click **+ Create** (left sidebar)
   - Select **Automated cloud flow**
   - Click **Skip** on the dialog (we'll add the trigger manually)

3. **Access Classic Designer**
   - Once the flow opens, ensure you're in the classic designer
   - If you see the Copilot interface, switch to classic view
   - Look for the traditional step-by-step action builder

---

### Step 1: HTTP Trigger Configuration

**Action to Add:** `When a HTTP request is received`

#### How to Add:
1. In the flow designer, click **+ New step**
2. Search for: `http request`
3. Select: **When a HTTP request is received** (Request connector)

#### Configuration:

**1. HTTP Method**
- Click **Show advanced options**
- Set **Method:** `POST`
- This ensures only POST requests are accepted

**2. Trigger Permission (CRITICAL for DLP compliance)**
- Look for **Who can trigger the flow**
- Select: **Only users in my tenant**
- ⚠️ **Warning:** Do NOT select "Anyone" as this will violate DLP policies

**3. Request Body JSON Schema**
Click on the **Request Body JSON Schema** field and paste:

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

#### After Configuration:
- Click **Save** (top-right)
- An HTTP POST URL will be generated
- You'll use this URL in your HTML form
- **Keep this URL secure** - it's the endpoint for your flow

#### Visual Check:
Your trigger should show:
```
When a HTTP request is received
  Method: POST
  Who can trigger: Only users in my tenant
  Request Body JSON Schema: ✓ Configured (18 properties)
```

---

### Step 2: Parse JSON Action

**Action to Add:** `Parse JSON`

#### Why We Need This:
- Extracts individual fields from the HTTP request body
- Makes data available for dynamic content selection
- Provides type safety and IntelliSense support

#### How to Add:
1. Click **+ New step** (below the HTTP trigger)
2. Search for: `parse json`
3. Select: **Parse JSON** (Data Operations)

#### Configuration:

**1. Content Field**
- Click in the **Content** field
- A dynamic content menu will appear
- Select: **Body** (from "When a HTTP request is received")
- ⚠️ **Important:** Select the entire Body, not individual properties

**2. Schema Field**
Click in the **Schema** field and paste the SAME schema as Step 1:

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

#### Visual Check:
Your Parse JSON action should show:
```
Parse JSON
  Content: Body (from HTTP trigger)
  Schema: ✓ Configured (18 properties)
```

#### Testing Dynamic Content:
- Click **+ New step** temporarily
- In the dynamic content menu, you should now see all 18 fields listed under "Parse JSON"
- This confirms the schema is working correctly
- Cancel adding the step (we'll add the real next step)

---

### Step 3: Get PIN from SharePoint

**Action to Add:** `Get items` (SharePoint)

#### Purpose:
Retrieve the stored PIN hash for the agent from SharePoint to validate against the submitted PIN.

#### How to Add:
1. Click **+ New step**
2. Search for: `sharepoint get items`
3. Select: **Get items** (SharePoint connector)

#### Configuration:

**1. Rename the Action (Important for Step 4)**
- Click on the three dots (⋯) at the top-right of the action
- Select **Rename**
- Name it: **Get PINs**
- ⚠️ **Critical:** This exact name will be used in the expression in Step 4

**2. Site Address**
- Click in the **Site Address** field
- Select: **Enter custom value**
- Paste: `https://bankfab.sharepoint.com/sites/FABInbound`
- OR search for "FABInbound" from the dropdown

**3. List Name**
- Click in the **List Name** field
- Select: **AUX_Platform_PINs**
- The list should appear in the dropdown after selecting the site

**4. Filter Query (Critical for Performance)**
- Click **Show advanced options**
- In the **Filter Query** field, you need to build an OData filter
- Click in the field to open dynamic content
- Build this filter: `StaffID eq 'STAFFID'`

**How to Build the Filter Query:**
1. Type: `StaffID eq '`
2. Click to open dynamic content
3. Select: **staffId** (from Parse JSON - NOT from Body)
4. Type: `'` (closing quote)

**Final Filter Query should look like:**
```
StaffID eq 'staffId'
```
Where `staffId` is highlighted in blue/purple (dynamic content)

**5. Top Count**
- Still in advanced options
- Set **Top Count:** `1`
- This limits results to 1 record (performance optimization)

**6. Other Settings**
- Leave all other fields empty/default

#### Visual Check:
```
Get PINs
  Site Address: https://bankfab.sharepoint.com/sites/FABInbound
  List Name: AUX_Platform_PINs
  Filter Query: StaffID eq 'staffId' (dynamic)
  Top Count: 1
```

#### Common Issues:
- **Error: "List not found"** → Verify site URL is correct and you have access
- **No filtering happening** → Ensure Filter Query uses single quotes around the dynamic content
- **Multiple results returned** → Set Top Count to 1

---

### Step 4: Validate PIN (Condition)

**Action to Add:** `Condition`

#### Purpose:
Compare the submitted PIN hash with the stored PIN hash to authenticate the agent.

#### How to Add:
1. Click **+ New step**
2. Search for: `condition`
3. Select: **Condition** (Control connector)

#### Configuration:

**1. Rename the Action**
- Click three dots (⋯) → Rename
- Name it: **Check PIN Valid**

**2. Build the Condition**

The condition has three parts: Left side, Operator, Right side

**Left Side (Stored PIN Hash from SharePoint):**
- Click in the left field
- Switch to **Expression** tab at the top
- Paste this expression:
```
body('Get_PINs')?['value']?[0]?['PINHash']
```

⚠️ **Important Notes:**
- The action name must match exactly: `Get_PINs` (underscores, not spaces)
- If you named it "Get PINs" (with space), the expression should be: `body('Get_PINs')?['value']?[0]?['PINHash']`
- Power Automate automatically converts spaces to underscores in action names
- The `?` operators provide null-safety
- `[0]` gets the first item from the array
- Click **OK** to confirm the expression

**Operator:**
- Select: **is equal to**

**Right Side (Submitted PIN Hash):**
- Click in the right field
- Switch to **Dynamic content** tab
- Select: **pinHash** (from Parse JSON)

#### Visual Check:
```
Check PIN Valid
  Condition:
    body('Get_PINs')?['value']?[0]?['PINHash']
    is equal to
    pinHash (from Parse JSON)
```

#### Understanding the Expression:
- `body('Get_PINs')` = Get the output from the "Get PINs" action
- `?['value']` = Access the value array (? handles null safely)
- `?[0]` = Get the first item in the array
- `?['PINHash']` = Get the PINHash property

#### Testing the Expression:
After saving, you can test the flow. If the expression is correct:
- **True branch** = PIN matches (authentication successful)
- **False branch** = PIN doesn't match OR no PIN found for staff ID

---

### Step 5A: Create AUX Record (True Branch)

**Action to Add:** `Create item` (SharePoint)

This goes in the **If yes** (True) branch of the condition.

#### How to Add:
1. Inside the **If yes** section, click **Add an action**
2. Search for: `sharepoint create item`
3. Select: **Create item** (SharePoint connector)

#### Configuration:

**1. Rename the Action**
- Click three dots (⋯) → Rename
- Name it: **Create AUX Record**

**2. Site Address**
- Select: `https://bankfab.sharepoint.com/sites/FABInbound`
- (Same as Step 3)

**3. List Name**
- Select: **AUX_Adjustments_2026**

**4. Field Mappings**

Once you select the list, all columns will appear. Fill them as follows:

| SharePoint Column | Value to Enter | How to Enter |
|------------------|---------------|--------------|
| **Title** | staffId | Dynamic content from Parse JSON |
| **StaffID** | staffId | Dynamic content from Parse JSON |
| **StaffName** | staffName | Dynamic content from Parse JSON |
| **LineManager** | lineManager | Dynamic content from Parse JSON |
| **AUXType** | auxType | Dynamic content from Parse JSON |
| **Category** | category | Dynamic content from Parse JSON |
| **Reason** | systemIssue | Dynamic content from Parse JSON |
| **FromTime** | fromTime | Dynamic content from Parse JSON |
| **ToTime** | toTime | Dynamic content from Parse JSON |
| **Duration** | duration | Dynamic content from Parse JSON |
| **ITTicket** | ticketNumber | Dynamic content from Parse JSON |
| **Location** | location | Dynamic content from Parse JSON |
| **WFO_WFH** | wfoWfh | Dynamic content from Parse JSON |
| **Remarks** | comments | Dynamic content from Parse JSON |
| **ApprovedBy** | approver | Dynamic content from Parse JSON |
| **SubmittedBy** | submittedBy | Dynamic content from Parse JSON |
| **Status** | Pending | Type manually: `Pending` |
| **SubmissionDate** | utcNow() | Expression (see below) |

**Detailed Steps for Each Field:**

For most fields (staffId, staffName, etc.):
1. Click in the field
2. Select from Dynamic content → Parse JSON section
3. Choose the matching field name

**For Status Field:**
- Type manually: `Pending`
- Do NOT use dynamic content - this is a fixed value

**For SubmissionDate Field:**
1. Click in the SubmissionDate field
2. Switch to **Expression** tab
3. Type: `utcNow()`
4. Click **OK**
- This inserts the current UTC timestamp

#### Important Notes:
- ⚠️ **All dynamic content must come from Parse JSON**, not from Body
- ⚠️ SharePoint column names are case-sensitive and must match exactly
- ⚠️ Choice columns (AUXType, Location, WFO_WFH, Status) must match the choices defined in SharePoint
- If a field is not required in SharePoint, you can leave it empty

#### Visual Check:
Your Create item action should show approximately 18 field mappings, with most coming from Parse JSON dynamic content, Status as "Pending", and SubmissionDate as utcNow() expression.

---

### Step 5B: Success Response (True Branch)

**Action to Add:** `Response`

This goes in the **If yes** (True) branch, AFTER the Create AUX Record action.

#### How to Add:
1. In the **If yes** branch, below "Create AUX Record", click **Add an action**
2. Search for: `response`
3. Select: **Response** (Request connector)

#### Configuration:

**1. Rename the Action**
- Click three dots (⋯) → Rename
- Name it: **Success Response**

**2. Status Code**
- Set: `200`
- This indicates successful processing

**3. Headers**
- Click **Add new item** under Headers
- **Key:** `Content-Type`
- **Value:** `application/json`

⚠️ **Critical:** The Content-Type header is required for proper JSON response handling

**4. Body**
Click in the Body field and paste:
```json
{
  "success": true,
  "message": "AUX submitted successfully!"
}
```

- Type this exactly as shown
- Do NOT use dynamic content - this is static JSON
- Ensure proper JSON formatting (double quotes, no trailing commas)

#### Visual Check:
```
Success Response
  Status Code: 200
  Headers:
    Content-Type: application/json
  Body: {"success": true, "message": "AUX submitted successfully!"}
```

#### What Happens:
When the PIN is valid and the record is created successfully, this response is sent back to the HTML form with HTTP 200 status, allowing the form to display a success message to the user.

---

### Step 6: Invalid PIN Response (False Branch)

**Action to Add:** `Response`

This goes in the **If no** (False) branch of the condition.

#### How to Add:
1. Inside the **If no** section, click **Add an action**
2. Search for: `response`
3. Select: **Response** (Request connector)

#### Configuration:

**1. Rename the Action**
- Click three dots (⋯) → Rename
- Name it: **Invalid PIN Response**

**2. Status Code**
- Set: `401`
- This indicates unauthorized access (authentication failure)

**3. Headers**
- Click **Add new item** under Headers
- **Key:** `Content-Type`
- **Value:** `application/json`

**4. Body**
Click in the Body field and paste:
```json
{
  "success": false,
  "message": "Invalid PIN. Please check and try again."
}
```

#### Visual Check:
```
Invalid PIN Response
  Status Code: 401
  Headers:
    Content-Type: application/json
  Body: {"success": false, "message": "Invalid PIN. Please check and try again."}
```

#### What Happens:
When the PIN doesn't match or the staff ID isn't found, this response is sent back to the HTML form with HTTP 401 status, allowing the form to display an error message to the user.

---

### Final Flow Structure

Your completed flow should look like this:

```
1. When a HTTP request is received
   ↓
2. Parse JSON
   ↓
3. Get PINs
   ↓
4. Check PIN Valid (Condition)
   ├─ If yes (True Branch)
   │  ├─ Create AUX Record
   │  └─ Success Response (200)
   │
   └─ If no (False Branch)
      └─ Invalid PIN Response (401)
```

---

## Testing the Flow

### Getting the HTTP Endpoint URL

1. **Save the Flow**
   - Click **Save** in the top-right
   - Wait for "Your flow is ready to go" message

2. **Copy the HTTP URL**
   - Go back to the "When a HTTP request is received" trigger
   - Copy the **HTTP POST URL** that appears
   - It should look like:
   ```
   https://prod-xx.region.logic.azure.com:443/workflows/xxxxx/triggers/manual/paths/invoke?api-version=xxxx&sp=xxxx&sv=xxxx&sig=xxxx
   ```

3. **Secure the URL**
   - ⚠️ Keep this URL confidential
   - Only share with authorized developers
   - This URL allows triggering your flow

### Test Method 1: Using Power Automate Test Feature

1. Click **Test** (top-right)
2. Select **Manually**
3. Click **Test**
4. The flow will wait for an HTTP request

5. Use a tool like **Postman** or **cURL** to send a test request:

**Postman:**
- Method: POST
- URL: [Your HTTP POST URL]
- Headers:
  - Content-Type: application/json
- Body (raw JSON):
```json
{
  "staffId": "TEST001",
  "staffName": "Test User",
  "lineManager": "Manager Name",
  "auxType": "AUX-1",
  "category": "System Issue",
  "systemIssue": "Computer freeze",
  "fromTime": "09:00",
  "toTime": "09:15",
  "duration": 15,
  "ticketNumber": "INC123456",
  "location": "Abu Dhabi",
  "wfoWfh": "WFO",
  "comments": "Test submission",
  "approver": "Approver Name",
  "submittedBy": "Test User",
  "ipAddress": "192.168.1.1",
  "deviceInfo": "Test Device",
  "pinHash": "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"
}
```

**cURL:**
```bash
curl -X POST "YOUR_HTTP_POST_URL_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "staffId": "TEST001",
    "staffName": "Test User",
    "lineManager": "Manager Name",
    "auxType": "AUX-1",
    "category": "System Issue",
    "systemIssue": "Computer freeze",
    "fromTime": "09:00",
    "toTime": "09:15",
    "duration": 15,
    "ticketNumber": "INC123456",
    "location": "Abu Dhabi",
    "wfoWfh": "WFO",
    "comments": "Test submission",
    "approver": "Approver Name",
    "submittedBy": "Test User",
    "ipAddress": "192.168.1.1",
    "deviceInfo": "Test Device",
    "pinHash": "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"
  }'
```

6. **Check the Results:**
   - The flow should run and show each step
   - Green checkmarks = success
   - Red X = error
   - Click on each step to see inputs/outputs

### Test Method 2: From Run History

1. After sending a test request, go to the flow
2. Click on the run in **28-day run history**
3. Examine each step:
   - **Parse JSON**: Verify all 18 fields extracted
   - **Get PINs**: Should show 1 item found (or 0 if staff ID doesn't exist)
   - **Condition**: Check which branch executed
   - **Create item**: Verify record created in SharePoint
   - **Response**: Check status code and body

### Validation Checklist

✅ **Successful PIN Test:**
- [ ] HTTP 200 response received
- [ ] Record appears in AUX_Adjustments_2026 SharePoint list
- [ ] All fields populated correctly
- [ ] SubmissionDate has current timestamp
- [ ] Status is "Pending"

✅ **Invalid PIN Test:**
- [ ] HTTP 401 response received
- [ ] NO record created in SharePoint
- [ ] Error message received: "Invalid PIN. Please check and try again."

✅ **Integration Test:**
- [ ] HTML form can successfully submit
- [ ] Form receives and displays success/error messages
- [ ] Multiple submissions work correctly
- [ ] Different staff IDs are handled properly

---

## Troubleshooting Guide

### Common Errors and Solutions

#### Error 1: "The template validation failed"

**Symptoms:**
- Flow won't save
- Error message about JSON schema

**Causes:**
- Invalid JSON in Request Body Schema or Response Body
- Missing quotes or commas
- Trailing commas in JSON

**Solutions:**
1. Validate your JSON at https://jsonlint.com
2. Ensure all property names have double quotes
3. Remove any trailing commas
4. Copy the schemas from this guide exactly

---

#### Error 2: "Access Denied" or "Forbidden" when accessing SharePoint

**Symptoms:**
- Red X on "Get PINs" or "Create AUX Record" action
- Error: "Access to the resource is forbidden"

**Causes:**
- Insufficient SharePoint permissions
- List doesn't exist or was renamed
- Site URL is incorrect

**Solutions:**
1. Verify you have Edit permissions on both SharePoint lists
2. Check that list names match exactly (case-sensitive):
   - AUX_Platform_PINs
   - AUX_Adjustments_2026
3. Verify site URL: `https://bankfab.sharepoint.com/sites/FABInbound`
4. Re-authenticate the SharePoint connection:
   - Click the three dots on the action
   - Change connection
   - Sign in again

---

#### Error 3: "Expression evaluation failed"

**Symptoms:**
- Error on the Condition action
- Message about invalid expression syntax

**Causes:**
- Action name mismatch in expression
- Incorrect expression syntax
- Missing null-safe operators (?)

**Solutions:**
1. Verify the action name matches:
   - If you named it "Get PINs", use: `body('Get_PINs')`
   - Power Automate converts spaces to underscores
2. Ensure you have the null-safe operators (?):
   ```
   body('Get_PINs')?['value']?[0]?['PINHash']
   ```
3. Check for typos in 'PINHash' (case-sensitive)

---

#### Error 4: Filter Query not working / Multiple results returned

**Symptoms:**
- Get PINs action returns wrong data or multiple items
- Condition doesn't work as expected

**Causes:**
- Filter Query syntax error
- Missing quotes around dynamic content
- StaffID field not indexed in SharePoint

**Solutions:**
1. Verify Filter Query syntax:
   ```
   StaffID eq 'staffId'
   ```
   Where `staffId` should be dynamic content (highlighted)
2. Ensure single quotes around the dynamic content
3. Set Top Count to 1
4. Create index on StaffID column in SharePoint (List Settings → Indexed columns)

---

#### Error 5: "Invalid type. Expected String but got Null."

**Symptoms:**
- Error when creating SharePoint item
- Null value being passed to required field

**Causes:**
- Required SharePoint column not receiving data
- Form not sending all required fields
- Dynamic content not selected correctly

**Solutions:**
1. Check SharePoint list - identify which columns are required
2. Verify all required fields have values in the Create item action
3. Test with complete JSON payload (all 18 fields populated)
4. Use Parse JSON dynamic content, not Body dynamic content

---

#### Error 6: DLP Policy Violation

**Symptoms:**
- Flow won't save
- Error about Data Loss Prevention policy
- Connector combination blocked

**Causes:**
- HTTP trigger set to "Anyone" instead of "Only users in my tenant"
- Connector combination not allowed by organization

**Solutions:**
1. In HTTP trigger, set: **Who can trigger the flow** = "Only users in my tenant"
2. Verify you're not using any blocked connectors
3. Contact IT admin if DLP policies need adjustment

---

#### Error 7: "The operation hasn't returned anything"

**Symptoms:**
- Get PINs returns empty array
- Condition always goes to False branch
- No error, but no results

**Causes:**
- Staff ID doesn't exist in AUX_Platform_PINs list
- Filter Query not matching any records
- StaffID value has extra spaces or wrong format

**Solutions:**
1. Verify the staff ID exists in AUX_Platform_PINs SharePoint list
2. Check for exact match (no extra spaces):
   - In Parse JSON output, check staffId value
   - Compare with StaffID in SharePoint
3. Test with a known valid staff ID
4. Add test data to AUX_Platform_PINs list

---

#### Error 8: Choice column values not accepted

**Symptoms:**
- Error when creating SharePoint item
- "Value is not valid for the choice column"

**Causes:**
- Value doesn't match defined choices in SharePoint
- Extra spaces in value
- Case sensitivity

**Solutions:**
1. Check SharePoint list column settings for valid choices:
   - AUXType: Must be exactly "AUX-1", "AUX-2", etc.
   - Location: Must be exactly "Abu Dhabi" or "Al Ain"
   - WFO_WFH: Must be exactly "WFO" or "WFH"
   - Status: Must be exactly "Pending", "Approved", or "Rejected"
2. Ensure HTML form sends exact match values
3. Verify no extra spaces or special characters

---

#### Error 9: "Resource not found for the segment"

**Symptoms:**
- Error on Create item action
- SharePoint column not found

**Causes:**
- SharePoint column name mismatch
- Column doesn't exist
- Typo in column internal name

**Solutions:**
1. Verify column names in SharePoint list (Settings → Columns)
2. Check internal column names:
   - Go to List Settings → Click on column name
   - Check URL for internal name (e.g., field=WFO_WFH)
3. Ensure case matches exactly
4. Some columns may have different internal names than display names

---

#### Error 10: Response not received by HTML form

**Symptoms:**
- Flow runs successfully
- HTML form doesn't receive response
- Form shows "network error" or timeout

**Causes:**
- Missing Content-Type header in Response action
- CORS issue (less likely with "Only users in my tenant")
- Network/firewall blocking

**Solutions:**
1. Verify Response actions have Content-Type header:
   - Key: `Content-Type`
   - Value: `application/json`
2. Check both Success Response AND Invalid PIN Response have headers
3. Test response with Postman first to confirm it works
4. Check browser console for CORS errors

---

### Debugging Tips

#### Enable Detailed Logging

1. Run the flow in Test mode
2. Click on each action to see:
   - **Inputs**: What data was received
   - **Outputs**: What data was produced
   - **Duration**: How long it took

#### Check Run History

1. Go to flow → 28-day run history
2. Click on any run to see full details
3. Look for patterns in failures
4. Export run details for analysis

#### Common Validation Points

**After HTTP Trigger:**
- Check Body contains all 18 fields
- Verify JSON structure is correct

**After Parse JSON:**
- Verify all 18 fields are extracted
- Check for null or empty values
- Ensure data types match (duration should be number)

**After Get PINs:**
- Check "value" array has 1 item
- Verify PINHash field exists
- Confirm StaffID filter worked

**At Condition:**
- Check both left and right values
- Ensure they're being compared correctly
- Look for null values causing False

**After Create Item:**
- Verify item ID is returned
- Check all fields were populated
- Confirm in SharePoint list

---

## Security Considerations

### PIN Security

**Current Implementation:**
- ✅ PINs stored as SHA-256 hashes (never plain text)
- ✅ Comparison happens server-side in Power Automate
- ✅ Plain PINs never stored in SharePoint

**Best Practices:**
1. **Never log or display the pinHash** in any user-visible location
2. **Never send plain PIN over network** - hash on client side using JavaScript
3. **Rotate PINs periodically** - recommend every 90 days
4. **Monitor for suspicious activity** - multiple failed PIN attempts

**HTML Form Requirements:**
The form must:
1. Hash the PIN using SHA-256 before sending
2. Send only the hash in the `pinHash` field
3. Never transmit the plain PIN

**JavaScript Example for HTML Form:**
```javascript
async function hashPIN(pin) {
  const msgBuffer = new TextEncoder().encode(pin);
  const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
  return hashHex;
}

// Usage
const pinHash = await hashPIN(userEnteredPIN);
```

### Audit Trail

**Tracked Information:**
- IP Address (for geolocation tracking)
- Device Info (for device fingerprinting)
- Submission Date (automatic timestamp)
- Submitted By (form submitter)

**Audit Capabilities:**
1. Track who submitted what and when
2. Identify unusual patterns (multiple submissions from same IP)
3. Detect potential fraud or misuse
4. Comply with audit requirements

**Recommendations:**
1. Regularly review audit data in SharePoint
2. Set up alerts for suspicious activity
3. Archive old records per data retention policy

### HTTP Endpoint Security

**Current Protection:**
- ✅ "Only users in my tenant" trigger permission
- ✅ Requires FAB authentication to call
- ✅ HTTPS encrypted communication

**Additional Recommendations:**
1. **Don't share the HTTP URL publicly** - keep it in internal documentation
2. **Rotate the URL periodically** by recreating the flow
3. **Monitor for unusual traffic patterns** in run history
4. **Implement rate limiting** in the HTML form (client-side)

### Data Privacy

**PII Handled:**
- Staff ID
- Staff Name
- IP Address
- Device fingerprint

**Compliance:**
1. Ensure data retention aligns with bank policies
2. Implement data purging for old records
3. Restrict SharePoint list access to authorized personnel
4. Log access to sensitive AUX adjustment data

### SharePoint Permissions

**Recommended Permissions:**

**AUX_Platform_PINs List:**
- RTM Officers: Full Control (to manage PINs)
- Agents: No access (PINs are confidential)
- Managers: Read (for verification only)

**AUX_Adjustments_2026 List:**
- RTM Officers: Full Control
- Agents: Create items only (through flow, not directly)
- Managers: Read (to review their team's adjustments)

**Important:**
- Remove "Everyone" permissions
- Use security groups for role-based access
- Audit permissions regularly

---

## Maintenance and Updates

### Regular Maintenance Tasks

#### Monthly:
- [ ] Review run history for errors
- [ ] Check SharePoint list storage (ensure not nearing limits)
- [ ] Verify all connections are active
- [ ] Test flow with sample data

#### Quarterly:
- [ ] Review and update PIN hashes for agents
- [ ] Audit SharePoint permissions
- [ ] Review and optimize Filter Query performance
- [ ] Update documentation if process changes

#### Annually:
- [ ] Create new SharePoint list for new year (AUX_Adjustments_2027)
- [ ] Archive previous year's data
- [ ] Review security practices
- [ ] Update flow to point to new list

### Updating the Flow

**To Modify Field Mappings:**
1. Edit the flow
2. Go to "Create AUX Record" action
3. Update field mappings as needed
4. Save and test

**To Add New Fields:**
1. Add column to SharePoint list first
2. Update JSON schema in both HTTP trigger AND Parse JSON
3. Add field mapping in Create item action
4. Update HTML form to send new field
5. Test thoroughly

**To Change SharePoint Lists:**
1. Create new list with required columns
2. Edit flow → Change list name in actions
3. Remap fields if column names differ
4. Test extensively before production use

### Version Control

**Recommended Practice:**
1. **Save a copy before major changes:**
   - Flow menu → Save As
   - Name it with version/date: "FAB AUX Platform - Submit v2.0 - 2026-01-23"

2. **Document changes:**
   - Keep changelog in flow description
   - Note what changed and why
   - Include date and your name

3. **Test in separate environment:**
   - Create test SharePoint lists
   - Test flow with test lists first
   - Move to production only after validation

### Backup and Recovery

**Flow Backup:**
1. Export flow as package:
   - Flow menu → Export → Package (.zip)
   - Store in secure location
   - Include documentation

**SharePoint Data Backup:**
1. Use SharePoint built-in versioning
2. Regular exports to Excel for offline backup
3. Consider third-party backup solutions for critical data

**Recovery Process:**
1. Import flow package if flow is deleted
2. Recreate connections
3. Update HTTP URL in HTML form
4. Test thoroughly

---

## Additional Resources

### Power Automate Expression Reference

**Common Expressions Used in This Flow:**

| Expression | Purpose | Example |
|-----------|---------|---------|
| `utcNow()` | Current UTC timestamp | `2026-01-23T10:30:00Z` |
| `body('ActionName')` | Get output from an action | `body('Get_PINs')` |
| `?['property']` | Null-safe property access | `?['value']` |
| `[0]` | Get first array item | `?[0]?['PINHash']` |

### SharePoint Best Practices

1. **Index columns used in filters** (StaffID in this case)
2. **Use views to organize data** (by status, by date, etc.)
3. **Set up alerts** for new submissions
4. **Regular data archiving** to prevent list bloat
5. **Limit column count** to essential fields only

### HTML Form Integration

**Required Form Fields:**
All 18 fields from the JSON schema must be present in the form submission:
- staffId, staffName, lineManager
- auxType, category, systemIssue
- fromTime, toTime, duration
- ticketNumber, location, wfoWfh
- comments, approver, submittedBy
- ipAddress, deviceInfo, pinHash

**Form Submission Example (JavaScript):**
```javascript
const formData = {
  staffId: document.getElementById('staffId').value,
  staffName: document.getElementById('staffName').value,
  // ... other fields ...
  pinHash: await hashPIN(document.getElementById('pin').value),
  ipAddress: await getUserIP(),
  deviceInfo: getDeviceFingerprint()
};

const response = await fetch('YOUR_FLOW_HTTP_URL', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(formData)
});

const result = await response.json();

if (response.ok && result.success) {
  alert('AUX submitted successfully!');
} else {
  alert(result.message || 'Submission failed');
}
```

### Contact and Support

**For Flow Issues:**
- Check Power Automate run history
- Review this troubleshooting guide
- Contact IT support with flow run ID

**For SharePoint Issues:**
- Verify permissions
- Check list settings
- Contact SharePoint admin

**For Form Issues:**
- Check browser console for errors
- Verify all fields are being sent
- Test with Postman to isolate issue

---

## Appendix

### Complete JSON Schema Reference

```json
{
  "type": "object",
  "properties": {
    "staffId": {"type": "string"},
    "staffName": {"type": "string"},
    "lineManager": {"type": "string"},
    "auxType": {"type": "string"},
    "category": {"type": "string"},
    "systemIssue": {"type": "string"},
    "fromTime": {"type": "string"},
    "toTime": {"type": "string"},
    "duration": {"type": "number"},
    "ticketNumber": {"type": "string"},
    "location": {"type": "string"},
    "wfoWfh": {"type": "string"},
    "comments": {"type": "string"},
    "approver": {"type": "string"},
    "submittedBy": {"type": "string"},
    "ipAddress": {"type": "string"},
    "deviceInfo": {"type": "string"},
    "pinHash": {"type": "string"}
  }
}
```

### Sample Test Payloads

**Valid Submission (for testing):**
```json
{
  "staffId": "FAB12345",
  "staffName": "Ahmed Al Mansoori",
  "lineManager": "Mohammed Al Hashimi",
  "auxType": "AUX-1",
  "category": "System Issue",
  "systemIssue": "Genesys Cloud disconnected",
  "fromTime": "14:30",
  "toTime": "14:45",
  "duration": 15,
  "ticketNumber": "INC2026012301",
  "location": "Abu Dhabi",
  "wfoWfh": "WFO",
  "comments": "System automatically logged me out",
  "approver": "Sara Al Mazrouei",
  "submittedBy": "Ahmed Al Mansoori",
  "ipAddress": "172.16.10.25",
  "deviceInfo": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
  "pinHash": "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"
}
```

**Note:** The PIN hash above is for PIN "password" - replace with actual agent PIN hash for real testing.

### Flow Performance Metrics

**Expected Performance:**
- **Total execution time:** 2-5 seconds
- **HTTP trigger:** < 100ms
- **Parse JSON:** < 100ms
- **Get PINs:** 500ms - 1s (with indexed column)
- **Condition:** < 100ms
- **Create item:** 500ms - 1s
- **Response:** < 100ms

**Optimization Tips:**
1. Index the StaffID column in AUX_Platform_PINs
2. Use Top Count = 1 in Get PINs
3. Keep SharePoint lists under 5,000 items (or use large list threshold techniques)
4. Archive old data regularly

### Glossary

- **AUX:** Auxiliary (non-productive time codes in contact centers)
- **DLP:** Data Loss Prevention
- **OData:** Open Data Protocol (used for SharePoint filtering)
- **PIN:** Personal Identification Number
- **RTM:** Real-Time Management
- **SHA-256:** Secure Hash Algorithm 256-bit (cryptographic hash function)
- **UTC:** Coordinated Universal Time
- **WFH:** Work From Home
- **WFO:** Work From Office

---

## Quick Reference Card

### Flow Actions Summary

| Step | Action | Key Configuration |
|------|--------|-------------------|
| 1 | HTTP Trigger | Method: POST, Permission: Only users in my tenant |
| 2 | Parse JSON | Content: Body, Schema: 18 properties |
| 3 | Get PINs | Filter: `StaffID eq 'staffId'`, Top: 1 |
| 4 | Condition | `body('Get_PINs')?['value']?[0]?['PINHash']` eq `pinHash` |
| 5A | Create Item | Map all 18 fields, Status: "Pending", Date: utcNow() |
| 5B | Success Response | Status: 200, Body: success JSON |
| 6 | Error Response | Status: 401, Body: error JSON |

### Critical Checkpoints

✅ **Before Saving:**
- [ ] HTTP trigger set to "Only users in my tenant"
- [ ] Both Response actions have Content-Type headers
- [ ] All dynamic content from Parse JSON (not Body)
- [ ] Get PINs action name matches expression
- [ ] Status field set to "Pending" (manual text)
- [ ] SubmissionDate uses utcNow() expression

✅ **Before Production:**
- [ ] Tested with valid PIN
- [ ] Tested with invalid PIN
- [ ] Verified SharePoint records created correctly
- [ ] HTML form receives responses properly
- [ ] SharePoint permissions configured
- [ ] HTTP URL secured and documented

---

**Document Version:** 1.0
**Last Updated:** 2026-01-23
**Author:** FAB RTM Team
**Flow Version:** FAB AUX Platform - Submit v1.0

---

*End of Guide*
