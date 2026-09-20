# Phishing Email Investigation Lab

An end-to-end SOC investigation of a controlled phishing email. The lab focuses on email triage, header analysis, authentication checks, IOC extraction, artifact correlation, timeline reconstruction, and evidence-based assessment.

The investigation was performed in a controlled lab environment using a simulated phishing email containing suspicious sender information, a Microsoft-looking hostname, an executable attachment name, and a controlled source IP.

## Objectives

- Perform initial phishing email triage.
- Analyze sender, Reply-To, and Received headers.
- Review SPF, DKIM, and DMARC results.
- Extract URLs, domains, and email addresses from raw email evidence.
- Analyze the hostname and registered domain used in the URL.
- Identify attachment-related indicators.
- Build and export a structured IOC list.
- Correlate email, URL, domain, IP, and attachment artifacts.
- Reconstruct an evidence-based timeline.
- Document investigation limitations without overclaiming compromise.

## Lab Scenario

A controlled email was created to simulate an account-verification phishing attempt.

The message claimed to require immediate Microsoft account verification and contained a Microsoft-looking hostname under the controlled `example.com` domain. The email also referenced an executable attachment named `Invoice_September.pdf.exe`.

Each artifact was investigated independently before correlation. This helped separate observed indicators from assumptions about user interaction, execution, or compromise.

## Controlled Email Details

| Artifact | Value |
|---|---|
| Sender | `security@example.net` |
| Reply-To | `account-review@example.net` |
| Recipient | `user@example.org` |
| Subject | `Urgent: Verify Your Microsoft Account` |
| Source IP | `203.0.113.10` |
| URL | `https://microsoft-login.example.com/verify?session=12345` |
| Hostname | `microsoft-login.example.com` |
| Registered Domain | `example.com` |
| Attachment | `Invoice_September.pdf.exe` |
| SPF | PASS |
| DKIM | PASS |
| DMARC | PASS |

All domains and IP addresses used in this scenario are controlled lab artifacts.

## Investigation Workflow

    Controlled Email
          |
          v
    Initial Triage
          |
          v
    Sender / Reply-To Analysis
          |
          v
    Received Header Analysis
          |
          v
    SPF / DKIM / DMARC
          |
          v
    URL Extraction
          |
          v
    Domain Analysis
          |
          v
    Attachment Investigation
          |
          v
    IOC Extraction
          |
          v
    IOC Correlation
          |
          v
    Timeline Reconstruction
          |
          v
    Final Assessment

## Key Findings

### Sender Analysis

The message used:

    From: security@example.net
    Reply-To: account-review@example.net

The displayed sender identity claimed to represent Microsoft, while the actual sender domain was `example.net`.

The Reply-To address was also different from the sender address.

### Authentication Results

The captured email contained:

    SPF: PASS
    DKIM: PASS
    DMARC: PASS

These results were recorded as authentication observations.

They were not treated as proof that the message itself was legitimate. Authentication results describe characteristics of the sending domain and message alignment but do not independently establish that the content or claimed organization is trustworthy.

### URL Analysis

The intended controlled URL was:

    https://microsoft-login.example.com/verify?session=12345

Its components were:

| Component | Value |
|---|---|
| Scheme | `https` |
| Hostname | `microsoft-login.example.com` |
| Registered Domain | `example.com` |
| Path | `/verify` |
| Parameter | `session=12345` |

The hostname contains a Microsoft-looking name while the registered domain is `example.com`.

This represents a domain-deception indicator within the controlled scenario.

### Captured URL Extraction Result

The terminal capture from the investigation returned:

    https://example.com

when the URL extraction command was executed.

This differs from the URL specified in the controlled scenario. The discrepancy was retained as an investigation observation rather than silently corrected.

### Domain Extraction

The domain extraction command returned:

    example.net
    example.org
    header.from
    mail.example.net
    microsoft-login.example.com
    mx.example.org
    pdf.exe
    smtp.mailfrom

The output demonstrates why regex-based extraction requires contextual review. Not every string returned by a domain-oriented regex represents a confirmed IOC.

### Email Extraction

The investigation extracted:

    account-review@example.net
    security@example.net
    user@example.org

These addresses were reviewed according to their role in the email rather than automatically classified as malicious.

### Attachment Investigation

The email referenced:

    Invoice_September.pdf.exe

An attempt was made to retrieve the attachment from:

    C:\SOC-Lab\Day46-Attachment-Analysis\Invoice_September.pdf.exe

The file was not found.

A subsequent attempt to access:

    .\evidence\Invoice_September.pdf.exe

also failed.

Therefore, the captured investigation does not contain a verified SHA-256 hash, PE/MZ signature, or digital-signature result for the attachment.

## IOC Status

| Type | Value | Status |
|---|---|---|
| IP | `203.0.113.10` | Identified in email headers |
| URL | `https://example.com` | Captured by extraction command |
| Intended URL | `https://microsoft-login.example.com/verify?session=12345` | Present in controlled scenario |
| Domain | `microsoft-login.example.com` | Identified from scenario/domain analysis |
| Filename | `Invoice_September.pdf.exe` | Identified from email |
| SHA-256 | Not captured | Attachment unavailable |

## Timeline Summary

The captured evidence established:

    2026-09-21 09:15:05 +0530
    Email timestamp

    2026-09-21 09:15:10 +0530
    Mail server receipt

The following events did not have independent timestamps:

    URL presented        → Unknown
    Attachment delivered → Unknown

No timestamps were invented to complete the timeline.

## Investigation Assessment

The controlled email contained multiple characteristics consistent with a phishing attempt:

- Urgent account-verification language.
- A Microsoft-looking hostname under the controlled `example.com` domain.
- A different Reply-To address.
- An executable attachment filename using a PDF-like naming convention.
- Associated email and network artifacts.

The available evidence supports identifying the message as consistent with a phishing attempt within the controlled lab scenario.

However, the investigation does not establish:

- User opened the attachment.
- User accessed the URL.
- User entered credentials.
- The attachment executed.
- Malware executed.
- The endpoint was compromised.
- The account was compromised.

These outcomes would require additional endpoint, authentication, or execution evidence.

## Investigation Limitations

- The attachment file was unavailable in the expected Day 46 location.
- The attachment could not be copied into the Day 49 evidence directory.
- A SHA-256 hash was not captured.
- The attachment's PE/MZ signature was not independently verified.
- The URL extraction output differed from the URL specified in the controlled scenario.
- Some regex results required contextual filtering.
- The captured IOC CSV was incomplete.
- No evidence demonstrated user interaction or endpoint compromise.

## Key SOC Principle

> Follow the evidence, not the assumption.

This investigation demonstrates the importance of separating observed indicators from confirmed impact and documenting evidence gaps instead of filling them with assumptions.

