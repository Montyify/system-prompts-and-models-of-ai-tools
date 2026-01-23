# FAB AUX Platform - Quick Troubleshooting Guide

## 🚨 Emergency Quick Reference

This guide provides rapid solutions to common issues. For detailed explanations, refer to the main [Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md).

---

## Quick Diagnostics

### Check Flow Status
1. Open Power Automate → My flows
2. Check flow status indicator:
   - ✅ **On** = Flow is active
   - ❌ **Off** = Flow is disabled
   - ⚠️ **Suspended** = Too many errors, needs attention

### Check Last Run
1. Click on flow → 28-day run history
2. Latest run shows:
   - ✅ **Succeeded** = Working correctly
   - ❌ **Failed** = Something went wrong
   - ⏳ **Running** = Currently processing

---

## Common Error Messages & Instant Fixes

### Error: "The template validation failed"

**Quick Fix:**
1. Go to HTTP trigger action
2. Copy JSON schema from [JSON Schema Reference](JSON-Schema-Reference.md)
3. Paste exactly (no modifications)
4. Save

---

### Error: "Access Denied" / "Forbidden"

**Quick Fix:**
1. Check SharePoint permissions (need Edit access)
2. Re-authenticate SharePoint connection:
   - Click ⋯ on SharePoint action
   - Change connection → Add new connection
   - Sign in
3. Verify list names:
   - `AUX_Platform_PINs` (exact spelling)
   - `AUX_Adjustments_2026` (exact spelling)

---

### Error: "Expression evaluation failed"

**Quick Fix:**
1. Check Condition action name matches expression
2. If action named "Get PINs", use: `body('Get_PINs')`
3. Copy this exact expression:
   ```
   body('Get_PINs')?['value']?[0]?['PINHash']
   ```

---

### Error: "Invalid type. Expected String but got Null"

**Quick Fix:**
1. Open Create item action
2. Find empty required field
3. Add value from Parse JSON dynamic content
4. Never use Body dynamic content - always use Parse JSON

---

### Error: DLP Policy Violation

**Quick Fix:**
1. Open HTTP trigger
2. Find "Who can trigger the flow"
3. Change to: **"Only users in my tenant"**
4. Save

---

### Error: Filter Query not working

**Quick Fix:**
1. Open Get PINs action
2. Delete existing Filter Query
3. Re-enter: `StaffID eq '`
4. Add `staffId` from Parse JSON dynamic content
5. Type closing quote: `'`
6. Final result: `StaffID eq 'staffId'` (with staffId highlighted)

---

### Error: "Resource not found for the segment"

**Quick Fix:**
1. SharePoint column name mismatch
2. Go to SharePoint list → Settings → Columns
3. Verify exact column names (case-sensitive)
4. Update Create item action with correct names

---

### Error: "Choice value is not valid"

**Quick Fix:**
Check these exact values in your submission:
- **AUXType:** Must be `AUX-1`, `AUX-2`, `AUX-3`, `AUX-4`, `AUX-5`, or `AUX-6` (uppercase)
- **Location:** Must be `Abu Dhabi` or `Al Ain` (exact case)
- **WFO_WFH:** Must be `WFO` or `WFH` (uppercase)
- **Status:** Must be `Pending`, `Approved`, or `Rejected`

---

### Error: Response not received by form

**Quick Fix:**
1. Check both Response actions have headers:
   - Key: `Content-Type`
   - Value: `application/json`
2. Verify JSON body has no syntax errors
3. Test with Postman first to isolate issue

---

### Error: "The operation hasn't returned anything"

**Quick Fix:**
1. Staff ID doesn't exist in AUX_Platform_PINs
2. Add test record to AUX_Platform_PINs:
   - StaffID: `TEST001`
   - PINHash: `03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4` (hash of "1234")
3. Test with this staff ID

---

## Flow Not Triggering At All

### Symptoms
- Sending HTTP request but nothing happens
- No run appears in history
- Form shows network error

### Rapid Checks

1. **Verify HTTP URL is correct**
   - Open HTTP trigger
   - Copy HTTP POST URL
   - Compare with URL in HTML form
   - They must match exactly

2. **Check flow is turned ON**
   - Go to My flows
   - Flow should show "On" status
   - If Off, click to turn on

3. **Test with cURL**
   ```bash
   curl -X POST "YOUR_URL" \
     -H "Content-Type: application/json" \
     -d '{"staffId":"TEST","staffName":"Test",...}'
   ```
   - If this works, issue is in HTML form
   - If this fails, issue is in flow

4. **Check authentication**
   - Ensure you're signed into O365/FAB account
   - "Only users in my tenant" requires authentication
   - Test from browser signed into FAB account

---

## PIN Validation Always Failing

### Symptoms
- Always returns 401 Invalid PIN
- Even with correct PIN
- Condition always goes to False

