# Alert 8816 - Microsoft-Themed Sign-In Phishing

## Overview

This case documents the investigation of a Microsoft-themed phishing email identified during a simulated SOC investigation.

The email claimed that unusual sign-in activity had occurred on the recipient's Microsoft account and provided a link to review the activity.

The alert was investigated using email telemetry in Splunk and IP-reputation enrichment through AbuseIPDB. Based on the investigation and simulator case report, the alert was classified as a **True Positive**.

---

## Alert Information

| Field           | Value                                                |
| --------------- | ---------------------------------------------------- |
| Alert ID        | 8816                                                 |
| Alert Type      | Phishing / Suspicious Email                          |
| Data Source     | Email                                                |
| Event Timestamp | `07/02/2026 11:41:16.804`                            |
| Subject         | `Unusual Sign-In Activity on Your Microsoft Account` |
| Sender          | `no-reply@microsoftsupport.co`                       |
| Recipient       | `c.allen@thetrydaily.thm`                            |
| Attachment      | None                                                 |
| Direction       | Inbound                                              |

---

## 1. Initial Triage

The email claimed that an unusual Microsoft account sign-in had occurred and instructed the recipient to review the activity through an embedded link.

The message contained several characteristics requiring investigation, including:

* Microsoft-themed account-security messaging
* Suspicious sender address
* Suspicious sender domain
* External login URL
* Referenced IP address
* Claimed sign-in location

---

## 2. Email Analysis

### Sender

```text
no-reply@microsoftsupport.co
```

### Recipient

```text
c.allen@thetrydaily.thm
```

### Subject

```text
Unusual Sign-In Activity on Your Microsoft Account
```

### Login URL

```text
https://microsoftsupport.co/login
```

### Claimed Sign-In Location

```text
Lagos, Nigeria
```

### Referenced IP

```text
102.89.222.143
```

The message therefore contained multiple indicators that warranted further investigation.

---

## 3. Splunk Investigation

A Splunk search was performed using the sender address:

```text
no-reply@microsoftsupport.co
```

The resulting event confirmed the inbound email and its associated fields, including:

* Sender
* Recipient
* Subject
* Timestamp
* Email content

The investigation also showed that the email contained the Microsoft-themed sign-in warning and the external login link.

### Investigative Observation

The SIEM investigation confirmed that the suspicious sender, recipient, subject and associated login URL were present in the observed email event.

---

## 4. Threat Intelligence Investigation

The IP referenced in the email was investigated using AbuseIPDB.

### IP

```text
102.89.222.143
```

### AbuseIPDB Results

| Field               | Result                       |
| ------------------- | ---------------------------- |
| Reports             | 0                            |
| Confidence of Abuse | 0%                           |
| ISP                 | MTN Nigeria                  |
| Usage Type          | Fixed Line ISP               |
| Country             | Nigeria                      |
| City                | Lagos                        |
| Database Status     | IP not found in the database |

---

## 5. Analytical Interpretation

The AbuseIPDB result did **not** establish that the IP was legitimate.

It also did not prove that the IP was fabricated or malicious.

The result only indicated that this particular reputation source did not provide positive abuse evidence for the IP at the time of the lookup.

The phishing assessment therefore relied primarily on the other characteristics of the email, particularly:

* Suspicious sender address
* Suspicious sender domain
* Microsoft-themed account-security message
* External login URL
* Referenced IP address
* Claimed sign-in location

This demonstrates why a lack of reputation reports should not automatically be interpreted as proof that an indicator is safe.

---

## 6. Classification

### Classification: True Positive

The alert was classified as a **True Positive**.

### Classification Rationale

The case report identified the following evidence:

1. The sender used `no-reply@microsoftsupport.co`.
2. The sender domain was not associated with Microsoft.
3. The email contained an external login URL.
4. The link redirected toward an external phishing destination.
5. The message was presented as a Microsoft account-security notification.

The final classification was therefore based on the combined characteristics of the email rather than on the IP reputation result alone.

---

## 7. Escalation Assessment

### Escalation Reason

```text
Possibility of user c.allen@thetrydaily.thm being compromised.
```

This represents the escalation assessment documented by the simulator.

