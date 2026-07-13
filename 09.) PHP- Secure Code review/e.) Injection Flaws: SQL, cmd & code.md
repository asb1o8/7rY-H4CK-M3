# 💉 Injection Vulnerabilities

Injection vulnerabilities occur when **attacker-controlled input is interpreted as part of a command** instead of being treated as **data**.

```text
User Input
    │
    ▼
Command String
    │
    ▼
Interpreter (SQL / Shell / PHP)
    │
    ▼
Arbitrary Command Execution
```

> **Root Cause:** Untrusted input reaches an interpreter without proper separation or sanitization.

---

# 🗄️ SQL Injection (SQLi)

## 📖 What is it?

Occurs when user input is directly embedded into an SQL query.

### Vulnerable Flow

```text
Source
$request->query('q')
        │
        ▼
DB::select()
        │
        ▼
SQL Database
```

### Vulnerable Code

```php
$term = $request->query('q');

DB::select("SELECT * FROM products WHERE name LIKE '%$term%'");
```

### Exploit

```sql
%' UNION SELECT username,password,NULL FROM users -- -
```

### Result

- Reads unauthorized database records.
- Dumps usernames and password hashes.
- Alters the intended SQL query.

---

## 💻 Command Injection

### What is it?

Occurs when user input is passed directly to the **operating system shell**.

### Common PHP Sinks

- `system()`
- `exec()`
- `shell_exec()`
- `passthru()`
- `popen()`
- `proc_open()`
- Backtick operator (`` `cmd` ``)

---

### 🔄 Vulnerable Flow

```text
Source
$request->query('host')
        │
        ▼
system()
        │
        ▼
Operating System Shell
```

### Vulnerable Code

```php
$host = $request->query('host');

system("ping -c 1 " . $host);
```

### 🧨 Exploit

Normal input

```text
127.0.0.1
```

Runs

```bash
ping -c 1 127.0.0.1
```


Malicious input

```text
127.0.0.1; id
```

Runs

```bash
ping -c 1 127.0.0.1; id
```

### Result

- Executes the intended command.
- Executes attacker-controlled commands.
- Reveals the web server user (`id`).



## 🛡️ Defenses

### ✅ `escapeshellarg()`

- Escapes user input.
- Treats input as **one argument**.
- Recommended for user-supplied values.



### ⚠️ `escapeshellcmd()`

- Escapes shell metacharacters.
- Does **not** prevent argument injection.
- Frequently misused.



### 🏆 Best Practice

Avoid invoking the shell altogether.

Use APIs like:

```php
proc_open()
```

with an **argument array** instead of constructing shell command strings.



### ⚡ Code Injection

## 📖 What is it?

Occurs when user input is executed as **PHP code**.

### Dangerous PHP Sinks

- `eval()`
- `assert()` *(string arguments in older PHP versions)*
- `create_function()` *(removed in PHP 8.0)*
- `preg_replace()` with `/e` *(removed in PHP 7.0)*



### 🔄 Vulnerable Flow

```text
Source
$request->query('expr')
        │
        ▼
eval()
        │
        ▼
PHP Interpreter
```

### Vulnerable Code

```php
$expr = $request->query('expr');

eval("\$result = " . $expr . ";");
```



### 🧨 Exploit

Normal input

```text
2+2
```

Produces

```text
4
```

---

Malicious input

```php
system('id')
```

Executed as

```php
$result = system('id');
```

### Result

- Executes arbitrary PHP code.
- Achieves remote code execution (RCE).
- Can fully compromise the server.



### 📌 PHP Version Notes

| ⚠️ Feature | Status |
|------------|--------|
| `preg_replace('/e')` | ❌ Removed in PHP 7.0 |
| `create_function()` | ❌ Removed in PHP 8.0 |
| `assert(string)` | ⚠️ Deprecated in PHP 7.2, removed in PHP 8.0 |
| `eval()` | 🚨 Still dangerous; never use with user input |

---

### 🔍 Identifying Injection During Code Review

Look for this pattern:

```text
📥 Source
      │
      ▼
String Concatenation
      │
      ▼
Interpreter
(SQL / Shell / PHP)
```

If untrusted input reaches an interpreter, assume **Injection** until proven otherwise.

---

### 🛡️ General Prevention

- Use parameterized SQL queries (prepared statements).
- Never concatenate user input into commands.
- Use context-appropriate sanitization.
- Avoid dangerous functions (`eval()`, `system()`, etc.).
- Pass arguments directly instead of building command strings.
- Treat all user input as untrusted.

---

### 📝 Key Takeaways

- **Injection** occurs when input is interpreted as **commands**, not data.
- **SQL Injection** manipulates database queries.
- **Command Injection** executes operating system commands.
- **Code Injection** executes arbitrary PHP code.
- All three vulnerabilities share the same root cause:
  - **Untrusted input + Dangerous interpreter + No proper separation/sanitization**.
- The safest approach is to **avoid constructing executable command strings from user input altogether.**
