### 🌲 Learning Objectives
- Process of Serialisation and Deserialisation
- Risks to WebApps
- Exploit techniques
- Mitigation measures




## 🚩 Serialisation:-
- It is just like taking different pieces of information (like notes) and putting them together to make them easy to store or send to a friend.
- In programming, serialization means converting an object (data structure) into a format that can be stored or transmitted (like a file, database, or over the internet) and reconstructed as and when required. This capability is essential in applications where data must be transferred between different parts of a
system or across a network, such as in web-based applications. In PHP, this process is performed using the serialize() function.
- Example: 

      <?php
      $noteArray = array("title" => "My THM Note", "content" => "Welcome to THM!");
      $serialisedNote = serialize($noteArray);  // Converting the note into a storable format
      file_put_contents('note.txt', $serialisedNote);  // Saving the serialised note to a file
      ?>
  
- The following output shows the serialised string in the note.txt file, which includes details of the note's structure and content. It’s stored in a way that
  can be easily saved or transmitted.

      Serialised Note: a:2:{s:5:"title";s:12:"My THM Note";s:7:"content";s:12:"Welcome to THM!";}




## 🚩 Deserialisation:-
- Imagine you're a student. Deserialisation is like unpacking your school bag when you get to class; you take out each item so you can use it throughout the day.
  As you unpack your bag to get your books and lunch, deserialisation takes the packed-up data and turns it back into something you can use. Deserialisation is
  the process of converting the formatted data back into an object. It's crucial for retrieving data from files, databases, or across networks, restoring it to
  its original state for usage in applications.
- Following our previous example, here's how you might deserialise the note data in PHP:

      <?php
      $serialisedNote = file_get_contents('note.txt');  // Reading the serialised note from the file
      $noteArray = unserialize($serialisedNote);  // Converting the serialised string back into a PHP array
      echo "Title: " . $noteArray['title'] . "<br>";
      echo "Content: " . $noteArray['content'];
      ?>

- This code reads the serialised note from a file and converts it back into an array, effectively reconstructing the original note. Discussing serialisation
  also necessitates a conversation about security. Like you wouldn’t want someone tampering with your school bag, insecure deserialisation can lead to
  significant security vulnerabilities in software applications. Attackers might alter serialised objects to execute unauthorised actions or steal data.




## 🚩 Specific incidents involving Serialisation Vulnerabilities:-

##### Log4j Vulnerability CVE-2021-44228
- Incident: The [Log4j vulnerability](https://nvd.nist.gov/vuln/detail/CVE-2021-44228), or Log4Shell, is a critical security flaw found in the Apache Log4j 2
  library, a widely used logging library in Java applications. The vulnerability allows remote attackers to execute arbitrary code on affected systems by
  exploiting the library's insecure deserialisation functionality. [Solar exploiting log4j](https://tryhackme.com/r/room/solar).
- Impact: The vulnerability facilitated remote code execution, enabling attackers to execute arbitrary commands on affected systems. This allowed attackers to
  compromise critical infrastructure, leading to unauthorised access to sensitive data, service disruptions, and potential supply chain attacks.

  ##### WebLogic Server Remote Code Execution CVE-2015-4852
- Incident: This vulnerability was related to how the [Oracle WebLogic Server](https://www.oracle.com/security-alerts/alert-cve-2015-4852.html) deserialised data
  was sent to the T3 protocol. Attackers could send maliciously crafted objects to the server, which, when deserialised, led to remote code execution.
- Impact: This vulnerability was widely exploited to gain unauthorised access to systems, deploy ransomware, or steal data. It affected all versions of the
  WebLogic Server that had not disabled the vulnerable service or patched the issue.

##### Jenkins Java Deserialisation CVE-2016-0792
- Incident: [Jenkins](https://www.tenable.com/plugins/nessus/89034), a popular automation server used in software development, experienced a critical
  vulnerability involving Java deserialisation. Attackers could send crafted serialisation payloads to the Jenkins CLI, which, when deserialised, could
  allow arbitrary code execution.
- Impact: This allowed attackers to execute shell commands, potentially taking over the Jenkins server, which often has broad access to a software development
  environment, including source code, build systems, and potentially deployment environments.
