# SOC Alert Triage & Phishing Investigation

Hands-on SOC alert triage and phishing investigation performed in a simulated enterprise SOC environment using **Splunk Enterprise 8.2.6**, firewall telemetry, and **AbuseIPDB** threat-intelligence enrichment.

The project demonstrates an end-to-end SOC investigation workflow covering alert triage, email analysis, SIEM investigation, network correlation, IOC extraction, threat-intelligence enrichment, alert classification, escalation assessment, and incident documentation.

---
## Investigation Report

📄 **[View the Complete SOC Alert Triage & Phishing Investigation Report](reports/SOC-Alert-Triage-Phishing-Investigation-Report.pdf)**

The report contains the detailed investigation methodology, alert analysis,
Splunk investigation, firewall correlation, threat-intelligence enrichment,
classification rationale, escalation assessments, remediation recommendations,
findings, limitations, and simulator outcome.

## Project Overview

This project contains three phishing-related SOC investigations conducted in a simulated SOC environment:

| Alert    | Investigation                                  | Classification     |
| -------- | ---------------------------------------------- | ------------------ |
| **8814** | Onboarding email with suspicious external link | **False Positive** |
| **8815** | Package-delivery phishing email                | **True Positive**  |
| **8816** | Microsoft-themed unusual sign-in phishing      | **True Positive**  |

The investigations demonstrate why SOC analysts should validate alerts using surrounding context and correlated telemetry rather than making decisions based only on the alert name or a single indicator.

---

## Environment

| Component               | Details                            |
| ----------------------- | ---------------------------------- |
| Platform                | TryHackMe SOC Immersive Simulation |
| SIEM                    | Splunk Enterprise 8.2.6            |
| Threat Intelligence     | AbuseIPDB                          |
| Environment             | Simulated SOC                      |
| Investigation Date      | 2 July 2026                        |
| Primary Threat Category | Phishing / Social Engineering      |

---

## Investigation Workflow

The investigations followed a SOC-style workflow:

```text
Alert Intake
     ↓
Initial Triage
     ↓
Email Analysis
     ↓
SIEM Investigation
     ↓
Network Correlation
     ↓
Threat Intelligence Enrichment
     ↓
Alert Classification
     ↓
Escalation Assessment
     ↓
Incident Documentation
```

This workflow was applied across the investigated alerts, with the depth of investigation determined by the available evidence and telemetry.

---

# Investigated Cases

## Alert 8814 — Onboarding Email

**Classification: False Positive**

An inbound email triggered a suspicious-link detection rule.

The investigation reviewed the recipient's surrounding email activity in Splunk and identified legitimate organizational communications associated with hiring, interview scheduling, event coordination, and onboarding.

The surrounding context supported the interpretation that the email was part of a legitimate onboarding process rather than an isolated malicious communication.

### Key lesson

A suspicious-link alert should not automatically be treated as a confirmed phishing incident. Contextual validation is necessary before classification.

[View Alert 8814 Investigation](cases/8814-onboarding-false-positive.md)

---

## Alert 8815 — Package-Delivery Phishing

**Classification: True Positive**

The email presented itself as an Amazon package-delivery notification and instructed the recipient to confirm shipping information through a shortened Bitly URL.

### Key indicators

```text
Sender:
urgents@amazon.biz

Recipient:
h.harris@thetrydaily.thm

URL:
http://bit.ly/3sHkX3da12340
```

The investigation correlated the email with firewall telemetry showing an attempted connection to:

```text
Destination IP: 67.199.248.11
Destination Port: 80
Protocol: TCP
Source IP: 10.20.2.17
Action: blocked
Rule: Blocked Websites
```

The destination IP was also investigated using AbuseIPDB.

The AbuseIPDB result showed the IP associated with Bitly infrastructure, but the displayed abuse-confidence value was **0%**. The IP reputation result was therefore treated as supporting evidence rather than as proof that the IP itself was malicious.

### Key lesson

Correlation between email telemetry and network telemetry provided stronger investigative context than relying on a single indicator.

[View Alert 8815 Investigation](cases/8815-package-delivery-phishing.md)

---

## Alert 8816 — Microsoft-Themed Sign-In Phishing

**Classification: True Positive**

The email claimed that unusual sign-in activity had occurred on a Microsoft account and directed the recipient to an external login page.

### Key indicators

```text
Sender:
no-reply@microsoftsupport.co

Recipient:
c.allen@thetrydaily.thm

Login URL:
https://microsoftsupport.co/login

Referenced IP:
102.89.222.143

Claimed Location:
Lagos, Nigeria
```

The sender/domain characteristics and external login URL were significant phishing indicators.

The referenced IP was investigated using AbuseIPDB, which returned no reports and 0% abuse confidence. This result did not establish that the IP or email was legitimate and was therefore interpreted only as supporting threat-intelligence context.

### Key lesson

A clean or unavailable reputation result does not automatically make an indicator trustworthy. Multiple pieces of evidence must be considered together.

[View Alert 8816 Investigation](cases/8816-microsoft-signin-phishing.md)

---

# Tools & Technologies

### Splunk

Used as the primary SIEM for:

* Email-event investigation
* Sender and recipient searches
* Source and destination IP analysis
* URL investigation
* Event correlation
* Firewall telemetry investigation

### AbuseIPDB

Used to enrich network indicators and provide additional reputation context.

AbuseIPDB results were treated as **supporting evidence** rather than as the sole basis for determining maliciousness.

