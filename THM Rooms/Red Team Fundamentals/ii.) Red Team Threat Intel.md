## 🔺 Red Team Threat Intel 🔻
- Threat Intelligence= All information about hackers and their attack vector. It tells us who the attacker is,
  what tools they use, and how they attack.
- Blue team use CTI to spot and stop attacks. Red team can also use CTI to copy real hacker behavior
  (adversary emulation) during penetration tests.


  ### 🌲 What is Threat Intelligence ?
  
  - CTI is used by collecting hacker clues (IOCs) and attack methods (TTPs), shared by ISACs (Information and
    Sharing Analysis Centers), and organized by platforms to show the full timeline of an attack.

  > Note: The term ISAC is used loosely in the threat intelligence landscape and often refers to a threat
          intelligence platform.
  
  - IOCs(Indicators of Compromise) are quantified by traces left by adversaries such as domains, IPs, files,
    strings, etc. The blue team can utilize various IOCs to build detections and analyze behavior. From a red
    team perspective, you can think of threat intelligence as the red team's analysis of the blue team's
    ability to properly leverage CTI for detections.


  ### 🌲 Applying Threat Intel to the Red Team:-
- The red team will leverage CTI to aid in adversary emulation (pretending to be a hacker to test defenses)
  and support evidence of an adversary's behaviors. To aid in consuming CTI and collecting TTPs, red teams
  will often use CTI platforms and frameworks such as `MITRE ATT&CK`, `TIBER-EU`, and `OST Map`. 
- These cyber frameworks will collect known TTPs and categorize them based on varying characteristics such as,

    - Threat Group
    - Kill Chain Phase
    - Tactic
    - Objective/Goal

- Once a targeted adversary is selected, the goal is to identify all TTPs categorized with that chosen adversary
  and map them to a known cyber kill chain.
- Leveraging TTPs is used as a planning technique rather than something a team will focus on during engagement
  execution. During the execution of an engagement, the red team will use threat intelligence to craft tooling,
  modify traffic and behavior, and emulate the targeted adversary.
- Overall the red team consumes threat intelligence to analyze and emulate the behaviors of adversaries through
  collected TTPs and IOCs.


  ### 🌲 The TIBER-EU Framework:- 
- TIBER-EU (Threat Intelligence-based Ethical Red Teaming) is a common framework developed by the European
  Central Bank that centers around the use of threat intelligence.
- From the ECB TIBER-EU white paper, "The Framework for
  TIBER-EU enables European and national authorities to work with financial infrastructures and institutions
  (hereafter referred to collectively as 'entities') to put in place a programme to test and improve their
  resilience against sophisticated cyber attacks."
- The main difference between this framework and others is the "Testing" phase that requires threat intelligence
  to feed the red team's testing.


  ### 🌲 TTP Mapping:-
- TTP Mapping is employed by the red cell to map adversaries' collected TTPs to a standard cyber kill chain.
  Mapping TTPs to a kill chain aids the red team in planning an engagement to emulate an adversary.
- To begin the process of mapping TTPs, an adversary must be selected as the target. An adversary can be chosen
  based on:- 

   - Target Industry
   - Employed Attack Vectors
   - Country of Origin
   - Other Factors

As an example for this task, we have decided to use [APT 39](https://attack.mitre.org/groups/G0087/), a 
cyber-espionage group run by the Iranian ministry, known for targeting a wide variety of industries.
- The first cyber framework we will be collecting TTPs from is [MITRE ATT&CK](https://attack.mitre.org/).
  If you are not familiar with MITRE ATT&CK, it provides IDs and descriptions of categorized TTPs. ATT&CK
  provides a basic summary of a group's collected TTPs. We can use
  [ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) to help us visualize each TTP and
  categorize its place in the kill chain.


## 🛡 APT 39 Kill Chain:- 
1. Reconnaissance:

   - No identified TTPs, use internal team methodology

2. Weaponization:

   - Command and Scripting Interpreter
   - PowerShell
   - Python
   - VBA
   - User executed malicious attachments

3. Delivery:

    - Exploit Public-Facing Applications
    - Spearphishing

4. Exploitation:

    - Registry modification
    - Scheduled tasks
    - Keylogging
    - Credential dumping

5. Installation:

    - Ingress tool transfer
    - Proxy usage

6. Command & Control:

    - Web protocols (HTTP/HTTPS)
    - DNS

7. Actions on Objectives

    - Exfiltration over C2

##

- MITRE ATT&CK will do most of the work needed, but we can also supplement threat intelligence information with
  other platforms and frameworks. Another example of a TTP framework is
  [OST Map](https://github.com/intezer/ost-map). OST Map provides a visual map to link multiple threat actors
  and their TTPs.

- Other open-source and enterprise CTI platforms can aid red teamers in adversary emulation and TTP mapping, 
  such as:- 
   - CrowdStrike Falcon
   - Mandiant Advantage
   - Ontic
    

