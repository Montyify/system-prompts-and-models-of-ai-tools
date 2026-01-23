# FAB AUX Platform - JSON Schema Reference

## Overview
This document provides the complete JSON schema and sample payloads for the FAB AUX Platform Power Automate flow.

---

## Request Body JSON Schema

Use this schema in both the **HTTP Trigger** and **Parse JSON** actions.

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

---

## Field Descriptions

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `staffId` | string | Yes | Agent's staff ID | "FAB12345" |
| `staffName` | string | Yes | Agent's full name | "Ahmed Al Mansoori" |
| `lineManager` | string | Yes | Agent's direct manager | "Mohammed Al Hashimi" |
| `auxType` | string | Yes | AUX type code | "AUX-1" |
| `category` | string | Yes | Reason category | "System Issue" |
| `systemIssue` | string | No | Detailed issue description | "Genesys Cloud disconnected" |
| `fromTime` | string | Yes | Start time (HH:MM format) | "14:30" |
| `toTime` | string | Yes | End time (HH:MM format) | "14:45" |
| `duration` | number | Yes | Duration in minutes | 15 |
| `ticketNumber` | string | No | IT ticket reference | "INC2026012301" |
| `location` | string | Yes | Agent location | "Abu Dhabi" or "Al Ain" |
| `wfoWfh` | string | Yes | Work mode | "WFO" or "WFH" |
| `comments` | string | No | Additional notes | "System automatically logged me out" |
| `approver` | string | Yes | Approving manager name | "Sara Al Mazrouei" |
| `submittedBy` | string | Yes | Person submitting the form | "Ahmed Al Mansoori" |
| `ipAddress` | string | Yes | User's IP address for audit | "172.16.10.25" |
| `deviceInfo` | string | Yes | User agent/device fingerprint | "Mozilla/5.0..." |
| `pinHash` | string | Yes | SHA-256 hashed PIN | "5e884898da..." |

---

## AUX Type Codes

| Code | Description | Common Use Cases |
|------|-------------|------------------|
| AUX-1 | System Issues | System crashes, software failures, login issues |
| AUX-2 | Technical Problems | Network issues, hardware problems, peripheral failures |
| AUX-3 | Training | Coaching sessions, training programs, skill development |
| AUX-4 | Meetings | Team meetings, one-on-ones, briefings |
| AUX-5 | Break | Lunch breaks, prayer breaks, rest periods |
| AUX-6 | Administrative | Documentation, reporting, non-call tasks |

---

## Sample Payloads

### Example 1: System Issue (AUX-1)

```json
{
  "staffId": "FAB12345",
  "staffName": "Ahmed Al Mansoori",
  "lineManager": "Mohammed Al Hashimi",
  "auxType": "AUX-1",
  "category": "System Issue",
  "systemIssue": "Genesys Cloud disconnected during customer call",
  "fromTime": "14:30",
  "toTime": "14:45",
  "duration": 15,
  "ticketNumber": "INC2026012301",
  "location": "Abu Dhabi",
  "wfoWfh": "WFO",
  "comments": "System automatically logged me out, had to restart application",
  "approver": "Sara Al Mazrouei",
  "submittedBy": "Ahmed Al Mansoori",
  "ipAddress": "172.16.10.25",
  "deviceInfo": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "pinHash": "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"
}
```

### Example 2: Training Session (AUX-3)

```json
{
  "staffId": "FAB67890",
  "staffName": "Fatima Al Dhaheri",
  "lineManager": "Khalid Al Suwaidi",
  "auxType": "AUX-3",
  "category": "Training",
  "systemIssue": "",
  "fromTime": "10:00",
  "toTime": "11:30",
  "duration": 90,
  "ticketNumber": "",
  "location": "Al Ain",
  "wfoWfh": "WFH",
  "comments": "Product knowledge training - new credit card features",
  "approver": "Khalid Al Suwaidi",
  "submittedBy": "Fatima Al Dhaheri",
  "ipAddress": "192.168.1.150",
  "deviceInfo": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
  "pinHash": "b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb9"
}
```

### Example 3: Technical Problem (AUX-2)

```json
{
  "staffId": "FAB11122",
  "staffName": "Omar Al Kaabi",
  "lineManager": "Mohammed Al Hashimi",
  "auxType": "AUX-2",
  "category": "Hardware Issue",
  "systemIssue": "Headset microphone not working",
  "fromTime": "09:15",
  "toTime": "09:30",
  "duration": 15,
  "ticketNumber": "INC2026012302",
  "location": "Abu Dhabi",
  "wfoWfh": "WFO",
  "comments": "IT replaced headset, tested and working now",
  "approver": "Mohammed Al Hashimi",
  "submittedBy": "Omar Al Kaabi",
  "ipAddress": "172.16.10.47",
  "deviceInfo": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "pinHash": "ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f"
}
```

