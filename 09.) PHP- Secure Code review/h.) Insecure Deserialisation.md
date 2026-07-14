# 📦 PHP Deserialization & Object Injection

## 🎯 What is Serialization?

- **Serialization** converts a PHP object/value into a **storable string**.
- **Deserialization (`unserialize()`)** converts the string back into its original PHP object/value.

```text
PHP Object
    │
serialize()
    │
    ▼
Serialized String
    │
unserialize()
    │
    ▼
PHP Object
```

---

# ⚠️ Why `unserialize()` is Dangerous

A vulnerability occurs when:

- User-controlled data is passed to `unserialize()`.
- The attacker controls:
  - The object's **class**
  - Its **properties**
- PHP automatically executes **magic methods**, potentially leading to **Object Injection** and even **Remote Code Execution (RCE)**.

---

# 🪄 Automatic Magic Methods

When an object is restored, PHP may automatically invoke:

| 🪄 Magic Method | Trigger |
|---------------|---------|
| `__wakeup()` | During deserialization |
| `__destruct()` | When the object is destroyed |

These methods execute **without explicit calls**, making them ideal targets for attackers.

---

# 🔗 Gadget Chain

A **Gadget Chain** is a sequence of existing class methods that an attacker abuses to reach a dangerous operation.

```text
Attacker Input
      │
      ▼
unserialize()
      │
      ▼
__wakeup()
      │
      ▼
Another Magic Method
      │
      ▼
Dangerous Function
      │
      ▼
💥 Command Execution / RCE
```

> ⚠️ Individual classes are not malicious—the attacker chains their side effects together.

---

# 🛡️ Best Defense

Always restrict object creation:

```php
unserialize($data, ['allowed_classes' => false]);
```

This:

- ✅ Restores only primitive data (arrays, strings, integers, etc.)
- ❌ Prevents object creation
- ❌ Prevents magic methods from executing
- ❌ Blocks Object Injection

---

# 🔍 What to Check During Code Review

Whenever you find `unserialize()`:

### ✅ Check 1

Is the input **attacker-controlled**?

Examples:
- Cookies
- GET/POST parameters
- Files
- API requests

---

### ✅ Check 2

Does it use:

```php
['allowed_classes' => false]
```

If **No** → Potential vulnerability.

If **Yes** → Usually safe from Object Injection.

---

# 📦 `phar://` Deserialization

Object Injection isn't always obvious.

PHP's **Phar archives** contain **serialized metadata**.

Many file functions automatically deserialize this metadata when given a:

```text
phar://
```

path.

---

## ⚠️ Dangerous File Functions

Functions that may trigger deserialization:

- `file_exists()`
- `fopen()`
- `getimagesize()`
- `include`
- `require`

Example:

```text
file_exists("phar://evil.phar/file")
```

Even without calling `unserialize()`, PHP may deserialize the Phar metadata.

---

# 🔍 Reviewing Deserialization

Search the codebase:

```bash
rg -n "\bunserialize\s*\(" .
```

Example results:

```text
LoadPreferences.php
CacheReader.php
```

---

# 🚨 True Positive

```php
$prefs = unserialize($_COOKIE['prefs']);
```

### Problems

- 📥 Source: `$_COOKIE`
- ❌ No `allowed_classes`
- ⚠️ Attacker controls object creation

**Verdict:** Vulnerable to Object Injection.

---

# ✅ False Positive

```php
$data = unserialize(
    $blob,
    ['allowed_classes' => false]
);
```

Although scanners flag this:

- ❌ No objects are created
- ❌ Magic methods never execute

**Verdict:** Safe from Object Injection.

---

# 🔍 Triaging Findings

Not every `unserialize()` call is exploitable.

Ask:

1. 📥 Is the data attacker-controlled?
2. 🛡️ Are objects allowed?
3. 🪄 Can magic methods execute?

Only if all answers indicate risk is it a **true vulnerability**.

---

# ✅ Recommended Mitigations

### 🥇 Best Option

Avoid deserializing untrusted input.

Use safer formats such as:

- JSON
- XML (securely parsed)
- Plain arrays

---

### 🛡️ If `unserialize()` is Required

Always use:

```php
['allowed_classes' => false]
```

to prevent object creation.

---

# 📌 Key Takeaways

- 📦 `serialize()` converts objects into strings.
- 📥 `unserialize()` restores objects.
- ⚠️ User-controlled input to `unserialize()` can lead to **Object Injection**.
- 🪄 Magic methods like `__wakeup()` and `__destruct()` execute automatically.
- 🔗 Attackers build **Gadget Chains** to achieve RCE.
- 🛡️ `['allowed_classes' => false]` is the primary defense.
- 📦 `phar://` can trigger hidden deserialization through normal file operations.
- 🔍 Always verify whether input is attacker-controlled before classifying a finding as exploitable.
