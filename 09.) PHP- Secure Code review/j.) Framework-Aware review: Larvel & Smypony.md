# 🏗️ Framework-Aware Secure Code Review

Frameworks like **Laravel** and **Symfony** provide built-in security features, but developers can bypass or misconfigure them. During a review:

- ✅ Verify built-in protections are enabled.
- 🚨 Identify "escape hatches" that bypass those protections.

---

# 🔐 Access Control

Frameworks provide authentication and authorization, but developers must **explicitly apply** them.

## 🟢 Laravel
- `auth` middleware → Authentication
- Policies → Authorization

## 🟢 Symfony
- `access_control` (`security.yaml`)
- Voters

### 🚨 Common Issue

Sensitive routes are left **unprotected**.

Example:

```php
Route::middleware('auth')->group(function () {
    Route::get('/dashboard', ...);
});

Route::get('/admin/users', ...); // ❌ No authentication
```

### 🔍 Review Checklist

- Missing `auth` middleware?
- Missing authorization policies?
- Sensitive controllers publicly accessible?

---

# 💉 SQL Injection Through ORM

ORMs are secure **only when parameterized queries are used**.

## ✅ Safe

- Laravel **Eloquent**
- Symfony **Doctrine**

---

## 🚨 Dangerous Raw Queries

### Laravel

- `DB::raw()`
- `whereRaw()`
- `selectRaw()`
- `orderByRaw()`
- `DB::select()`

### Symfony

- Raw DQL
- Native SQL
- String-built queries

### 🔍 Review Checklist

Trace user-controlled input reaching any **raw query helper**.

---

# 🌐 Output & Templating

Template engines escape output by default to prevent **XSS**.

## ✅ Safe

### Blade

```php
{{ $value }}
```

### Twig

Auto-escaping enabled.

---

## 🚨 Dangerous

### Blade

```php
{!! $value !!}
```

### Twig

```twig
{{ value|raw }}
```

These bypass output encoding and may introduce **Cross-Site Scripting (XSS)**.

---

# ⚡ Server-Side Template Injection (SSTI)

Occurs when **user input becomes part of the template itself**, rather than just being displayed.

Example:

```php
Twig::createTemplate($userInput)
```

### 🚨 Impact

- Template Injection
- Server-side code execution

---

# 🛡️ Cross-Site Request Forgery (CSRF)

Both frameworks use **CSRF tokens** to protect state-changing requests.

## 🟢 Laravel

- `VerifyCsrfToken`
- `@csrf`

## 🟢 Symfony

- Form Component
- CSRF Token APIs

### 🔍 Review Checklist

Check whether state-changing routes are excluded from protection.

Laravel example:

```php
VerifyCsrfToken::$except
```

---

# ⚙️ Debug & Configuration

Debug mode must **never** be enabled in production.

## 🚨 Laravel

```env
APP_DEBUG=true
```

## 🚨 Symfony

```env
APP_ENV=dev
```

### Risks

- Detailed stack traces
- Internal information disclosure
- Debug tooling exposure

---

# 🔑 Sensitive Secrets

### Laravel

```env
APP_KEY
```

### Symfony

```env
APP_SECRET
```

These secrets establish the application's trust model.

---

# 📦 Laravel Object Injection

Laravel's:

```php
decrypt()
```

internally calls:

```php
unserialize()
```

### 🚨 Risk

If `APP_KEY` is compromised:

- Forge encrypted payloads.
- Trigger unsafe deserialization.
- Achieve Object Injection.

### Related CVE

- **CVE-2018-15133**

---

# 💥 Vulnerable Debug Package

Package:

```
facade/ignition
```

Affected versions:

```
< 2.5.2
```

### CVE

- **CVE-2021-3129**

### Impact

- Unauthenticated Remote Code Execution (RCE)
- Requires debug mode.

---

# 📝 Mass Assignment

Mass Assignment occurs when request data directly populates model attributes.

## 🟢 Laravel

Protected using:

- `$fillable`
- `$guarded`

### 🚨 Misconfigurations

```php
protected $guarded = [];
```

or

Overly permissive:

```php
protected $fillable = [...];
```

Attackers may modify sensitive fields such as:

- `is_admin`
- `role`
- `balance`

---

## 🟢 Symfony

Uses **Form Types** with explicit field binding.

### 🔍 Review Checklist

Verify only intended model attributes can be modified.

---

# 📦 Dependency Security

Third-party libraries are part of the application's attack surface.

Use:

```bash
composer audit
```

to identify vulnerable dependencies.

Example:

```
Package:
facade/ignition

CVE:
CVE-2021-3129

Affected:
<2.5.2
```

### 🔍 Record

- Package
- Installed version
- CVE
- Upgrade recommendation

---

# ✅ Framework Review Checklist

## 🔐 Access Control

- Authentication enforced?
- Authorization implemented?
- Public admin routes?

---

## 💉 Database

- Raw SQL helpers used?
- Parameterized queries bypassed?

---

## 🌐 Templates

- Unescaped output?
- `|raw` or `{!! !!}` used?
- SSTI possible?

---

## 🛡️ CSRF

- CSRF tokens present?
- State-changing routes protected?
- CSRF exclusions justified?

---

## ⚙️ Configuration

- Debug mode disabled?
- Secrets protected?

---

## 📦 Deserialization

- `decrypt()` → `unserialize()` reachable?
- `APP_KEY` compromise impact?

---

## 📝 Mass Assignment

- `$fillable` overly permissive?
- `$guarded` empty?
- Sensitive fields modifiable?

---

## 📚 Dependencies

- Run `composer audit`.
- Check vulnerable packages.
- Record associated CVEs.

---

# 🎯 Key Takeaways

- 🏗️ Frameworks provide **secure defaults**, but developers can bypass them.
- 🔐 Missing authentication or authorization is a common framework-specific issue.
- 💉 Raw ORM queries negate built-in SQL Injection protection.
- 🌐 Unescaped template output can lead to **XSS**, while user-controlled templates may result in **SSTI**.
- 🛡️ Ensure CSRF protection covers all state-changing endpoints.
- ⚙️ Disable debug mode and protect framework secrets (`APP_KEY`, `APP_SECRET`).
- 📦 Review mass assignment settings to prevent unauthorized attribute modification.
- 🔍 Treat vulnerable dependencies as **first-class security findings** using `composer audit`.