It should not be interpreted as proof that the user's account was actually compromised or that a production escalation was executed.

---

## 8. Recommended Remediation

The simulator case report listed the following recommended remediation actions.

### 1. Implement or tune email filtering and spam detection

Improve email-security controls to help identify and prevent similar phishing messages.

### 2. Configure or implement EDR

Use endpoint detection and response capabilities to improve visibility into potentially malicious activity associated with affected systems or users.

### 3. Block the suspicious domain

Block the identified suspicious domain to reduce the possibility of users accessing the phishing destination.

> **Important:** The available screenshots and report document these as recommended remediation actions. They do not demonstrate that these controls were actually deployed.

---

## 9. Attack Indicators / IOCs

### Sender

```text
no-reply@microsoftsupport.co
```

### Sender Domain

```text
microsoftsupport.co
```

### Phishing Login URL

```text
https://microsoftsupport.co/login
```

### Referenced IP

```text
102.89.222.143
```

### Affected Recipient

```text
c.allen@thetrydaily.thm
```

### Claimed Location

```text
Lagos, Nigeria
```

---

## 10. Investigation Workflow

```text
Alert Intake
    ↓
Initial Triage
    ↓
Email Analysis
    ↓
Sender / Domain Analysis
    ↓
Splunk Investigation
    ↓
IOC Extraction
    ↓
IP Reputation Enrichment
    ↓
Classification
    ↓
Escalation Assessment
    ↓
Remediation Recommendation
    ↓
Case Documentation
```

---

## 11. Key Findings

### Finding 1 - Sender and domain analysis was important

The email used a sender address and domain designed to appear related to Microsoft.

The investigation found that the domain was not associated with Microsoft.

### Finding 2 - The login URL was a significant phishing indicator

The email provided an external login URL:

```text
https://microsoftsupport.co/login
```

The case report identified the destination as part of the phishing activity.

### Finding 3 - IP reputation alone was insufficient

AbuseIPDB returned no reports and 0% abuse confidence for `102.89.222.143`.

This did not establish that the email or IP was legitimate. The phishing assessment depended on the broader evidence.

### Finding 4 - Potential account compromise affected escalation

The simulator recorded the possibility of the affected user being compromised as the reason for escalation assessment.

---

## 12. Evidence

Supporting screenshots for this investigation should be stored under:

```text
evidence/alert-8816/
```

Recommended evidence categories:

```text
evidence/alert-8816/
├── alert-details
├── splunk-email-investigation
├── abuseipdb-investigation
└── case-report
```

The complete screenshot collection is also available in:

```text
reports/soc_alert_images.pdf
```

---

## 13. Environment and Limitations

This investigation was conducted in a **simulated SOC environment** as part of a SOC Immersive Simulation.

The findings should therefore not be presented as a real-world corporate security incident.

The remediation actions described in this case are recommendations documented by the simulator. The available evidence does not demonstrate that the recommended email filtering, EDR or domain blocking controls were actually deployed.

The AbuseIPDB lookup represents the result available at the time of the investigation and should be interpreted as supporting threat-intelligence evidence rather than definitive proof of legitimacy or maliciousness.

---

## 14. Skills Demonstrated

* SOC Alert Triage
* Phishing Investigation
* Email Security Analysis
* Splunk SIEM
* SPL Searching
* Log Analysis
* IOC Extraction
* IP Reputation Analysis
* Threat Intelligence
* True Positive / False Positive Classification
* Escalation Assessment
* Incident Documentation
* Incident Response Fundamentals

---

## Conclusion

Alert 8816 was investigated as a Microsoft-themed sign-in phishing email.

The investigation identified a suspicious sender address and domain, an external login URL, a referenced IP address, and a claimed sign-in location.

Splunk was used to investigate the email event, while AbuseIPDB was used to enrich the referenced IP. The absence of AbuseIPDB reports was not treated as proof that the indicator was legitimate.

Based on the combined evidence documented during the simulation, the alert was classified as a **True Positive**.

The case demonstrated the importance of analyzing sender and domain characteristics, investigating embedded URLs, correlating multiple sources of evidence, and treating threat-intelligence reputation results as supporting evidence rather than the sole basis for classification.
