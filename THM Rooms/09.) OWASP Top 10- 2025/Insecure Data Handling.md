# Insecure Data Handling

#### 🌲 Following categories are covered:
- A04: Cryptographic Failures
- A05: Injection
- A08: Software or Data Integrity Failures


## 🚩 A04: Cryptographic Failures

##### What are Cryptographic Failures?
- Cryptographic failures happen when sensitive data isn't adequately protected due to lack of encryption, 
faulty implementation, or insufficient security measures. This includes storing passwords without hashing, 
using outdated or weak algorithms (such as MD5, SHA1, or DES), exposing encryption keys, or failing to secure 
data during transmission.

- An incredible example of this is an application or service "rolling their own cryptography", rather than using well-established, vetted, and verifiably secure encryption algorithms.


##### How to prevent cryptographic failures ?
- Preventing cryptographic failures starts with choosing strong, modern algorithms and implementing them properly.
  Sensitive information such as passwords should be hashed using robust, slow hashing functions like bcrypt, scrypt,
  or Argon2. When encrypting data, avoid creating your own algorithms; instead, rely on trusted, industry-standard
  libraries.
- Never embed access credentials (i.e., to a third-party service) in source code, configuration files, or repositories.
- Instead, use secure key management systems or environments specifically designed for storing secrets.


## 🚩 A05: Injection

##### What is Injection?
- Injection occurs when an application takes user input and mishandles it. Instead of processing the input
  securely, the application passes it directly into a system that can execute commands or queries, such as
  a database, a shell, a templating engine or API.
- You are likely familiar with SQL Injection, where an attacker inserts an SQL query into an application's
  logic, such as a login form, which then gets processed by the database. This happens when the web application
  fails to sanitise user input and instead uses it to construct the query. For instance, taking the "username"
  input on a login form and directly using it to query the database.

The following are some classic examples of injection that you may be familiar with:
- SQL Injection
- Command Injection
- AI Prompts
- Server Side Template Injection (SSTI)

Unfortunately, even in 2025, these types of attacks remain relevant, as evidenced by the inclusion of injection on the OWASP top ten list, not just once in 2021, but twice by 2025. Injection is of high severity and should be treated accordingly.


##### How to prevent Injection ?
- Preventing injection starts by ensuring that user input is always treated as untrusted. Rather than parsing
  directly, instead, take elements of the input for querying. For SQL queries, this means using prepared
  statements and parameterised queries instead of building queries through string concatenation. For OS commands,
  avoid functions that pass input directly to the system shell, and instead rely on safe APIs and processes that
  don’t invoke the shell at all.
- Input validation and sanitisation play a crucial role in preventing these types of attack. Escape dangerous
  characters, enforce strict data types and filter before the application even processes the input.



## 🚩 A08: Software or Data Integrity Failures

##### What are Software or Data Integrity failures?
- Software or Data Integrity Failures occur when an application relies on code, updates, or data it assumes are
  safe, without verifying their authenticity, integrity, or origin. This includes trusting software updates
  without verification, loading scripts or configuration files from untrusted sources, failing to validate data
  that impacts application logic, or accepting data such as binaries, templates, or JSON files without confirming
  whether it has been altered.
  

##### How to avoid Software & Data Integrity failures ?
- Preventing these failures begins with establishing trust boundaries. Applications should never assume that code,
  updates, or key pieces of data are legitimate and automatically trusted; their integrity must be verified. This
  involves using methods such as cryptographic checks (like checksums) for update packages and ensuring that only
  trusted sources can modify critical artefacts.
- Additionally, for applications, integrity and trust boundaries should also be within build processes such as CI/CD.

#
* `Source:` [TryHackMe](https://tryhackme.com/room/owasptopten2025three)
