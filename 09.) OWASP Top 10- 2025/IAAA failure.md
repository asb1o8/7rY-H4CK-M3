# IAAA- Identity, Authentication, Authorisation, & Accountability


#### 🌲 Following categories are covered:
- A01: Broken Access Control
- A07: Authentication Failures
- A09: Logging & Alerting Failures




## 🚩 A01: Broken Access Control
> IAAA is a simple way to think about how users and their actions are verified on applications. Each item plays a crucial role and it isn't possible to skip a level. That means, if a previous item isn't being performed, you cannot perform the later times. The four items are:

- Identity - the unique account (e.g., user ID/email) that represents a person or service.
- Authentication - proving that identity (passwords, OTP, passkeys).
- Authorisation - what that identity is allowed to do.
- Accountability - recording and alerting on who did what, when, and from where.

> Weaknesses here can be incredibly detrimental, as it can allow threat actors to either access the data of other users or Privilege Escalation


### 🌲 Privilege Escalation:-

The exploitation of a flaw, misconfiguration, or oversight in a system that allows a user to gain elevated/over access rights beyond what was originally intended.

**Types of Privilege Escalation**:

* `Vertical privilege escalation` → When a user moves upward to higher roles (e.g., from normal user to admin/root).

* `Horizontal privilege escalation` → When a user stays at the same role level but gains access to another user’s data or actions they shouldn’t have.


## 🌲 Broken Access Control:-

It happens when the server doesn’t properly enforce who can access what on every request OR Itrefer to situations where access control mechanisms fail to enforce proper restrictions on user access to resources or data. Some common exploits for broken access control and examples:

* `Horizontal privilege escalation` → It occurs when an attacker can access resources or data belonging to other users with the same level of access. Example, A user might be able to access another user’s account by changing the user ID in the URL.

* `Vertical privilege escalation` → It occurs when an attacker can access resources or data belonging to users with higher access levels. Example, A regular user can access administrative functions by manipulating a hidden form field or URL parameter.
- [Dive into Broken Access Control](https://tryhackme.com/room/owaspbrokenaccesscontrol)


## 🌲 IDOR (Insecure Direct Object Reference):-
- If changing an ID (like ?id=7 → ?id=6) lets you see or edit someone else’s data, access control is broken. can occur when a web server receives user-supplied input to retrieve objects (files, data, documents), too much trust has been placed on the input data, and it is not validated on the server-side to confirm the requested object belongs to the user requesting it.
- [Dive into Insecure Direct Object Refrence](https://tryhackme.com/room/idor)




## 🚩 A07: Authentication Failures
> Authentication failures occur when a system cannot correctly verify a user’s identity during the login or access process.
> Common issues include:
- username enumeration
- weak/guessable passwords (no lockout/rate limits)
- logic flaws in the login/registration flow
- insecure session or cookie handling

If any of these are present, an attacker can often log in as someone else or bind a session to the wrong 
account. 
> Suppose you register on X website with default id and it gives you access to account without proper validation, Why?
- The application exhibited a broken authentication vulnerability due to insecure credential management. Default administrative credentials (aDmiN / password) were left enabled without mandatory rotation or enforcement of password complexity policies. This represents a failure in authentication control, allowing unauthorized entities to bypass identity verification and gain privileged access. The weakness stems from improper hardening of authentication mechanisms, specifically the absence of credential lifecycle management, enforcement of least privilege, and defense‑in‑depth measures.




## 🚩 A09: Logging & Alerting Failures
- When applications don’t record or alert on security-relevant events, defenders can’t detect or investigate attacks. Good logging underpins accountability (being able to prove who did what, when, and from where). In practice, failures look like missing authentication events, vague error logs, no alerting on brute-force or privilege changes, short retention, or logs stored where attackers can tamper with them.

- [Dive into Logging for Accountability](https://tryhackme.com/room/loggingforaccountability)

##

* `Source:` [TryHackMe](https://tryhackme.com/room/owasptopten2025one)
