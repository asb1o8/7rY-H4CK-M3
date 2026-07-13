# 🔍 Secure Code Review

## 📖 What is Secure Code Review?

Secure code review is the process of examining an application's **source code** to identify:
- Security vulnerabilities
- Their root causes
- Whether they are exploitable

It is a **White-Box Testing** technique because the reviewer has full access to the application's source code.

---

# ⚖️ White-Box vs Black-Box Testing

## 🟢 White-Box Testing
- Full access to source code.
- Can trace exact program execution.
- Identifies root causes directly.
- More accurate and efficient for finding deep vulnerabilities.

## ⚫ Black-Box Testing
- No source code access.
- Relies only on application responses.
- Must infer internal behaviour.
- Often requires more guessing and experimentation.

---

# 🔄 Core Concept: Taint Analysis

Most web vulnerabilities follow the same pattern:

```text
Untrusted Input (Source)
        │
        ▼
 Application Processing
        │
 (Sanitization/Validation?)
        │
        ▼
 Dangerous Function (Sink)
```

Secure code review focuses on **tracking untrusted data** from its **Source** to a **Sink** to determine whether it can be abused.

This process is called **Taint Analysis**, and it is the foundation of secure code review.

---

# 🧩 Methodology

Secure code review follows a **systematic process**, not a collection of payloads.

The same methodology applies when reviewing:
- 💼 Commercial code reviews
- 🌍 Open-source projects
- 🛠️ Your own applications before deployment

---

# 🌐 Why PHP?

PHP remains one of the most widely used web languages.

Common ecosystems include:
- WordPress
- Drupal
- Magento
- Laravel
- Symfony
- Custom PHP applications

Although frameworks differ, the review principle remains the same:

> **Trace untrusted data from Source → Sink.**

---

# 🎯 Learning Objectives

By the end of the module, you should be able to:

## 📚 Fundamentals
- Understand secure code review.
- Differentiate White-Box and Black-Box testing.
- Know when each approach is appropriate.

## 🗺️ Codebase Recon
- Identify frameworks and dependencies.
- Read application configuration.
- Map the application's attack surface.

## 🔍 Data Flow Analysis
- Identify PHP Sources and Sinks.
- Categorize sinks by vulnerability type.
- Trace data flow through the application.
- Determine whether sanitization actually mitigates risk.

## ⚠️ PHP Security Pitfalls
Recognize common issues such as:
- Loose comparison (`==`)
- Weak randomness
- Dangerous variable-handling functions
- PHP stream wrappers

## 🛡️ Vulnerability Identification
Detect vulnerabilities including:
- SQL Injection
- Cross-Site Scripting (XSS)
- File Inclusion
- Path Traversal
- File Upload flaws
- Insecure Deserialization
- Server-Side Request Forgery (SSRF)
- XML External Entity (XXE)

## 🏗️ Framework-Aware Reviews
Perform reviews for:
- Laravel
- Symfony

## 🔧 Tooling
- Use static analysis tools effectively.
- Triage findings.
- Write clear and actionable security reports.