### Rapid Checks

1. **Verify PIN hash generation**
   - Test hash generator: https://emn178.github.io/online-tools/sha256.html
   - Hash "1234" should give: `03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4`
   - If different, fix hash generation code

2. **Check stored hash in SharePoint**
   - Open AUX_Platform_PINs list
   - Find test staff ID
   - Check PINHash field has correct hash
   - No spaces, all lowercase

3. **Verify expression syntax**
   - Open Condition action
   - Left side should be expression (not dynamic content):
     ```
     body('Get_PINs')?['value']?[0]?['PINHash']
     ```
   - Right side should be dynamic content: `pinHash` from Parse JSON

4. **Check Get PINs is returning data**
   - View run history
   - Click on Get PINs action
   - Outputs should show 1 item with PINHash value
   - If empty, staff ID doesn't exist in list

---

## Records Not Being Created

### Symptoms
- Flow succeeds (200 response)
- But no record in AUX_Adjustments_2026

### Rapid Checks

1. **Check Condition logic**
   - View run history
   - See which branch executed (Yes or No)
   - If No branch, PIN validation failed
   - If Yes branch, check Create item action

2. **Check Create item action**
   - Click on Create item in run history
   - Look for error message
   - Common: missing required field

3. **Verify SharePoint list**
   - Open AUX_Adjustments_2026
   - Check "All Items" view
   - Sort by Created (newest first)
   - Record should be there

4. **Check permissions**
   - Ensure flow has permission to create items
   - Test: manually create item in SharePoint
   - If you can't, permissions issue

---

## Performance Issues

### Symptoms
- Flow takes >10 seconds
- Timeouts
- "Slow run" warnings

### Rapid Fixes

1. **Index SharePoint columns**
   - AUX_Platform_PINs → List Settings → Indexed columns
   - Create index on StaffID
   - Improves Get PINs performance

2. **Limit results**
   - Get PINs action → Top Count: `1`
   - No need to retrieve all records

3. **Check SharePoint list size**
   - Lists >5,000 items slow down
   - Archive old records
   - Consider yearly lists (AUX_Adjustments_2027, etc.)

---

## Testing Checklist

### Before Going Live

- [ ] Flow turned ON
- [ ] HTTP URL copied to form
- [ ] Test with valid PIN (should succeed)
- [ ] Test with invalid PIN (should fail with 401)
- [ ] Record appears in SharePoint
- [ ] All fields populated correctly
- [ ] Status shows "Pending"
- [ ] SubmissionDate has timestamp
- [ ] Form receives success message
- [ ] Form receives error message for bad PIN

### Test Data

**Test Staff ID:** `TEST001`
**Test PIN:** `1234`
**Test PIN Hash:** `03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4`

Add this to AUX_Platform_PINs for testing.

---

## Quick Validation Commands

### Generate PIN Hash (Linux/Mac)
```bash
echo -n "1234" | sha256sum
```

### Test Flow with cURL
```bash
curl -X POST "YOUR_FLOW_URL" \
  -H "Content-Type: application/json" \
  -d @test-payload.json
```

### Validate JSON
```bash
cat payload.json | jq .
```
If invalid JSON, jq will show error.

---

## Emergency Contacts & Resources

### When Stuck
1. Check run history for error details
2. Review main [Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)
3. Verify [JSON Schema](JSON-Schema-Reference.md)
4. Contact IT support with:
   - Flow name
   - Run ID (from history)
   - Error message
   - Screenshot of error

### Useful Links
- **Power Automate Portal:** https://make.powerautomate.com
- **SharePoint Site:** https://bankfab.sharepoint.com/sites/FABInbound
- **JSON Validator:** https://jsonlint.com
- **SHA-256 Generator:** https://emn178.github.io/online-tools/sha256.html

---

## Quick Reference: Action Names

Use these exact action names in your flow:

1. **When a HTTP request is received** (trigger)
2. **Parse JSON**
3. **Get PINs** (SharePoint Get items)
4. **Check PIN Valid** (Condition)
5. **Create AUX Record** (SharePoint Create item) - in Yes branch
6. **Success Response** (Response) - in Yes branch
7. **Invalid PIN Response** (Response) - in No branch

---

## Common Copy-Paste Snippets

### JSON Schema (for HTTP trigger & Parse JSON)
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

### Filter Query (for Get PINs)
```
StaffID eq 'staffId'
```
(where `staffId` is dynamic content from Parse JSON)

### Condition Expression (left side)
```
body('Get_PINs')?['value']?[0]?['PINHash']
```

### Success Response Body
```json
{"success": true, "message": "AUX submitted successfully!"}
```

### Error Response Body
```json
{"success": false, "message": "Invalid PIN. Please check and try again."}
```

---

## Debug Mode: Step-by-Step Check

If completely stuck, go through this checklist:

