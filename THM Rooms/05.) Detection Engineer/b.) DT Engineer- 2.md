## 🚩 Detection Engineering Frameworks 2


### 🌴 Alerting and Detection Strategy Framework:- 

- Palantir developed the ADS Framework to provide a guideline for documenting detection content. A significant
  challenge faced by security teams, and Palantir being no exception, is alert fatigue and apathy, mainly caused
  by poor means of developing and implementing detection alerts that would result in effective incident response
  and mitigation. The ADS Framework seeks to address this challenge and provide a guideline for constructing
  effective detections and alerts.

- The ADS Framework has a strict flow that detection engineers must follow before publishing detection rules into
  production. The stages involved are:

1) Goal: Describes the intended reasons for setting up the alert and the type of behaviour that needs to be detected.
2) Categorisation: Mapping the detection to the MITRE ATT&CK framework to provide analysts with information on the
                   TTPs for investigation and areas of the kill chain where the ADS will be used.

3) Strategy Abstract: Provides a top-level description of how the detection strategy being implemented functions by
                      outlining what the alert will look for, the data sources, enrichment resources and ways of
                      reducing false positives.

4) Technical Context: Describes the technical environment of the detection to be used, providing analysts and
                      responders with all the information needed to understand the alert. Security analysts
                      should align this information with the platforms and tools for collecting and processing
                      threat alerts.

5) Blind Spots & Assumptions: Describes any issues identified where suspicious activities may not trigger the
                                strategy. Assumptions and blind spots help clarify ways the ADS may fail or be
                                bypassed by an adversary.

6) False Positives: Outlines occurrences where alerts may be triggered due to misconfigurations or non-malicious
                    activities within the environment. This makes it easy to configure your SIEM to limit alert
                    generation to only targetted threats when pushed to production.

7) Validation: Every detection needs to be verified, and here, you can outline all the steps required to produce
               a true-positive event that would trigger the detection alert. Consider this a unit test, which
               can even be a script or scenario used to generate an alert. For an effective validation:
   
   - Develop a plan that will produce a true-positive outcome.
   - Document the process of the plan.
   - From the testing environment, test and trigger an alert.
   - Validate the strategy that triggered the alert.

9) Priority: Set up the alerting levels with which the detection strategy may be tagged. This section provides
             the details of the criteria used to set up the preferences, and it is separate from the alerting
             levels shown through the SIEM.

10) Response: Provides details of how to triage and investigate a detection alert. This information is helpful
             for analysts and responders to be able to prevent extreme repercussions.