### Example 4: Meeting (AUX-4)

```json
{
  "staffId": "FAB22233",
  "staffName": "Mariam Al Nuaimi",
  "lineManager": "Sara Al Mazrouei",
  "auxType": "AUX-4",
  "category": "Team Meeting",
  "systemIssue": "",
  "fromTime": "15:00",
  "toTime": "15:30",
  "duration": 30,
  "ticketNumber": "",
  "location": "Abu Dhabi",
  "wfoWfh": "WFO",
  "comments": "Monthly team performance review meeting",
  "approver": "Sara Al Mazrouei",
  "submittedBy": "Mariam Al Nuaimi",
  "ipAddress": "172.16.10.89",
  "deviceInfo": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "pinHash": "fcde2b2edba56bf408601fb721fe9b5c338d10ee429ea04fae5511b68fbf8fb9"
}
```

### Example 5: Extended Break (AUX-5)

```json
{
  "staffId": "FAB33344",
  "staffName": "Rashid Al Mansoori",
  "lineManager": "Khalid Al Suwaidi",
  "auxType": "AUX-5",
  "category": "Prayer Break",
  "systemIssue": "",
  "fromTime": "13:00",
  "toTime": "13:15",
  "duration": 15,
  "ticketNumber": "",
  "location": "Al Ain",
  "wfoWfh": "WFO",
  "comments": "Maghrib prayer",
  "approver": "Khalid Al Suwaidi",
  "submittedBy": "Rashid Al Mansoori",
  "ipAddress": "172.16.11.23",
  "deviceInfo": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "pinHash": "d4735e3a265e16eee03f59718b9b5d03019c07d8b6c51f90da3a666eec13ab35"
}
```

### Example 6: Administrative Work (AUX-6)

```json
{
  "staffId": "FAB44455",
  "staffName": "Noura Al Shamsi",
  "lineManager": "Sara Al Mazrouei",
  "auxType": "AUX-6",
  "category": "Documentation",
  "systemIssue": "",
  "fromTime": "16:30",
  "toTime": "17:00",
  "duration": 30,
  "ticketNumber": "",
  "location": "Abu Dhabi",
  "wfoWfh": "WFH",
  "comments": "Completing customer follow-up documentation and quality forms",
  "approver": "Sara Al Mazrouei",
  "submittedBy": "Noura Al Shamsi",
  "ipAddress": "192.168.1.201",
  "deviceInfo": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "pinHash": "4e07408562bedb8b60ce05c1decfe3ad16b72230967de01f640b7e4729b49fce"
}
```

---

## Response Formats

### Success Response (HTTP 200)

```json
{
  "success": true,
  "message": "AUX submitted successfully!"
}
```

**When This Occurs:**
- PIN validation successful
- Record created in SharePoint
- All required fields validated

### Error Response (HTTP 401)

```json
{
  "success": false,
  "message": "Invalid PIN. Please check and try again."
}
```

**When This Occurs:**
- PIN hash doesn't match stored hash
- Staff ID not found in AUX_Platform_PINs
- Authentication failed

---

## PIN Hash Generation

### Generating SHA-256 Hash

The PIN must be hashed using SHA-256 before transmission. **Never send plain text PINs.**

#### JavaScript (Browser)

```javascript
async function hashPIN(pin) {
  // Convert PIN string to byte array
  const msgBuffer = new TextEncoder().encode(pin);

  // Hash using SHA-256
  const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);

  // Convert to hex string
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join('');

  return hashHex;
}

// Example usage
const userPin = "1234"; // User's 4-digit PIN
const hashedPin = await hashPIN(userPin);
console.log(hashedPin); // Output: 03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4
```

#### Python

```python
import hashlib

def hash_pin(pin):
    """Generate SHA-256 hash of PIN"""
    return hashlib.sha256(pin.encode()).hexdigest()

# Example usage
user_pin = "1234"
hashed_pin = hash_pin(user_pin)
print(hashed_pin)  # Output: 03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4
```

#### PowerShell

```powershell
function Get-PINHash {
    param([string]$PIN)

    $stringAsStream = [System.IO.MemoryStream]::new()
    $writer = [System.IO.StreamWriter]::new($stringAsStream)
    $writer.Write($PIN)
    $writer.Flush()
    $stringAsStream.Position = 0

    $hash = Get-FileHash -InputStream $stringAsStream -Algorithm SHA256
    return $hash.Hash.ToLower()
}

# Example usage
$userPin = "1234"
$hashedPin = Get-PINHash -PIN $userPin
Write-Output $hashedPin  # Output: 03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4
```

