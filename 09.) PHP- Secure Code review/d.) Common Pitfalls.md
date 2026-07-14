# ⚠️ PHP Language Behaviors That Lead to Vulnerabilities

Some PHP language features can silently introduce security vulnerabilities. These often look harmless, making them easy to miss during code reviews and static analysis.

---

# ⚖️ Loose Comparison & Type Juggling

PHP has two equality operators:

| Operator | Behavior |
|----------|----------|
| `===` | Strict comparison (value + type) ✅ |
| `==` | Loose comparison (automatic type conversion) ⚠️ |

## 🔄 Type Juggling

When using `==`, PHP converts operands to a common type before comparison.

### 💥 Magic Hash Vulnerability

Certain hashes begin with:

```text
0e123456789...
```

PHP interprets these as scientific notation:

```text
0e12345 == 0e67890
```

↓

```text
0 == 0
```

↓

```php
true
```

This allows **authentication or token bypasses** if hashes are compared using `==`.

### ❌ Vulnerable

```php
return $provided == $expected;
```

### ✅ Secure

```php
return $provided === $expected;
```

---

## 📝 PHP Version Differences

### PHP 8 Improvements

- Number ↔ Non-numeric string comparisons are safer.
- `strcmp(array, string)` now throws a `TypeError`.

### Still Vulnerable

- **Magic Hashes** still work because both values are numeric strings.

---

## ⚠️ Other Loose Comparison Pitfalls

Functions using `==` internally:

- `in_array()` (default mode)
- `switch`
- Loose `strcmp()` checks (older PHP)

Always enable **strict comparisons** when possible.

---

# 🧨 Variable Handling Footguns

Some PHP functions create or overwrite variables automatically.

---

## 📥 `extract()`

Converts array keys into variables.

### ❌ Example

```php
$isAdmin = false;

extract($_REQUEST);

if ($isAdmin) {
    // Admin code
}
```

Attack:

```text
?isAdmin=1
```

↓

```php
$isAdmin = true
```

Result:

- Privilege escalation
- Variable overwrite

---

## 📥 `parse_str()`

Old behavior (PHP < 8):

```php
parse_str($_SERVER['QUERY_STRING']);
```

Creates variables directly from user input.

---

## 📥 Variable Variables

```php
$$name
```

Allows attackers to decide **which variable** gets modified.

---

# 🎲 Weak Randomness

Security tokens must be **cryptographically unpredictable**.

## ❌ Insecure Functions

- `rand()`
- `mt_rand()`
- `uniqid()`

These are predictable.

---

### ❌ Vulnerable

```php
$token = md5(
    uniqid(
        mt_rand(),
        true
    )
);
```

Attackers may predict generated tokens.

---

### ✅ Secure

```php
bin2hex(random_bytes(32));
```

or

```php
random_int()
```

These use cryptographically secure randomness.

---

# 🪄 Magic Methods & Deserialization

PHP automatically calls certain methods during an object's lifecycle.

Common magic methods:

- `__construct()`
- `__wakeup()`
- `__destruct()`
- `__toString()`
- `__call()`

---

## ⚠️ Dangerous Combination

```text
unserialize()
        +
Magic Methods
```

Example:

```php
class TempFile {

    public $path;

    public function __destruct() {
        unlink($this->path);
    }

}
```

If an attacker controls:

```php
unserialize($input)
```

They can create:

```text
TempFile
```

with:

```text
path=/etc/passwd
```

When the object is destroyed:

```php
unlink("/etc/passwd");
```

executes automatically.

---

## 🔍 Review Tip

Whenever you find:

```php
unserialize()
```

Immediately inspect loaded classes for dangerous magic methods.

---

# 🌐 PHP Stream Wrappers

PHP file functions support special URI wrappers.

These can greatly increase the impact of file-related vulnerabilities.

---

## 📖 `php://filter`

Reads files after applying filters.

Often used to leak PHP source code.

---

## 📄 `data://`

Embeds data directly inside the URL.

Useful for bypassing file inclusion restrictions.

---

## 📦 `phar://`

Treats a PHAR archive like a filesystem.

Can trigger **deserialization** during normal file operations.

---

## 💻 `expect://`

Executes system commands (if enabled).

Potential **Remote Command Execution (RCE)**.

---

## ⚠️ Why Wrappers Matter

Any sink accepting a file path:

- `include`
- `require`
- `fopen`
- `file_get_contents`

may actually receive:

```text
php://filter/...
```

instead of

```text
file.txt
```

Always inspect file operations for wrapper abuse.

---

# 🚨 Error Suppression

PHP suppresses errors using:

```php
@
```

Example:

```php
@unserialize($data);
```

Errors are hidden.

This may conceal:

- Failed deserialization
- Exploitation attempts
- Security issues

Treat the `@` operator on sensitive functions as a **red flag**.

---

# 🔍 Code Review Checklist

When reviewing PHP code, always look for:

### ⚖️ Comparisons

- `==`
- `!=`

Prefer:

- `===`
- `!==`

---

### 📥 Variable Handling

- `extract()`
- `parse_str()`
- `$$variable`

---

### 🎲 Randomness

Avoid:

- `rand()`
- `mt_rand()`
- `uniqid()`

Use:

- `random_bytes()`
- `random_int()`

---

### 🪄 Deserialization

Search for:

```php
unserialize()
```

Then inspect:

- `__destruct()`
- `__wakeup()`
- `__call()`
- `__toString()`

---

### 🌐 Stream Wrappers

Watch for:

- `php://filter`
- `data://`
- `phar://`
- `expect://`

---

### 🚨 Error Suppression

Search for:

```php
@
```

Especially before:

- `unserialize()`
- `include`
- `eval()`
- File operations

---

# ✅ Key Takeaways

- ⚖️ Use **strict comparisons (`===`)** to prevent type juggling vulnerabilities.
- 🧨 Avoid functions that overwrite variables (`extract()`, `parse_str()`, `$$var`).
- 🎲 Generate security tokens with **cryptographically secure** functions.
- 🪄 Treat `unserialize()` + magic methods as a high-risk combination.
- 🌐 Always consider PHP stream wrappers when reviewing file operations.
- 🚨 The `@` operator hides errors and should be treated as suspicious in security-sensitive code.
- 🔍 Many exploitable vulnerabilities arise from PHP language behavior rather than obvious coding mistakes.
