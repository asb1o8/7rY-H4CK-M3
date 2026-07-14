# 🌐 Server-Side Request Vulnerabilities

Both vulnerabilities share the same concept:

> **The attacker tricks the application into acting as a client and making requests on their behalf.**

This can allow access to:
- 🏠 Internal network services
- ☁️ Cloud metadata services
- 🖥️ Attacker-controlled servers
- 📂 Local system files

---

# 🚨 Server-Side Request Forgery (SSRF)

## 📖 What is SSRF?

**SSRF (Server-Side Request Forgery)** occurs when **attacker-controlled input determines the destination of a server-side request.**

Instead of the attacker sending requests directly, **the vulnerable server sends them**.

---

## 📥 Source

```php
$request->query('url')
```

User controls the URL.

---

## 📤 Sink

PHP functions/libraries that make outbound requests:

- `file_get_contents()`
- `curl_exec()`
- `Guzzle HTTP Client`

Example:

```php
$url = $request->query('url');

$client = new GuzzleHttp\Client();

$client->get($url);
```

---

## 🔄 Data Flow

```text
User Input (url)
        │
        ▼
$request->query()
        │
        ▼
Guzzle::get()
        │
        ▼
Server Makes Request
```

---

## ⚠️ Attack Example

Instead of:

```text
https://example.com
```

Attacker supplies:

```text
http://127.0.0.1/
```

or

```text
http://169.254.169.254/
```

The server now accesses:

- 🏠 Localhost services
- 🔒 Internal applications
- ☁️ Cloud metadata endpoints
- 🔑 Cloud credentials

---

## 🛡️ SSRF Defenses

- ✅ Allowlist trusted domains.
- ✅ Validate resolved IP addresses.
- ✅ Block:
  - Internal IPs
  - Loopback addresses
  - Link-local addresses
- ✅ Disable or revalidate redirects.
- ✅ Never trust user-supplied URLs directly.

---

# 📄 XML External Entity (XXE)

## 📖 What is XXE?

**XXE (XML External Entity Injection)** occurs when an application parses **attacker-controlled XML** while allowing **external entities**.

An external entity instructs the XML parser to fetch data from:

- 📂 Local files
- 🌐 Remote URLs

---

## 📥 Source

```php
$request->getContent()
```

User supplies arbitrary XML.

---

## 📤 Sink

```php
DOMDocument::loadXML()
```

Dangerous parser configuration:

```php
LIBXML_NOENT
LIBXML_DTDLOAD
```

These options enable:

- External entity loading
- Entity expansion

---

## 🔄 Data Flow

```text
User XML
    │
    ▼
loadXML()
    │
    ▼
External Entity
    │
    ▼
Reads Local File / Makes Request
```

---

## ⚠️ Attack Example

Malicious XML:

```xml
<!DOCTYPE r [
<!ENTITY x SYSTEM "file:///etc/passwd">
]>
<r>&x;</r>
```

Parser expands:

```text
&x;
```

Result:

```text
Contents of /etc/passwd
```

returned in the application's response.

---

## 🎯 Impact

XXE can lead to:

- 📂 Local File Disclosure
- 🌐 SSRF
- 🔑 Credential Theft
- 📡 Blind Data Exfiltration

---

## 🛡️ XXE Defenses

- ✅ Disable external entity resolution.
- ✅ Remove:
  - `LIBXML_NOENT`
  - `LIBXML_DTDLOAD`
- ✅ Use default secure parser settings.
- ✅ Never parse untrusted XML with entity loading enabled.

> **Modern `libxml` (≥ 2.9) disables external entity loading by default. XXE usually occurs only when developers explicitly re-enable it.**

---

# 🔍 SSRF vs XXE

| 🌐 SSRF | 📄 XXE |
|---------|--------|
| Attacker controls request destination | Attacker controls XML parser |
| Uses HTTP clients (`curl`, `Guzzle`, `file_get_contents`) | Uses XML parsers (`DOMDocument`, `simplexml`) |
| Makes outbound network requests | Reads files or makes network requests via XML entities |
| Targets internal services and metadata APIs | Targets local files, internal services, or remote servers |

---

# ✅ Key Takeaways

- 🌐 **SSRF** tricks the server into making requests to attacker-chosen destinations.
- 📄 **XXE** abuses XML parsers configured to resolve external entities.
- ☁️ Cloud metadata endpoints (e.g., `169.254.169.254`) are common SSRF targets.
- 📂 XXE can disclose sensitive local files like `/etc/passwd`.
- 🛡️ Restrict outbound requests, validate destinations, and use secure XML parser defaults.
- 🔍 During code review, look for:
  - HTTP request functions (`curl`, `Guzzle`, `file_get_contents`)
  - XML parsers using `LIBXML_NOENT` or `LIBXML_DTDLOAD`
  - User-controlled input flowing into these sinks.