#### Command Line (Linux/Mac)

```bash
echo -n "1234" | sha256sum | awk '{print $1}'
# Output: 03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4
```

### Common PIN Hashes (For Testing Only)

| PIN | SHA-256 Hash |
|-----|--------------|
| 1234 | 03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4 |
| 5678 | ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f |
| 9999 | fcde2b2edba56bf408601fb721fe9b5c338d10ee429ea04fae5511b68fbf8fb9 |
| 0000 | e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855 |

⚠️ **Security Warning:** These are example hashes for testing purposes only. Never use simple PINs in production. Production PINs should be complex and unique per agent.

---

## Testing with cURL

### Successful Submission Test

```bash
curl -X POST "YOUR_FLOW_HTTP_URL_HERE" \
  -H "Content-Type: application/json" \
  -d '{
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
    "pinHash": "03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4"
  }'
```

**Expected Response:**
```json
{
  "success": true,
  "message": "AUX submitted successfully!"
}
```

### Invalid PIN Test

```bash
curl -X POST "YOUR_FLOW_HTTP_URL_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "staffId": "FAB12345",
    "staffName": "Ahmed Al Mansoori",
    "lineManager": "Mohammed Al Hashimi",
    "auxType": "AUX-1",
    "category": "System Issue",
    "systemIssue": "Test",
    "fromTime": "14:30",
    "toTime": "14:45",
    "duration": 15,
    "ticketNumber": "",
    "location": "Abu Dhabi",
    "wfoWfh": "WFO",
    "comments": "Testing invalid PIN",
    "approver": "Sara Al Mazrouei",
    "submittedBy": "Ahmed Al Mansoori",
    "ipAddress": "172.16.10.25",
    "deviceInfo": "Mozilla/5.0",
    "pinHash": "wronghash123456789"
  }'
```

**Expected Response:**
```json
{
  "success": false,
  "message": "Invalid PIN. Please check and try again."
}
```

---

## Testing with Postman

### Setup

1. **Create New Request**
   - Method: `POST`
   - URL: Your flow's HTTP POST URL

2. **Headers**
   - Key: `Content-Type`
   - Value: `application/json`

3. **Body**
   - Select: `raw`
   - Type: `JSON`
   - Paste one of the sample payloads above

4. **Send Request**
   - Click "Send"
   - Check response status and body

### Collection Variables

Create variables in Postman for easy testing:

- `flow_url`: Your flow HTTP POST URL
- `test_staff_id`: Test agent staff ID
- `test_pin_hash`: Test PIN hash

Use in request:
```json
{
  "staffId": "{{test_staff_id}}",
  "pinHash": "{{test_pin_hash}}",
  ...
}
```

---

## Validation Rules

### Field Validation in HTML Form

Implement these validations in your HTML form before submission:

| Field | Validation Rule |
|-------|----------------|
| `staffId` | Required, alphanumeric, max 20 characters |
| `staffName` | Required, min 3 characters, max 100 characters |
| `lineManager` | Required, min 3 characters, max 100 characters |
| `auxType` | Required, must be one of: AUX-1, AUX-2, AUX-3, AUX-4, AUX-5, AUX-6 |
| `category` | Required, max 100 characters |
| `systemIssue` | Optional, max 500 characters |
| `fromTime` | Required, format HH:MM (24-hour) |
| `toTime` | Required, format HH:MM (24-hour), must be after fromTime |
| `duration` | Required, must be positive integer, calculated from times |
| `ticketNumber` | Optional, alphanumeric, max 50 characters |
| `location` | Required, must be "Abu Dhabi" or "Al Ain" |
| `wfoWfh` | Required, must be "WFO" or "WFH" |
| `comments` | Optional, max 500 characters |
| `approver` | Required, min 3 characters, max 100 characters |
| `submittedBy` | Required, min 3 characters, max 100 characters |
| `ipAddress` | Auto-captured, valid IP format |
| `deviceInfo` | Auto-captured, user agent string |
| `pinHash` | Required, 64-character hex string (SHA-256 output) |

### JavaScript Validation Example

