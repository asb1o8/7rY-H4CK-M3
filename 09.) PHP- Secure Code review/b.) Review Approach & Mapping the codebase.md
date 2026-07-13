# 🔍 Secure Code Review Methodology

## 🎯 Choosing the Review Approach

The amount of information available determines the type of assessment.

| Approach | Access | Typical Use |
|------------|----------|---------------|
| **White-Box** | Full source code + configuration | Internal audits, code drops, deepest security assurance |
| **Grey-Box** | Partial information (e.g., source without credentials, docs without code) | Time-limited engagements, focused reviews |
| **Black-Box** | No internal access; only application behavior | Production testing, penetration tests, bug bounty |

---

# 📖 Review Strategies

#### 📚 Coverage-Driven Review
- Read the **entire codebase**.
- Best for:
  - Small applications
  - Critical systems
- Maximizes completeness.

#### 🎯 Threat-Driven Review
- Start from:
  - Valuable assets
  - Entry points
- Follow high-risk paths outward.
- Ideal for large codebases where reviewing everything is impractical.

> In practice, most reviews are **time-boxed**, so prioritize the highest-risk code paths.

---

#### 🔄 Source → Sink Model

Secure code review revolves around tracing user-controlled data.

```text
User Input (Source)
        │
        ▼
 Application Logic
        │
 Sanitization?
        │
        ▼
 Dangerous Function (Sink)
```

#### 📥 Source
Where **untrusted input** enters the application.

Examples:
- HTTP requests
- Forms
- Cookies
- Headers
- API parameters

#### 📤 Sink
A function or operation where untrusted data can cause harm.

Examples:
- SQL queries
- File operations
- Command execution
- HTML output

#### 🛡️ Sanitizer
A function intended to make input safe before reaching a sink.

⚠️ A sanitizer is only effective if it is:
- Appropriate for the sink
- Correctly implemented
- Applied to the actual data reaching the sink

---

### 🗂️ Getting Oriented in an Unfamiliar PHP Codebase

Before reviewing code, identify **what you're looking at**.

#### 📦 Step 1: Identify Framework & Dependencies

Read:

- `composer.json`
- `composer.lock`

These files reveal:
- PHP version
- Framework (Laravel, Symfony, etc.)
- Installed packages
- Exact dependency versions

Example:

```json
"require": {
    "php": "^7.3|^8.0",
    "laravel/framework": "^8.x",
    "guzzlehttp/guzzle": "^7.0.1"
}
```

### Why this matters

Knowing the framework immediately tells you:
- Routing structure
- Authentication system
- Security features
- Directory layout

Also look for:
- Vulnerable package versions
- Deprecated libraries
- Dangerous dependencies

Example findings:
- `guzzlehttp/guzzle` → Potential SSRF investigation.
- `facade/ignition` → Known historical Laravel RCE issues.

---

#### ⚙️ Step 2: Review Configuration

Laravel's important configuration locations:

- `.env`
- `config/`

Look for:

✅ Debug enabled

```env
APP_DEBUG=true
```

This exposes detailed error pages.

---

✅ Secret keys

```env
APP_KEY=...
```

The application's cryptographic trust depends on this key.

---

Also inspect:

- Database credentials
- Environment type
- URLs
- Default settings
- Hardcoded secrets
- Misconfigurations

---

#### 🚪 Step 3: Locate Entry Points

Every request in Laravel starts from:

```
public/index.php
```

Routing then forwards requests to:

```
routes/web.php
routes/api.php
```

Routes define the application's attack surface.

---

#### 🗺️ Step 4: Map the Attack Surface

For every route, trace:

```text
Route
   │
   ▼
Controller
   │
   ▼
Model / Service / Helper
   │
   ▼
Potential Sink
```

Example:

```
GET /
    ↓
LoadPreferences
    ↓
unserialize()
```

```
GET /reports/search
      ↓
ReportController@search
      ↓
DB::select()
```

```
GET /admin/users
      ↓
AdminController@users
      ↓
User Model
```
![Alt Text](https://github.com/asb1o8/7rY-H4CK-M3/blob/main/09.%29%20PHP-%20Secure%20Code%20review/Img/1.png)

🔴 Focus on:

- Missing authentication
- Dangerous functions
- Sensitive controllers
- Privileged routes

---

# 🧭 Practical Workflow

A good code review typically follows this order:

1. Identify framework (`composer.json`)
2. Check dependency versions
3. Read configuration (`.env`, `config/`)
4. Find application entry points
5. Map all routes
6. Trace routes → controllers → models/helpers
7. Follow user input to dangerous sinks
8. Record findings and validate them

---

# 💻 Target Environment

The review environment includes:

- Source Code:
  ```
  /var/www/app
  ```

- Editor:
  ```
  Visual Studio Code
  ```

- Search Tool:
  ```
  ripgrep (rg)
  ```

- Local Application:
  ```
  http://localhost:8080
  ```

---

# ✅ Key Takeaways

- White-box reviews provide the highest visibility and assurance.
- Prioritize high-risk code paths in large applications.
- Always think in terms of **Source → Sanitizer → Sink**.
- Start by identifying frameworks and dependencies.
- Review configuration for secrets and insecure defaults.
- Map every route to understand the attack surface.
- Trace data flow from entry point to sink to uncover vulnerabilities.
