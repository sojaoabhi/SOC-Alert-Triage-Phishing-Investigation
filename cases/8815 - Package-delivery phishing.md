# Alert 8815 - Package-Delivery Phishing

## Overview

This case documents the investigation of a package-delivery-themed phishing email identified during a simulated SOC investigation.

The alert was analyzed using email telemetry in Splunk, correlated with firewall activity, and enriched with AbuseIPDB data. The alert was ultimately classified as a **True Positive**.

---

## Alert Information

| Field           | Value                                                         |
| --------------- | ------------------------------------------------------------- |
| Alert ID        | 8815                                                          |
| Alert Type      | Phishing                                                      |
| Severity        | Medium                                                        |
| Data Source     | Email                                                         |
| Alert Time      | 2 July 2026 at approximately 11:40                            |
| Event Timestamp | `07/02/2026 11:38:58.804`                                     |
| Subject         | `Your Amazon Package Couldn't Be Delivered - Action Required` |
| Sender          | `urgents@amazon.biz`                                          |
| Recipient       | `h.harris@thetrydaily.thm`                                    |
| Attachment      | None                                                          |
| Direction       | Inbound                                                       |

---

## 1. Initial Triage

The alert involved an inbound email using a package-delivery theme. The message instructed the recipient to confirm shipping information through a shortened URL.

The sender used the domain `amazon.biz` while presenting the email as an Amazon delivery notification.

The use of a shortened URL made the final destination less immediately visible and warranted further investigation.

---

## 2. Email Analysis

### Sender

```text
urgents@amazon.biz
```

### Recipient

```text
h.harris@thetrydaily.thm
```

### Subject

```text
Your Amazon Package Couldn't Be Delivered - Action Required
```

### Embedded URL

```text
http://bit.ly/3sHkX3da12340
```

### Initial Indicators

The following characteristics required investigation:

* Package-delivery phishing theme
* Suspicious sender address
* Sender domain inconsistent with the claimed brand
* Shortened Bitly URL
* Inbound delivery to an internal recipient

---

## 3. Splunk Investigation

A Splunk search was performed using the sender and recipient information to locate the associated email event.

The relevant event confirmed:

```text
Sender:      urgents@amazon.biz
Recipient:   h.harris@thetrydaily.thm
Subject:     Your Amazon Package Couldn't Be Delivered - Action Required
Timestamp:   07/02/2026 11:38:58.804
Attachment:  None
Direction:   Inbound
URL:         http://bit.ly/3sHkX3da12340
```

The SIEM investigation confirmed that the shortened Bitly URL was contained in the suspicious email.

---

## 4. Firewall Correlation

The investigation was expanded from email telemetry to firewall telemetry.

A corresponding firewall event was identified at:

```text
Timestamp:       07/02/2026 11:40:12.804
Action:          blocked
Application:     web-browsing
Destination IP:  67.199.248.11
Destination Port: 80
Protocol:        TCP
Rule:            Blocked Websites
Source IP:       10.20.2.17
Source Port:     34257
URL:             http://bit.ly/3sHkX3da12340
Data Source:     firewall
```

### Correlation Finding

The firewall recorded an attempted web connection to the same Bitly URL contained in the phishing email.

The connection was blocked by the `Blocked Websites` rule.

A separate firewall event from the same source IP showed normal browsing activity to Google over HTTPS. This helped distinguish the suspicious connection from ordinary allowed internet traffic.

---

## 5. Threat Intelligence Enrichment

The destination IP associated with the Bitly URL was investigated using AbuseIPDB.

### IP

```text
67.199.248.11
```

### AbuseIPDB Results

| Field               | Result                   |
| ------------------- | ------------------------ |
| Reports             | 851                      |
| Confidence of Abuse | 0%                       |
| ISP                 | Bitly Inc                |
| Usage Type          | Content Delivery Network |
| Hostname            | bit.ly                   |
| Domain              | bit.ly                   |

### Analytical Interpretation

The AbuseIPDB result did **not** establish that the IP itself was malicious.

Although the IP was present in the AbuseIPDB database and had 851 reports, the displayed abuse-confidence value was 0%.

The result therefore provided context indicating that the destination belonged to Bitly infrastructure. The final maliciousness assessment relied on the combined investigation evidence rather than the IP reputation result alone.

---

## 6. Classification

### Classification: True Positive

The alert was classified as a **True Positive**.

### Classification Rationale

The investigation identified multiple pieces of supporting evidence:

