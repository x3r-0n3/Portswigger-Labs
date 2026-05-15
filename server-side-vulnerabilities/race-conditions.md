# 🧠Lab - 1 Race Condition — Limit Overrun Race Condition Lab Notes

---

## 🔵 OVERVIEW

This lab demonstrates:

- Race Condition vulnerability

specifically:

- Limit Overrun Race Condition

The application allows:

- coupon usage
- shopping cart operations

but processes requests concurrently without proper locking.

This allows attacker to:

- apply same coupon multiple times
- before database updates

Result:

- product becomes extremely cheap
- attacker buys expensive jacket using limited store credit

---

## 🔵 WHAT IS THE TOPIC?

### Race Conditions

---

### Simple Definition

Race condition happens when:

multiple requests interact with same data simultaneously

causing unintended behavior.

---

### Core Problem

Application logic usually works like:

1. Check condition
2. Perform action
3. Update database

BUT:

between check and update,
there is tiny vulnerable timing gap.

---

### That Timing Gap Is Called

🧠 Race Window

---

### Race Window Meaning

Tiny time period where:

- multiple requests can pass validation
- before state changes

Usually:

- milliseconds
- microseconds

---

### Real-Life Analogy

Imagine:

only one coupon exists.

Two people redeem:

at exact same time

Both systems check:

```text
"coupon unused?"
```

Both receive:

```text
YES
```

before DB updates.

💥 Coupon applied twice.

---

### Shared State Concept

Race conditions require:

shared server-side state

Examples:

- same account
- same balance
- same cart
- same coupon

---

### Important Term — TOCTOU

```text
Time Of Check To Time Of Use
```

---

### Meaning

Application:

- checks something
- later uses it

Tiny delay exists between:

- checking
- updating

Attacker abuses that gap.

---

## 🔵 TYPES OF REAL-WORLD RACE CONDITIONS

---

### Coupon Reuse

Apply same discount:

multiple times

---

### Gift Card Duplication

Redeem:

same gift card repeatedly

---

### Banking Exploits

Withdraw:

more money than balance

---

### CAPTCHA Reuse

Reuse:

single CAPTCHA token

---

### Rate Limit Bypass

Send:

many login attempts simultaneously

before limiter updates.

---

## 🔵 LAB THEORY

---

### Vulnerable Feature

Coupon application system.

---

### Expected Secure Logic

1. Check coupon unused
2. Apply discount
3. Mark coupon as used

---

### Vulnerable Scenario

Attacker sends:

many coupon requests simultaneously

before step 3 finishes.

---

### Internal Collision

```text
Request A → coupon unused
Request B → coupon unused
Request C → coupon unused

(DB not updated yet)

A applies discount
B applies discount
C applies discount
```

💥 Multiple discounts stack together.

---

# 🔵 LAB WALKTHROUGH (STEP-BY-STEP)

---

## ✅ Step 1 — Login

Credentials:

```text
wiener:peter
```

---

## ✅ Step 2 — Study Coupon System

Buy cheap item first.

Purpose:

- understand request flow

---

## ✅ Step 3 — Observe Coupon Request

Find request:

```http
POST /cart/coupon
```

This request applies discount code.

---

## ✅ Step 4 — Test Normal Behavior

Reuse coupon normally.

Response:

```text
Coupon already applied
```

Meaning:

validation exists.

---

## ✅ Step 5 — Study Cart Session

Send:

```http
GET /cart
```

with and without session cookie.

---

### Observation

Without cookie:

```text
empty cart
```

---

### Meaning

Cart stored:

server-side using session

Important because:

all requests affect SAME cart state.

---

## ✅ Step 6 — Send Coupon Request to Repeater

Send:

```http
POST /cart/coupon
```

to Repeater multiple times.

---

## ✅ Step 7 — Create Many Identical Requests

Goal:

many simultaneous coupon requests

---

### Community Edition Note

Community edition lacks:

- advanced parallel sending
- trigger race conditions feature
- single-packet attack support

So requests are harder to synchronize.

---

## ✅ Step 8 — Send Requests Rapidly

Try sending:

many requests almost simultaneously

Purpose:

make requests collide inside race window.

---

### Expected Vulnerable Flow

```text
A checks coupon → unused
B checks coupon → unused
C checks coupon → unused

(DB unchanged)

A applies discount
B applies discount
C applies discount
```

---

## ✅ Step 9 — Refresh Cart

Now:

multiple discounts may stack.

Order total becomes:

```text
extremely low
```

---

## ✅ Step 10 — Add Leather Jacket

Remove cheap item.

Add:

```text
Lightweight L33t Leather Jacket
```

---

## ✅ Step 11 — Repeat Attack

Apply multiple coupon requests again.

---

## ✅ Step 12 — Purchase Jacket

Once total price:

below store credit

purchase jacket.

✅ Lab solved.

---

# 🔵 REQUEST FLOW VISUALIZATION

```text
Request A arrives
        ↓
Coupon check = unused

Request B arrives
        ↓
Coupon check = unused

(DB still unchanged)

A applies coupon
B applies coupon

DB updates later

💥 Race condition successful
```

---

## 🔵 WHY COMMUNITY EDITION STRUGGLES

Manual clicking:

too slow

Server processes requests sequentially.

So:

- first request succeeds
- others fail

---

### Professional Edition Advantage

Burp Pro supports:

- parallel requests
- last-byte sync
- single-packet attacks

These reduce:

- network jitter
- improve collisions

---

# 🔵 IMPORTANT TERMS

| Term | Meaning |
|---|---|
| Race Condition | Simultaneous request collision |
| Race Window | Vulnerable timing gap |
| Shared State | Same server-side data |
| TOCTOU | Time Of Check To Time Of Use |
| Network Jitter | Random network timing delay |
| Parallel Requests | Requests sent simultaneously |

---

# 🔵 REAL-WORLD SCENARIOS

---

## E-Commerce

Apply:

same discount repeatedly

---

## Banking Apps

Transfer:

more money than available

---

## Gift Cards

Redeem:

same card multiple times

---

## OTP Systems

Reuse:

same verification token

---

## API Rate Limits

Bypass:

anti-bruteforce protections

---

# 🔵 HIGH-VALUE ENDPOINTS

Common vulnerable endpoints:

```http
POST /cart/coupon
POST /redeem
POST /withdraw
POST /transfer
POST /vote
POST /verify-otp
POST /giftcard/redeem
```

---

# 🔵 REMEDIATION

Developers should:

---

## Use Atomic Transactions

Ensure:

all operations happen together safely

---

## Lock Shared Resources

Prevent:

multiple simultaneous modifications

---

## Update State Earlier

Mark:

coupon used immediately

before applying discount.

---

## Serialize Critical Actions

Only allow:

one request at a time

for sensitive operations.

---

## Proper Synchronization

Use:

- mutexes
- DB row locks
- transactional integrity

---

# 🔵 IMPORTANT MENTAL MODEL

```text
Race condition =
multiple requests sneak through validation
before server updates shared state
```

---

# 🔵 ONE-LINE SUMMARY

👉 “This lab exploits a race condition where multiple simultaneous coupon requests pass validation before the database updates, allowing the same discount to be applied many times.”
