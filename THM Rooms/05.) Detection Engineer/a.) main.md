#### 🌲 Following categories are covered:
- Detection Engineer
- Detection Engineering Frameworks 1


## 🚩 Detection Engineer
##### Detection engineering is the continuous process of building and operating threat intelligence analytics to identify potentially malicious activity or misconfigurations that may affect your environment. It requires a cultural shift with the alignment of all security teams and management to build effective threat-rich defence systems.

#### 🌴 Detection Types:-
- Environment-based detection:-
  It monitors changes in configurations and baseline activities, including configuration detection and modelling.
- Threat-based detection:-
  It focuses on elements associated with an adversary’s activity, such as tactics, tools and artefacts that would
  identify their actions. Under this, we have Indicators and Threat Behaviour detections.


#### 🌴 Configuration Detection:- 
- Under this detection, we use current knowledge of the known environment and infrastructure to identify
  misalignments. Configurations can cross domains, including network, asset or identity.


#### 🌴 Modelling:-
- Threat detection under this type is done by defining baseline operations and activities and recording any
  deviations that occur. The primary assumption of this approach is that malicious activity can be sufficiently
  identified from benign activity.
- The approach involves building an asset or activity profile that includes baseline events, time and data
  threshold. An in-depth look into baselining shall be discussed in the next task.


#### 🌴 Indicator Detection:-
- As a reminder, indicators are pieces of information that identify a state and context of an element or entity.
  There are both good indicators used to identify legitimate activities or resources, such as those used in
  whitelists, and bad indicators used for suspicious or malicious resources, such as in blacklists or malware IPs.
- IOCs are commonly referenced and derived from investigations against malicious events. By observing threat
  activities and investigations, analysts can use identified indicators to craft detections and adapt them based
  on an adversary’s rate of change.


#### 🌴 Threat Behaviour Detection:-
- Analysts will look at an adversary’s Tactics, Techniques and Procedures (TTPs) to conduct an attack,
  regardless of any specific indicators. This makes detection more scalable beyond indicators.
- Through this detection, analysts can focus their efforts more efficiently on responding to the threat and
  mitigate against it instead of utilising time and resources to understand how and why alerts were triggered.
  Additionally, threat behaviour detection can be paired with established workflows and playbooks to provide
  best practices that can be followed during an investigation.


#### 🌴 Detection as Code (DaC):-
- DaC is a structured approach to writing detections by incorporating software engineering best practice
  principles. This means that detection engineers and analysts will handle detection processes and logic
  as code, offering scalability to address the rapidly changing environments and adversary capabilities.
- DaC offers a code-driven workflow that creates fine-tuned detection processes that introduce critical
  elements found in Continuous Integration/Continuous Development (CI/CD) workflows. Some of these elements
  include:
      - Version Control
      - Automation workflows



## 🚩 Detection Engineering Frameworks 1

- [ATT&CK framework](https://attack.mitre.org/) maps adversarial actions for detection engineering and guides gap analysis.
- [CAR or Cyber Analytics Repository](https://car.mitre.org/) knowledge base detects and prioritizes adversary behaviours using ATT&CK.


#### 🌴 Pyramid of Pain:-
- This is a well-known framework in the industry and is mainly used to showcase the pain for the adversary; if
  the defenders detect their TTPs, then how difficult and/or costly it would be for the adversary to change
  their TTPs.

  Attack img



#### 🌴 Cyber Kill Chain:-
- Thanks to a military concept of an attack strategy, Lockheed Martin formulated the Cyber Kill Chain framework
  to define the necessary steps followed by adversaries. The framework focuses on seven crucial phases that
  cyber-attacks commonly follow:

    - Reconnaissance
    - Weaponisation
    - Delivery
    - Exploitation
    - Installation
    - Command & Control
    - Actions on Objectives
 - As a security analyst and detection engineer, understanding the Cyber Kill Chain will give you the knowledge
   to recognise intrusion attempts crafted by an adversary and map them into your detection plan. The Unified
   Kill Chain was developed to complement the Cyber Kill Chain by combining it with other frameworks, such as
   the MITRE ATT&CK framework. This expanded the original kill chain into 18 phases to cover every known element
   of a cyber attack.
   UKC cmd image

#### 🌴 Other Frameworks:- 
- [MITRE](https://tryhackme.com/room/mitre)
- [Pyramid of Pain](https://tryhackme.com/room/pyramidofpainax)
- [Cyber Kill Chain](https://tryhackme.com/room/cyberkillchainzmt)
- [Unified Kill Chain](https://tryhackme.com/room/unifiedkillchain)