```javascript
function validateForm(formData) {
  const errors = [];

  // Validate staffId
  if (!formData.staffId || formData.staffId.length > 20) {
    errors.push("Invalid Staff ID");
  }

  // Validate auxType
  const validAuxTypes = ['AUX-1', 'AUX-2', 'AUX-3', 'AUX-4', 'AUX-5', 'AUX-6'];
  if (!validAuxTypes.includes(formData.auxType)) {
    errors.push("Invalid AUX Type");
  }

  // Validate time format (HH:MM)
  const timeRegex = /^([01]\d|2[0-3]):([0-5]\d)$/;
  if (!timeRegex.test(formData.fromTime) || !timeRegex.test(formData.toTime)) {
    errors.push("Invalid time format. Use HH:MM");
  }

  // Validate toTime is after fromTime
  const from = new Date(`2000-01-01 ${formData.fromTime}`);
  const to = new Date(`2000-01-01 ${formData.toTime}`);
  if (to <= from) {
    errors.push("End time must be after start time");
  }

  // Validate location
  if (!['Abu Dhabi', 'Al Ain'].includes(formData.location)) {
    errors.push("Invalid location");
  }

  // Validate WFO/WFH
  if (!['WFO', 'WFH'].includes(formData.wfoWfh)) {
    errors.push("Invalid work mode");
  }

  // Validate PIN hash format (64 hex characters)
  const hashRegex = /^[a-f0-9]{64}$/i;
  if (!hashRegex.test(formData.pinHash)) {
    errors.push("Invalid PIN hash format");
  }

  return errors;
}
```

---

## Field Mapping Reference

### HTML Form → JSON → SharePoint

| HTML Form Field | JSON Property | SharePoint Column | Type |
|----------------|---------------|-------------------|------|
| Staff ID | `staffId` | Title, StaffID | Text |
| Staff Name | `staffName` | StaffName | Text |
| Line Manager | `lineManager` | LineManager | Text |
| AUX Type | `auxType` | AUXType | Choice |
| Category | `category` | Category | Text |
| System Issue | `systemIssue` | Reason | Multi-line |
| From Time | `fromTime` | FromTime | Text |
| To Time | `toTime` | ToTime | Text |
| Duration | `duration` | Duration | Number |
| Ticket Number | `ticketNumber` | ITTicket | Text |
| Location | `location` | Location | Choice |
| WFO/WFH | `wfoWfh` | WFO_WFH | Choice |
| Comments | `comments` | Remarks | Multi-line |
| Approver | `approver` | ApprovedBy | Text |
| Submitted By | `submittedBy` | SubmittedBy | Text |
| IP Address | `ipAddress` | *(not stored)* | - |
| Device Info | `deviceInfo` | *(not stored)* | - |
| PIN Hash | `pinHash` | *(not stored)* | - |
| *(auto)* | - | Status | Choice |
| *(auto)* | - | SubmissionDate | DateTime |

**Note:** IP Address, Device Info, and PIN Hash are used for validation/audit but not stored in the AUX_Adjustments_2026 list. Consider adding audit fields if needed.

---

## Common Mistakes to Avoid

1. ❌ **Missing quotes in JSON**
   ```json
   { staffId: "FAB12345" }  // WRONG
   ```
   ```json
   { "staffId": "FAB12345" }  // CORRECT
   ```

2. ❌ **Trailing commas**
   ```json
   { "staffId": "FAB12345", }  // WRONG
   ```
   ```json
   { "staffId": "FAB12345" }  // CORRECT
   ```

3. ❌ **Wrong data type for duration**
   ```json
   { "duration": "15" }  // WRONG (string)
   ```
   ```json
   { "duration": 15 }  // CORRECT (number)
   ```

4. ❌ **Sending plain PIN instead of hash**
   ```json
   { "pinHash": "1234" }  // WRONG (plain PIN)
   ```
   ```json
   { "pinHash": "03ac674216f..." }  // CORRECT (hashed)
   ```

5. ❌ **Invalid choice values**
   ```json
   { "auxType": "aux-1" }  // WRONG (lowercase)
   ```
   ```json
   { "auxType": "AUX-1" }  // CORRECT (uppercase)
   ```

---

## Additional Resources

### Online JSON Validators
- https://jsonlint.com/ - Validate JSON syntax
- https://www.jsonschemavalidator.net/ - Validate against schema

### SHA-256 Hash Generators (For Testing)
- https://emn178.github.io/online-tools/sha256.html
- https://passwordsgenerator.net/sha256-hash-generator/

### Postman Learning
- https://learning.postman.com/docs/getting-started/introduction/

---

**Document Version:** 1.0
**Last Updated:** 2026-01-23
**Related Documents:** FAB-AUX-Platform-Flow-Guide.md

---

*End of JSON Schema Reference*