### Firewall Telemetry

Used to correlate suspicious email activity with network connections and determine whether potentially suspicious connections were allowed or blocked.

---

# SPL Investigation

The repository contains Splunk search examples used during the investigations:

```text
spl/
├── 01-search-recipient.spl
├── 02-search-sender.spl
└── 03-firewall-url-correlation.spl
```

These queries demonstrate investigation techniques such as searching by:

* Recipient
* Sender
* URL
* Source IP
* Destination IP
* Related network activity

---

# IOC Collection

Indicators identified during the investigations are maintained in:

```text
iocs/
└── iocs.csv
```

The IOC collection includes relevant:

* Email addresses
* Domains
* URLs
* IP addresses
* Affected recipients
* Network indicators

The repository intentionally distinguishes between an **indicator being observed** and an **indicator being proven malicious**.

---

# Phishing Triage Playbook

The investigation methodology has been converted into a reusable phishing-alert triage workflow:

```text
playbooks/
└── phishing-alert-triage.md
```

The playbook covers:

1. Alert validation
2. Email analysis
3. SIEM searching
4. Network correlation
5. Indicator enrichment
6. Alert classification
7. Escalation assessment
8. Incident documentation

---

# Screenshot

Investigation screenshots are organized by alert:

```text
screenshots/
├── case-8814/
├── case-8815/
└── case-8816/
```

The screenshots contains evidence associated with the respective investigations, including alert information, Splunk investigations, threat-intelligence lookups, network evidence, and case-report information where applicable.


---

# Key Findings

## 1. Context matters when triaging alerts

Alert 8814 demonstrated that a suspicious-link detection can occur during legitimate organizational activity.

The alert was investigated using surrounding user and communication context before being classified as a false positive.

## 2. Correlation increases investigative confidence

Alert 8815 demonstrated the value of correlating email telemetry with firewall activity.

The investigation identified an attempted connection associated with the phishing URL, and the firewall recorded that connection as blocked.

## 3. Threat intelligence is supporting evidence

The AbuseIPDB investigations demonstrated that IP reputation results should not automatically determine alert classification.

A reputation result can provide context without proving that the associated activity is legitimate or malicious.

## 4. Potential account compromise affects escalation

The two true-positive investigations included possible user compromise as part of the escalation assessment.

---

# Simulator Outcome

The final simulator result reported:

```text
Scenario Status:
Victory! Security breach prevented!

True-Positive Rate:
60%
```

The simulator also reported that all true-positive alerts were identified while noting that MTTR and dwell time were longer than average and that the phishing investigation took notably longer to close.

### Important

The **60% true-positive rate is retained exactly as reported by the simulator**.

It is not represented as 100% in this portfolio.

The statement that all true-positive alerts were identified and the separate 60% simulator metric describe different performance measurements.

---

# Skills Demonstrated

```text
SOC Alert Triage
Phishing Investigation
Email Security Analysis
Splunk SIEM
SPL Searching
Log Analysis
Firewall Log Analysis
Event Correlation
IOC Extraction
IP Reputation Analysis
Threat Intelligence
True Positive / False Positive Classification
Escalation Assessment
Incident Documentation
Incident Response Fundamentals
```

---

# Repository Structure

```text
SOC-Alert-Triage-Phishing-Investigation/
│
├── README.md
│
├── reports/
│   └── SOC-Alert-Triage-Phishing-Investigation-Report.pdf
│
├── cases/
│   ├── 8814-onboarding-false-positive.md
│   ├── 8815-package-delivery-phishing.md
│   └── 8816-microsoft-signin-phishing.md
│
├── spl/
│   ├── 01-search-recipient.spl
│   ├── 02-search-sender.spl
│   └── 03-firewall-url-correlation.spl
│
├── iocs/
│   └── iocs.csv
│
├── playbooks/
│   └── phishing-alert-triage.md
│
└── screenshots/
    ├── case-8814/
    ├── case-8815/
    └── case-8816/
```

---

# Investigation Limitations

This project was conducted in a **simulated SOC environment** and should not be presented as a real corporate security incident.

The investigation evidence demonstrates the activities performed within the simulation, but it does not establish that the recommended remediation actions were actually deployed in a production environment.

Similarly:

* Recommended password resets were not independently verified as performed.
* MFA implementation was not independently verified as performed.
* Email filtering changes were not independently verified as deployed.
* EDR deployment was not independently verified as deployed.
* Domain blocking was not independently verified as deployed.
* Threat-intelligence results represent the information available from the investigated source at the time of the exercise.
* The available investigation evidence represents the observed investigation scope and should not be interpreted as proof that no activity existed outside that scope.

---

# What This Project Demonstrates

This project demonstrates a structured approach to SOC alert investigation:

```text
Don't trust the alert blindly.
        ↓
Investigate the surrounding context.
        ↓
Search the SIEM.
        ↓
Correlate multiple telemetry sources.
        ↓
Extract relevant indicators.
        ↓
Enrich indicators with threat intelligence.
        ↓
Classify using combined evidence.
        ↓
Assess escalation requirements.
        ↓
Document the investigation.
```

The goal is not simply to identify phishing emails, but to demonstrate the analytical process used to distinguish legitimate activity from suspicious activity and document the reasoning behind the final classification.

---

## Disclaimer

This repository is a portfolio project based on a simulated SOC investigation.

All investigation findings, indicators, classifications, and remediation recommendations should be understood within the context of the simulation and its available telemetry.