1. The email used a package-delivery phishing theme.
2. The sender used the suspicious address `urgents@amazon.biz`.
3. The message presented itself as an Amazon delivery notification.
4. The email contained a shortened Bitly URL.
5. Splunk confirmed the associated email event.
6. Firewall telemetry showed an attempted connection to the same URL.
7. The firewall blocked the connection.
8. The case report identified the possibility that the user's account had been compromised.

The classification was therefore based on the combined email, SIEM, network and investigation evidence.

---

## 7. Escalation Assessment

### Escalation Reason

```text
Possibility of user being compromised
```

This represents the **escalation assessment/reason recorded by the simulator**.

It should not be interpreted as proof that a real-world production escalation was executed or that a confirmed account compromise occurred.

---

## 8. Recommended Remediation

The simulator case report listed the following recommended actions:

### 1. Force the user to reset the password

A password reset was recommended as a response measure due to the possibility of account compromise.

### 2. Implement MFA

Multi-factor authentication was recommended to strengthen account protection.

> **Note:** The available evidence does not demonstrate that the password reset or MFA implementation was actually performed. These were recommended remediation actions documented by the simulator.

---

## 9. Attack Indicators / IOCs

### Email Indicators

```text
Sender:
urgents@amazon.biz

Recipient:
h.harris@thetrydaily.thm
```

### URL Indicator

```text
http://bit.ly/3sHkX3da12340
```

### Network Indicators

```text
Destination IP:
67.199.248.11

Source IP:
10.20.2.17

Destination Port:
80

Source Port:
34257

Protocol:
TCP
```

### Security Control

```text
Firewall Rule:
Blocked Websites
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
Splunk Investigation
    ↓
Firewall Correlation
    ↓
IP Reputation Enrichment
    ↓
IOC Extraction
    ↓
True Positive Classification
    ↓
Escalation Assessment
    ↓
Remediation Recommendation
    ↓
Case Documentation
```

---

## 11. Analyst Takeaways

### Correlation is critical

The phishing email alone provided suspicious indicators, but the related firewall event increased the investigative confidence by showing an attempted connection to the same URL.

### Reputation data must be interpreted carefully

The AbuseIPDB result did not independently prove malicious activity. Threat-intelligence results should be treated as supporting evidence and interpreted alongside email, network and other available telemetry.

### Security controls can prevent successful access

The firewall event showed that the connection to the suspicious URL was blocked by the `Blocked Websites` rule.

### Possible compromise affects escalation

The simulator identified the possibility of user compromise as the reason for escalation assessment.

---

## 12. MITRE ATT&CK Mapping

This case demonstrates behavior consistent with phishing and credential-oriented social engineering.

Potential ATT&CK techniques should only be mapped where supported by the evidence available in the investigation. This case primarily demonstrates analysis of a phishing email and suspicious web destination; the evidence does not independently establish successful credential theft or account compromise.

---

## 13. Evidence

Supporting screenshots are stored in:

```text
evidence/alert-8815/
```

Suggested evidence files:

```text
evidence/alert-8815/
├── 01-alert.png
├── 02-splunk-email-search.png
├── 03-firewall-correlation.png
├── 04-abuseipdb.png
└── 05-case-report.png
```

The complete screenshot collection is also available in:

```text
reports/soc_alert_images.pdf
```

---

## 14. Environment and Limitations

This investigation was conducted in a **simulated SOC environment** as part of a SOC Immersive Simulation.

The investigation evidence should therefore not be presented as a real corporate security incident.

The recommended remediation actions documented in the case report are recommendations from the simulation; the available evidence does not demonstrate that those actions were actually deployed.

The investigation screenshots also represent the observed investigation scope and should not be interpreted as proof that no additional activity existed outside that scope.

---

## 15. Skills Demonstrated

* SOC Alert Triage
* Phishing Investigation
* Email Security Analysis
* Splunk SIEM
* SPL Searching
* Log Analysis
* Firewall Log Analysis
* Event Correlation
* IOC Extraction
* IP Reputation Analysis
* Threat Intelligence
* True Positive / False Positive Classification
* Escalation Assessment
* Incident Documentation
* Incident Response Fundamentals

---

## Conclusion

Alert 8815 was investigated as a package-delivery phishing attempt.

The investigation combined email analysis, Splunk telemetry, firewall correlation and IP reputation enrichment. The email contained a shortened Bitly URL, and firewall telemetry showed an attempted connection to the associated destination that was blocked.

Based on the combined evidence documented during the simulation, the alert was classified as a **True Positive**.

The case also demonstrated the importance of correlating multiple telemetry sources and treating threat-intelligence reputation results as supporting evidence rather than as the sole basis for classification.
