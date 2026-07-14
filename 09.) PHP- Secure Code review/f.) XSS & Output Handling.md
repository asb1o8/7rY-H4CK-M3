# 🌐 Cross-Site Scripting (XSS)

Cross-Site Scripting (**XSS**) occurs when an application outputs **attacker-controlled input** into a web page **without proper encoding**, causing the browser to interpret it as **HTML or JavaScript**.

Unlike **SQL Injection** or **Command Injection**, which target the **server**, XSS targets the **victim's browser**.

---

# 🎯 XSS Types

## 🔄 Reflected XSS

- Payload comes from the **current HTTP request**.
- Reflected immediately in the server response.
- Victim must visit a crafted URL.
- Exists in **server-side code (PHP)**.

### Vulnerable Example

```php
public function site(Request $request)
{
    $q = $request->query('q');

    return response("<h1>Results for " . $q . "</h1>");
}
```

### 🔄 Data Flow

```text
Source
$request->query('q')
        │
        ▼
Concatenated into HTML
        │
        ▼
Response
        │
        ▼
Browser Executes HTML/JS
```

### 💥 Payload

```text
q=<script>alert(document.domain)</script>
```

Result:

```html
<h1>Results for <script>alert(document.domain)</script></h1>
```

The browser executes the script because it is returned **unescaped**.

---

## 💾 Stored XSS

- Payload is **saved** in the application.
- Later served to **every user** viewing the content.
- More dangerous because it affects multiple victims.

### Vulnerable Example

```php
<h2>{{ $user->name }}</h2>

<div>{!! $user->bio !!}</div>
```

### ✅ Safe

```blade
{{ $user->name }}
```

Blade automatically **escapes HTML**.

---

### ❌ Unsafe

```blade
{!! $user->bio !!}
```

Outputs data **without escaping**.

If an attacker stores:

```html
<script>alert(1)</script>
```

Anyone viewing the profile executes the script.

---

## 🖥️ DOM-Based XSS

Occurs entirely in **client-side JavaScript**.

Characteristics:

- No vulnerable PHP code required.
- JavaScript reads attacker-controlled data.
- JavaScript writes it into the DOM without encoding.

Review focus:

- JavaScript files
- DOM manipulation
- Client-side sinks

---

# 🔄 Source → Sink

```text
User Input
     │
     ▼
Application
     │
     ▼
HTML / JavaScript Output
     │
     ▼
Victim Browser Executes
```

---

# ⚠️ Encoding Depends on Context

There is **no universal encoding function**.

The correct protection depends on **where the data is inserted**.

## 📝 HTML Text

Use:

```php
htmlspecialchars()
```

Protects against HTML injection.

---

## 🏷️ HTML Attributes

Must encode:

- Quotes (`"` and `'`)
- Special HTML characters

Unquoted attributes remain dangerous.

---

## ⚡ JavaScript Context

Example:

```html
<script>
```

Requires **JavaScript-specific encoding**.

HTML encoding alone is **not sufficient**.

---

## 🔗 URL Context

Values inside URLs require:

- URL Encoding
- Percent Encoding

Example:

```
Space → %20
```

---

# 🛡️ Secure Output Practices

For HTML output:

```php
htmlspecialchars($data, ENT_QUOTES, 'UTF-8')
```

- Encodes HTML entities.
- Encodes both single and double quotes.
- Uses UTF-8 safely.

---

# 🏗️ Template Engines

Prefer automatic escaping provided by frameworks.

### ✅ Laravel Blade

```blade
{{ $value }}
```

Automatically escapes output.

---

### ❌ Unescaped Output

```blade
{!! $value !!}
```

Should always be reviewed carefully.

Treat every occurrence as a potential security finding unless there is a strong justification.

---

# 🔍 Code Review Checklist

### 📥 Sources

- `$_GET`
- `$_POST`
- `request()->input()`
- Database content
- User profiles
- Comments
- Messages

---

### 📤 XSS Sinks

- `echo`
- `print`
- `printf`
- HTML templates
- Blade `{!! !!}`
- JavaScript DOM updates

---

### 🛡️ Verify

- Is output encoded?
- Is the encoding correct **for this context**?
- Is automatic escaping bypassed?
- Can attacker-controlled data reach the sink?

---

# ✅ Key Takeaways

- XSS executes **JavaScript in the victim's browser**, not on the server.
- **Reflected XSS** returns attacker input immediately in the response.
- **Stored XSS** saves malicious input and affects every future viewer.
- **DOM-Based XSS** exists entirely in client-side JavaScript.
- Output encoding is **context-specific** (HTML, Attribute, JavaScript, URL).
- Use `htmlspecialchars(..., ENT_QUOTES, 'UTF-8')` for HTML contexts.
- Prefer framework auto-escaping (e.g., `{{ }}` in Blade).
- Treat unescaped output (e.g., `{!! !!}`) as a potential security finding.
