# 🔍 Manual Taint Analysis

Taint analysis is the process of **tracking attacker-controlled input** from where it enters the application (**Source**) to where it can cause harm (**Sink**), while checking if any **proper sanitization** stops the attack.

```text
Source → Data Flow → Sanitizer? → Sink
```
![Alt Text](https://github.com/asb1o8/7rY-H4CK-M3/blob/main/09.%29%20PHP-%20Secure%20Code%20review/Img/2.png)
---

# 📥 Sources (Attacker-Controlled Input)

A **Source** is any input that an attacker can influence.

## 🌐 PHP Superglobals

| Source | Description |
|---------|-------------|
| `$_GET` | Query string parameters |
| `$_POST` | Form data |
| `$_REQUEST` | Combined GET, POST, COOKIE |
| `$_COOKIE` | Cookies |
| `$_FILES` | File uploads |

---

## ⚠️ Commonly Overlooked Source

### `$_SERVER`

Although it appears server-side, many values are **user-controlled**.

Examples:
- `User-Agent`
- `Referer`
- `Host`
- `X-Forwarded-For`

Never assume values from `$_SERVER` are trusted.

---

## 📦 Raw Request Body

Applications accepting JSON often read input using:

```php
php://input
file_get_contents('php://input')
```

Framework equivalents:

### Laravel

```php
request()->input()
```

### Symfony

```php
$request->query->get()
```

---

# 📤 Sinks (Dangerous Functions)

A **Sink** is a function where attacker-controlled input can cause harm.

## 💉 SQL Injection

- `mysqli_query()`
- `PDO::query()`
- Raw SQL queries
- `DB::select()`

---

## 💻 Command Injection

- `system()`
- `exec()`
- `shell_exec()`
- `passthru()`
- `popen()`
- `proc_open()`
- Backtick operator (`` `cmd` ``)

---

## ⚡ Code Injection

- `eval()`
- `assert()` (string)
- `create_function()`
- `preg_replace()` with `/e`

---

## 📂 File Inclusion

- `include`
- `require`
- `include_once`
- `require_once`

---

## 📁 File Operations / Path Traversal

- `fopen()`
- `readfile()`
- `file_get_contents()`
- `file_put_contents()`
- `unlink()`
- `move_uploaded_file()`

---

## 📦 Object Injection

- `unserialize()`
- `phar://`

---

## 🌍 SSRF (Server-Side Request Forgery)

- `file_get_contents(URL)`
- `curl_exec()`
- Guzzle HTTP Client

---

## 📄 XXE (XML External Entity)

- `simplexml_load_string()`
- `simplexml_load_file()`
- `DOMDocument::loadXML()`

---

## 🌐 Cross-Site Scripting (XSS)

- `echo`
- `print`
- `printf`
- Template output

---

> ⚠️ A sink **alone is not a vulnerability**. It becomes vulnerable only when **untrusted input reaches it without proper sanitization**.

---

# 🔄 Tracing Data Flow

Example:

```php
public function search(Request $request)
{
    $term = $request->query('q');

    $rows = DB::select(
        "SELECT id, name, sku FROM products WHERE name LIKE '%$term%'"
    );

    return view('reports.search', ['rows' => $rows]);
}
```

## Data Flow

```text
$request->query('q')
        │
        ▼
      $term
        │
        ▼
DB::select(...)
        │
        ▼
SQL Injection Sink
```

No sanitization exists between the source and sink, making the path vulnerable.

---

# 💉 SQL Injection Example

Normal input:

```text
q=widget
```

Query becomes:

```sql
SELECT ...
WHERE name LIKE '%widget%'
```

---

Malicious input:

```text
q=%' UNION SELECT username,password,NULL FROM users -- -
```

Query becomes:

```sql
SELECT ...
WHERE name LIKE '%'
UNION SELECT username,password,NULL FROM users
-- -%'
```

### Result

- Closes the original string.
- Executes attacker-controlled SQL.
- Comments out the remaining query.
- Returns usernames and password hashes.

---

# ❓ Why SQL Injection Works

The database receives **one combined SQL string**.

It **cannot distinguish**:

- Developer-written SQL
- User-supplied input

Special characters (e.g., `'`) modify the **query structure**, causing input to be executed as SQL rather than treated as data.

---

# 🛡️ Sanitization Must Match the Context

Applying **any sanitizer** does **not** guarantee security.

Example:

```php
$id   = $request->query('id');

$safe = htmlspecialchars($id);

DB::select("SELECT * FROM users WHERE id = $safe");
```

### ❌ Problem

`htmlspecialchars()` protects **HTML output**, not SQL queries.

It converts:

```text
<  →  &lt;
>  →  &gt;
"  →  &quot;
```

But it **does not neutralize SQL syntax**, allowing payloads such as:

```text
1 OR 1=1
```

to remain unchanged.

---

### 🚩 Common Sanitization Mistakes
- Wrong Sanitizer: Using an HTML sanitizer for SQL.
- Incomplete Denylists: Blocking only a few dangerous characters.
- Type Casting:

```php
(string)$value
```

Changes the type, **not the content**.

---

- Validation Without Enforcement

```php
if ($valid) {
    // Validation passed
}

DB::query($input);
````````
- Validation exists but does not prevent unsafe input from reaching the sink.

---

# 🎯 The Key Question

For every data flow ask:

> **Does this transformation make the input safe for THIS specific sink?**

Not:

> **Was some sanitizer applied?**

---

# ⚡ Finding Sinks with `ripgrep`

Instead of reading every file manually, use **ripgrep (`rg`)** to locate dangerous functions.

---

## Search for Command Injection

```bash
rg -n "system\(|exec\(|shell_exec\(|passthru\(|popen\(|proc_open\(" .
```

---

## Search for Deserialization

```bash
rg -n "\bunserialize\s*\(" .
```

---

## Why Use `rg`?

✅ Searches entire codebase quickly.

✅ Finds all occurrences of dangerous functions.

✅ Helps build a review worklist.

⚠️ Every match is a **starting point**, **not a confirmed vulnerability**.

You must still trace:

```text
Source
   │
   ▼
Data Flow
   │
   ▼
Sanitizer?
   │
   ▼
Sink
```

to determine if the path is exploitable.

---

# ✅ Key Takeaways

- 📥 **Sources** are attacker-controlled inputs.
- 📤 **Sinks** are dangerous functions where input can cause harm.
- 🔄 **Taint Analysis** follows data from **Source → Sink**.
- 🛡️ A sanitizer is only effective if it matches the sink's context.
- ❌ Incorrect or incomplete sanitization still leaves applications vulnerable.
- 🔍 Use `ripgrep (rg)` to quickly locate sinks and prioritize manual review.
- 🎯 A vulnerability exists **only when an untrusted source reaches a sink without adequate sanitization.**
