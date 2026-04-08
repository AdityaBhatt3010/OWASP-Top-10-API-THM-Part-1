# 🛡️ OWASP API Security Top 10 (Part 1) — TryHackMe Walkthrough

Lab: [https://tryhackme.com/room/owaspapisecuritytop105w](https://tryhackme.com/room/owaspapisecuritytop105w)

![](OWASP_API_1/Cover.jpeg)

---

# 📌 Task 1 — Introduction

### 🧠 What’s Happening

You’re basically spinning up a **Windows VM + Talend API Tester + vulnerable Laravel app**.

➡️ This environment is pre-configured for testing API vulnerabilities.

### ✅ Outcome

* Connected to VM successfully
* Tools auto-launched
* Ready for API testing

---

# 📌 Task 2 — Understanding APIs

### 🧠 Key Idea

API = bridge between applications.

* Client sends request
* Server responds
* Defined via API documentation

👉 APIs are **core building blocks of modern apps**

---

### 🚨 Real Breaches (Important Insight)

* LinkedIn → 700M users scraped via API
* Twitter → 5.4M users exposed
* PIXLR → 1.9M records leaked

➡️ Lesson: **APIs = high-value attack surface**

---

### ✅ Answers

* Sample records → **1 million**
* API docs useless? → **No (nay)**

---

# 📌 Task 3 — BOLA (Broken Object Level Authorization)

### 🧠 Concept

API exposes data using IDs but **doesn’t check who is requesting**.

➡️ ID change = data leak

---

## ⚔️ PoC

### Step 1 — Hit Vulnerable Endpoint

```
GET /apirule1_v/user/1
```

➡️ Returns user data without any auth check.

![](OWASP_API_1/1.png)

---

### Step 2 — ID Enumeration

```
GET /apirule1_v/user/2
```

➡️ Just increment ID → access other users.

![](OWASP_API_1/2.png)

---

### Step 3 — Extract Data

* Total employees → **3**
* Flag (ID=2) → **THM{838123}**
* Username (ID=3) → **Bob**

---

### ⚠️ Why Vulnerable

* No authorization check
* Predictable IDs
* Direct object reference

---

### 🔐 Fix

* Authorization tokens
* Role validation
* Use UUIDs

---

# 📌 Task 4 — Broken User Authentication (BUA)

### 🧠 Concept

Login system is flawed — **password not validated**.

---

## ⚔️ PoC

### Step 1 — Login with Only Email

```
POST /apirule2/user/login_v
```

```
email=hr@mht.com&password=anything
```

➡️ Login works even with wrong password 💀

![](OWASP_API_1/3.png)

---

### Step 2 — Get Token

```
cOC%Aonyis%H)mZ&uJkuI?_W#4&m>Y
```

➡️ Token issued without proper auth.

---

### Step 3 — Use Token

```
GET /apirule2/user/details
Authorization-Token: <token>
```

➡️ Full account takeover.

---

### ⚠️ Why Vulnerable

* SQL checks only email
* Password ignored
* Token issued blindly

---

### 🔐 Fix

* Validate password properly
* Use hashing (bcrypt)
* MFA + JWT

---

### ✅ Extra Answer

* GET request for creds? → **No (nay)**

---

# 📌 Task 5 — Excessive Data Exposure

### 🧠 Concept

API returns **too much information**, expecting frontend to filter.

➡️ Attacker intercepts raw response → gets secrets.

---

## ⚔️ PoC

### Step 1 — Fetch Comment

```
GET /apirule3/comment_v/2
```

➡️ Returns full dataset including hidden fields.

![](OWASP_API_1/6.png)

---

### Step 2 — Extract Sensitive Data

* Device ID → **iOS15.411**

---

### Step 3 — Another Record

```
GET /apirule3/comment_v/3
```

➡️ Username → **hacker#!**

![](OWASP_API_1/7.png)

---

### ⚠️ Why Vulnerable

* Backend sends everything
* No filtering
* Trusting frontend

---

### 🔐 Fix

* Return minimal data
* Avoid generic serializers
* Validate API responses

---

### ✅ Answer

* Network-level fix only? → **No (nay)**

---

# 📌 Task 6 — Lack of Resources & Rate Limiting

### 🧠 Concept

No request limits → attackers can spam endpoints.

➡️ Leads to DoS or financial abuse.

---

## ⚔️ PoC

### Step 1 — Send OTP

```
POST /apirule4/sendOTP_s
email=hr@mht.com
```

➡️ Response → **200**

![](OWASP_API_1/8.png)

---

### Step 2 — Invalid Email

```
POST /apirule4/sendOTP_s
email=sale@mht.com
```

```
Invalid Email
```

![](OWASP_API_1/9.png)

---

### ⚠️ Why Vulnerable

* No rate limiting
* Unlimited requests
* Resource exhaustion

---

### 🔐 Fix

* Rate limiting (time-based)
* CAPTCHA
* Request quotas

---

### ✅ Answer

* Rate limiting at network level? → **Yes (yea)**

---

# 📌 Task 7 — Broken Function Level Authorization

### 🧠 Concept

User can escalate privileges by manipulating request parameters.

---

## ⚔️ PoC

### Step 1 — Send Admin Request

```
GET /apirule5/users_v
Authorization-Token: YWxpY2U6dGVzdCFAISM6Nzg5Nzg=
isAdmin: 1
```

➡️ Normal user accesses admin data 😶

---

### Step 2 — Extract Data

* Alice mobile → **+1235322323**
* Admin flag → **THM{3432$@#2!}**

---

### ⚠️ Why Vulnerable

* Trusting client input (`isAdmin`)
* No backend role validation

---

### 🔐 Fix

* Enforce RBAC server-side
* Ignore client-controlled role fields

---

### ✅ Answer

* isAdmin in hidden field safe? → **No (nay)**

---

# 📌 Task 8 — Conclusion

### 🧠 What You Learned

This lab basically drills 5 core API failures:

1. BOLA → ID-based data leaks
2. BUA → Broken login logic
3. Data Exposure → Too much data returned
4. No Rate Limiting → Abuse possible
5. Function Auth → Privilege escalation

![](OWASP_API_1/10.png)

---

# 🧾 Final Take (Important)

👉 APIs fail mainly due to **trust assumptions**:

* Trusting IDs → BOLA
* Trusting login → BUA
* Trusting frontend → Data leak
* No limits → Abuse
* Trusting user roles → Admin bypass

---

## 👋 Outro

If this helped, connect here:

* GitHub: [https://github.com/AdityaBhatt3010](https://github.com/AdityaBhatt3010)
* LinkedIn: [https://www.linkedin.com/in/adityabhatt3010/](https://www.linkedin.com/in/adityabhatt3010/)
* Medium: [https://medium.com/@adityabhatt3010](https://medium.com/@adityabhatt3010)

More writeups soon — cleaner, deeper, and slightly unhinged 🗿🔥

---
