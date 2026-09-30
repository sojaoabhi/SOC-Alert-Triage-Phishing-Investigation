# Alert 8814 - Onboarding Email False Positive

## Summary

An inbound email triggered a suspicious-link phishing alert.

## Alert Metadata

| Field | Value |
|---|---|
| Alert ID | 8814 |
| Severity | Medium |
| Type | Phishing |
| Sender | onboarding@hrconnex.thm |
| Recipient | j.garcia@thetrydaily.thm |
| Direction | Inbound |

## Investigation

1. Reviewed alert details.
2. Searched the recipient's email activity in Splunk.
3. Reviewed surrounding organizational communications.
4. Compared the suspicious message with the user's normal activity.

## Findings

The email occurred within a legitimate onboarding context.

## Classification

False Positive

## Analyst Rationale

The alert was triggered by the presence of an external URL, but surrounding
telemetry supported legitimate onboarding activity.

## Closure

Closed as false positive.