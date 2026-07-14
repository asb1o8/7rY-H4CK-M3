Serialisation turns a PHP value into a storable string, and unserialize turns that string back into a value. The flaw arises when unserialize is called on attacker-controlled data, because the attacker then controls which objects are created and what their properties contain. Combined with the magic methods from Task 4, this leads to a chain of automatic method calls that a reviewer can follow all the way to code execution.

Why a Restored Object Is Dangerous
When unserialize reconstructs an object, PHP may call its magic methods automatically, __wakeup as it is restored and __destruct when it is later destroyed. An attacker who can supply the serialised string therefore chooses the class, sets the properties, and causes those methods to run with attacker-chosen data. A gadget chain is a sequence of such methods, already present in the application's loaded classes, that an attacker strings together to reach a dangerous operation. The individual classes were never written to be malicious, but the attacker assembles their side effects into a path that ends, in the strongest case, at command execution.

The single most important control is the second argument to unserialize. Passing ['allowed_classes' => false] instructs PHP to restore no objects at all, only plain data, which defeats object injection because no magic methods can fire. A reviewer reading an unserialize call therefore checks two things at once, whether the data reaching it is attacker-controlled, and whether the call restricts the classes it will instantiate.

phar:// Deserialisation
There is a route to deserialisation that does not pass through an obvious unserialize call at all. A PHP archive, or Phar, stores serialised metadata, and many ordinary file functions will unserialize that metadata when they are given a path beginning with the phar:// wrapper. This means that a file operation such as file_exists, fopen, getimagesize or an inclusion, when handed an attacker-influenced path, can trigger object injection even though the code contains no unserialize. This is why the file-operation sinks from Task 7 matter to this task, and why a reviewer who controls a path anywhere should consider whether it can be pointed at a phar:// resource. We saw the relevant file sinks while reading the download and upload features.

Reviewing the Target's Deserialisation
We will exploit the target's deserialisation flaw in full during the capstone in Task 12, but we read it here. A ripgrep search for the sink returns two calls.

rg -n "\bunserialize\s*\(" .
app/Http/Middleware/LoadPreferences.php:12:
$prefs = unserialize($_COOKIE['prefs']);
app/Services/CacheReader.php:10:
$data = unserialize($blob, ['allowed_classes' => false]);
As we can see, the search returns two hits, and the two calls demand different verdicts. The call in LoadPreferences.php reads its data from the attacker-controlled prefs cookie and places no restriction on which classes may be instantiated, so it is exploitable.

// app/Http/Middleware/LoadPreferences.php  (true positive)
$prefs = unserialize($_COOKIE['prefs']);
The call in CacheReader.php passes ['allowed_classes' => false], so although a scanner flags it as object injection, no objects are created and no magic methods can fire, which makes it a false positive.

// app/Services/CacheReader.php  (false positive)
$data = unserialize($blob, ['allowed_classes' => false]);
This contrast is the core of triaging deserialisation findings, and we carry the true-positive call through to exploitation in Task 12. The defence a reviewer recommends is to avoid deserialising untrusted input at all, preferring a data format such as JSON for anything crossing a trust boundary, and to pass ['allowed_classes' => false] wherever native deserialisation is unavoidable.

Two flaws that turn the server itself into a client come next, server-side request forgery and XML external entity injection.
