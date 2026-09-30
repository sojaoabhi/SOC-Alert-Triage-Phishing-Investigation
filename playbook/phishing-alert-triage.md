# Phishing Alert Triage Playbook

## Step 1 - Validate the Alert

Record:

- Alert ID
- Severity
- Timestamp
- Sender
- Recipient
- Subject
- URL
- Attachment status
- Direction

## Step 2 - Analyze the Email

Review:

- Sender/domain
- Recipient
- Subject
- Message content
- Embedded URLs
- Attachments
- Claimed identity

## Step 3 - Search SIEM

Search by:

- Sender
- Recipient
- URL
- Source IP
- Destination IP

## Step 4 - Correlate Network Activity

Check:

- Firewall action
- Source IP
- Destination IP
- Port
- Protocol
- URL
- Security rule

## Step 5 - Enrich Indicators

Use reputation/intelligence sources as supporting evidence.

## Step 6 - Classify

Possible outcomes:

- False positive
- True positive

## Step 7 - Assess Escalation

Consider potential account compromise and related activity.

## Step 8 - Document

Record:

- Classification rationale
- Evidence
- Attack indicators
- Escalation reason
- Recommended remediation