### Step 1: HTTP Trigger
- [ ] Method is POST
- [ ] Permission is "Only users in my tenant"
- [ ] Schema has 18 properties
- [ ] HTTP URL exists and is copied

### Step 2: Parse JSON
- [ ] Content is Body (from HTTP trigger)
- [ ] Schema matches HTTP trigger schema
- [ ] 18 fields visible in dynamic content

### Step 3: Get PINs
- [ ] Site URL is correct
- [ ] List name is AUX_Platform_PINs
- [ ] Filter Query: `StaffID eq 'staffId'`
- [ ] Top Count is 1
- [ ] Action renamed to "Get PINs"

### Step 4: Condition
- [ ] Left side is expression: `body('Get_PINs')?['value']?[0]?['PINHash']`
- [ ] Operator is "is equal to"
- [ ] Right side is pinHash (from Parse JSON)

### Step 5A: Create AUX Record (Yes branch)
- [ ] Site URL is correct
- [ ] List name is AUX_Adjustments_2026
- [ ] All fields mapped to Parse JSON (not Body)
- [ ] Status is manual text "Pending"
- [ ] SubmissionDate is expression `utcNow()`

### Step 5B: Success Response (Yes branch)
- [ ] Status Code is 200
- [ ] Content-Type header exists
- [ ] Body is valid JSON with success: true

### Step 6: Invalid PIN Response (No branch)
- [ ] Status Code is 401
- [ ] Content-Type header exists
- [ ] Body is valid JSON with success: false

---

## Status Codes & What They Mean

| Code | Meaning | When It Happens |
|------|---------|----------------|
| 200 | Success | PIN valid, record created |
| 401 | Unauthorized | Invalid PIN or staff ID not found |
| 400 | Bad Request | Invalid JSON or missing required fields |
| 403 | Forbidden | DLP policy violation or permission issue |
| 500 | Server Error | Flow crashed, check run history |

---

## Power Automate Best Practices

### DO ✅
- Save frequently
- Test after each change
- Use descriptive action names
- Check run history regularly
- Keep flows simple
- Document changes

### DON'T ❌
- Use complex nested conditions
- Add unnecessary loops
- Skip testing
- Use Body dynamic content (use Parse JSON)
- Ignore errors in run history
- Share HTTP URL publicly

---

## Quick Recovery Steps

### Flow Completely Broken?

1. **Check if flow is On**
   - Turn off, wait 30 seconds, turn back on

2. **Re-authenticate connections**
   - Check flow → Connections
   - Remove and re-add SharePoint connection

3. **Restore from backup**
   - If you exported flow, import it
   - Reconfigure connections
   - Update HTTP URL in form

4. **Start fresh (last resort)**
   - Export current flow as backup
   - Create new flow following guide
   - Test thoroughly before switching

---

## Monitoring & Alerts

### Set Up Monitoring

1. **Create alert for failures**
   - Flow → ... → Configure run after
   - Send email on failure

2. **Regular checks**
   - Weekly: Review run history
   - Monthly: Check error patterns
   - Quarterly: Performance review

3. **Metrics to track**
   - Success rate (should be >95%)
   - Average duration (should be <5s)
   - Failure patterns (specific times/users?)

---

## Advanced Troubleshooting

### Enable Detailed Tracing

1. Run flow in Test mode
2. Click on each action
3. Check Inputs and Outputs tabs
4. Look for unexpected null values

### Export Run Details

1. Go to run history
2. Click on failed run
3. Click "Download flow run"
4. Share with support team

### Check Service Health

- Power Automate status: https://status.powerplatform.microsoft.com/
- SharePoint status: Check Microsoft 365 admin center
- Network issues: Contact IT

---

## Glossary of Error Terms

- **Expression evaluation failed** = Syntax error in expression or action name mismatch
- **Access denied** = Permission issue with SharePoint
- **Template validation** = JSON syntax error
- **Resource not found** = Wrong list/column name
- **Choice value invalid** = Value doesn't match SharePoint choices
- **Operation returned nothing** = Get PINs found no matching records
- **DLP policy** = Data Loss Prevention policy blocking connector

---

## Final Tips

1. **Always test in order:**
   - First with Postman/cURL
   - Then with HTML form
   - Then with real users

2. **Keep a test account:**
   - Staff ID: TEST001
   - Known PIN hash
   - Use for regular testing

3. **Document everything:**
   - Note changes made
   - Keep changelog
   - Save working version

4. **Have backup plan:**
   - Export flow monthly
   - Test restore process
   - Know rollback steps

---

**Document Version:** 1.0
**Last Updated:** 2026-01-23
**Related Documents:**
- [Complete Flow Building Guide](FAB-AUX-Platform-Flow-Guide.md)
- [JSON Schema Reference](JSON-Schema-Reference.md)

---

*End of Quick Troubleshooting Guide*
