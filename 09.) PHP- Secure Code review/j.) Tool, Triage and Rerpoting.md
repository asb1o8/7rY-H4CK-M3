# 🛠️ Secure Code Review Toolchain

Manual code tracing is the **core of secure code review**, but it doesn't scale well for large applications. Static analysis tools help **identify potential vulnerabilities**, while **manual triage** confirms whether they are genuine issues.

---

# 🔧 PHP Security Review Tools

## 🔍 Search Tools

### `grep` / `ripgrep (rg)`

Fast recursive search tools for locating:

- Sources
- Sinks
- Dangerous functions
- Vulnerable patterns

Example:

```bash
rg -n "unserialize\(" .
```

---

## ⚡ Semgrep

Pattern-based and dataflow static analysis.

Features:

- PHP security rules
- Custom rules
- Easy setup
- Great for quick scans

Example:

```bash
semgrep --config=p/php
```

---

## 🌊 Psalm

Static analyzer with **Taint Analysis**.

Tracks:

```text
Source
   │
   ▼
Data Flow
   │
   ▼
Sink
```

Example:

```bash
psalm --taint-analysis
```

---

## 📊 PHPStan

Focuses on:

- Type analysis
- Logic issues
- Hidden programming bugs

Useful for improving code quality and uncovering security-related logic errors.

---

## 🛰️ SonarQube Community Edition

Provides:

- PHP security scanning
- Code quality analysis

Includes technology from the former **RIPS** security scanner.

---

## 🧪 Progpilot

Dedicated **PHP Taint Analysis** tool.

Configurable using YAML:

- Sources
- Sinks
- Sanitizers

Used as a **secondary validation tool**.

---

## 📚 Exakat

Comprehensive PHP auditing tool.

Checks:

- Code quality
- Performance
- Security
- Best practices

Acts as a second opinion during reviews.

---

## 📦 Composer Audit

Checks installed dependencies against public security advisories.

Example:

```bash
composer audit
```

Detects:

- Vulnerable packages
- CVEs
- Recommended upgrades

---

## 📜 Git History

Useful commands:

```bash
git log
```

```bash
git blame
```

Helps identify:

- Recently modified code
- Risky changes
- Original developer
- Change history

---

## 💣 PHPGGC

**PHP Generic Gadget Chains**

Used to verify **Object Injection** findings.

Generates deserialization payloads for:

- Laravel
- Symfony
- WordPress
- Other PHP frameworks

Useful for confirming exploitability.

---

# 🚨 Scanner Results Are NOT Proof

Static analysis tools produce:

- ✅ True Positives
- ❌ False Positives

Many scanners struggle to accurately understand sanitization.

Example:

```php
unserialize($data, [
    'allowed_classes' => false
]);
```

A scanner may still report this as vulnerable, even though deserialization is properly restricted.

---

# 🔎 Triage Process

Every scanner finding should be manually verified.

## ✅ Reachability

Determine whether:

- A real request reaches the sink.
- The vulnerable code is actually executable.

Ignore:

- Dead code
- Unused functions
- Unreachable paths

---

## ⚡ Exploitability

Confirm the vulnerability can actually be exploited.

Avoid assumptions.

Demonstrate it with a working:

- Payload
- Request
- Proof of Concept (PoC)

---

## 🎯 Severity

Evaluate based on:

- Ease of exploitation
- Required privileges
- Impact

Consider:

- Confidentiality
- Integrity
- Availability

---

# 📝 Writing a Security Finding

Every finding should be actionable and reproducible.

---

## 🏷️ 1. Title

Clearly identify:

- Vulnerability type
- Affected component

Example:

```
SQL Injection in Report Search Endpoint
```

---

## 📍 2. Location

Include:

- File path
- Line number
- Route / Endpoint

Example:

```
app/Http/Controllers/ReportController.php:42
```

---

## 🔄 3. Data Flow

Describe:

```text
Source
   │
   ▼
Data Flow
   │
   ▼
Sink
```

Include whether sanitization is:

- Missing
- Incorrect
- Insufficient

---

## 💥 4. Proof of Concept (PoC)

Provide:

- HTTP request
- Payload
- Steps to reproduce

Allows developers to verify the issue.

---

## 🎯 5. Impact

Explain what an attacker gains.

Examples:

- Read database
- Execute commands
- Privilege escalation
- Account takeover

Impact determines severity.

---

## 🛠️ 6. Remediation

Recommend a **specific fix**.

Examples:

✅ Replace:

```php
DB::select(...)
```

with:

```php
Prepared Statements
```

---

✅ Replace:

```php
unserialize(...)
```

with:

```php
unserialize(..., [
    'allowed_classes' => false
]);
```

Avoid vague advice like:

> "Sanitize the input."

---

## 📊 7. Severity Rating

Severity should be justified using:

- Reachability
- Exploitability
- Impact

Many organizations use **CVSS** to score vulnerabilities consistently.

---

# 🔄 Secure Code Review Workflow

```text
🔍 Search
      │
      ▼
🛠️ Static Analysis
      │
      ▼
🔎 Manual Triage
      │
      ▼
🧪 Confirm Exploitability
      │
      ▼
📝 Write Finding
      │
      ▼
🛠️ Recommend Fix
```

---

# 📋 Tool Summary

| 🛠️ Tool | 🎯 Primary Purpose |
|-----------|-------------------|
| 🔍 `grep` / `rg` | Find sources & sinks |
| ⚡ Semgrep | Pattern-based security scanning |
| 🌊 Psalm | Taint analysis |
| 📊 PHPStan | Static type & logic analysis |
| 🛰️ SonarQube | Security & code quality analysis |
| 🧪 Progpilot | Configurable taint analysis |
| 📚 Exakat | PHP auditing & best practices |
| 📦 `composer audit` | Dependency vulnerability scanning |
| 📜 Git (`log`, `blame`) | Code history & ownership |
| 💣 PHPGGC | Deserialization exploit generation |

---

# ✅ Key Takeaways

- 🛠️ Manual code review remains the most reliable technique.
- 🔍 Static analysis tools **assist**, but never replace manual verification.
- 🚨 Treat scanner output as **leads**, not confirmed vulnerabilities.
- 🔎 Validate **reachability**, **exploitability**, and **impact** before reporting.
- 📝 High-quality findings include a clear title, location, data flow, PoC, impact, remediation, and severity.
- 📦 Review third-party dependencies using `composer audit`.
- 💣 Use tools like **PHPGGC** to confirm object injection vulnerabilities.
- 🎯 The goal is not just finding bugs—it is delivering **accurate, reproducible, and actionable security findings**.
