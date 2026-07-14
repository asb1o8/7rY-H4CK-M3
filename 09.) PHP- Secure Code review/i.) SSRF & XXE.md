The flaws in this task share a shape. In each, the application can be made to act as a client and reach out to a destination the attacker chooses, whether an internal service or an attacker's own server. Both are common in PHP and both are visible from the source.

Server-Side Request Forgery
Server-side request forgery, or SSRF, is the flaw where attacker input controls the destination of a request the server makes. The PHP sinks are file_get_contents given a URL, curl_exec, and HTTP-client calls such as Guzzle, which the target's composer.json showed back in Task 2. The target previews a link by fetching it.

// app/Http/Controllers/FetchController.php
public function preview(Request $request)
{
    $url = $request->query('url');
    $client = new \GuzzleHttp\Client();
    return response((string) $client->get($url)->getBody());
}
The source $request->query('url') becomes the destination of an outbound request with no restriction on where it may point. The intended use fetches an external page, but an attacker supplies an internal address instead. By pointing url at http://127.0.0.1/ or an internal-only service, the attacker reaches systems that are not exposed to the internet but are reachable from the server. A particularly serious target on cloud-hosted systems is the instance metadata service at http://169.254.169.254/, which can return credentials. We confirm the reach against the target on the /link/preview route.

curl -s "http://localhost:8080/link/preview?url=http://127.0.0.1:8080/"
As we can see, the body of an internal resource comes back through the application, which proves the request is made to a destination we control rather than only to intended external sites. The reviewer's defence is to validate the destination against an allowlist of permitted hosts, to resolve and check the address so that it is not an internal or link-local range, and to be aware that following redirects can defeat a naive check, so redirects should be disabled or revalidated.

XML External Entity Injection
XML external entity injection, or XXE, arises when an application parses attacker-controlled XML with a parser configured to resolve external entities. An external entity is a placeholder in the XML document whose value the parser fetches from a URI, so an attacker who defines one can make the parser read a local file or make a network request. The target imports data from XML.

// app/Http/Controllers/ImportController.php
public function import(Request $request)
{
    $dom = new \DOMDocument();
    $dom->loadXML($request->getContent(), LIBXML_NOENT | LIBXML_DTDLOAD);
    return response($dom->textContent);
}
The flaw here is the LIBXML_NOENT | LIBXML_DTDLOAD option, which tells libxml to load the document's document-type definition and substitute its entities. With that enabled, an attacker submits a body that defines an external entity pointing at a local file.

<?xml version="1.0"?>
<!DOCTYPE r [<!ENTITY x SYSTEM "file:///etc/passwd">]>
<r>&x;</r>
When the parser expands &x;, it reads the file and places its contents into the document, which the application then returns. We confirm it on the /import route by sending that body.

curl -s "http://localhost:8080/import" -H "Content-Type: application/xml" --data-binary '<?xml version="1.0"?><!DOCTYPE r [<!ENTITY x SYSTEM "file:///etc/passwd">]><r>&x;</r>'
As we can see, the contents of the file are reflected in the response, which confirms the parser resolved our external entity. It is worth knowing the current default, because it shapes the finding. Since libxml 2.9, external entity loading is disabled by default, so XXE in modern PHP requires the parser to have been explicitly configured to load entities, exactly as this code does with its libxml options. A reviewer therefore treats the presence of those options on a parser fed untrusted XML as the finding, and recommends removing them so the safe default applies. When the file contents are not returned to us directly, the same flaw becomes a blind one, exfiltrated to an attacker server, which we note here and which the SSRF defences above also bear on.

We now raise our altitude from these language-level sinks to the framework, where Laravel and Symfony both provide protections we must confirm are applied and offer escape hatches that quietly reintroduce the very flaws we have been finding.
