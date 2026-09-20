# phishing-email-investigation-lab

A phishing investigation is the process of determining whether an email is attempting to deceive the recipient into taking an unsafe action, such as:

Visiting a fraudulent website
Entering credentials
Opening a malicious attachment
Sending sensitive information
Making a payment

A SOC analyst should not decide that an email is phishing simply because it looks suspicious.

The investigation should follow the evidence:

Email
  ↓
Initial triage
  ↓
Headers
  ↓
SPF / DKIM / DMARC
  ↓
URLs
  ↓
Domains / redirects
  ↓
Attachments
  ↓
Hashes / IOCs
  ↓
Correlation
  ↓
Timeline
  ↓
Final assessment

The central principle for the project is:

Follow the evidence, not the assumption.

A single indicator does not necessarily prove phishing.

For example:

SPF = PASS

does not mean:

Email = legitimate

Similarly:

URL contains "login"

does not mean:

URL = malicious

And:

Attachment = .docm

does not automatically mean:

Attachment = malware

The SOC analyst combines multiple observations.

For example:

Urgent account verification request
+
Brand-like hostname
+
Unrelated registered domain
+
Executable attachment
+
Suspicious filename

provides much stronger evidence than any one indicator by itself.

An end-to-end SOC investigation of a controlled phishing email. The lab focuses on email triage, header analysis, authentication checks, IOC extraction, artifact correlation, timeline reconstruction, and evidence-based assessment.

The investigation was performed in a controlled lab environment using a simulated phishing email containing suspicious sender information, a Microsoft-looking hostname, an executable attachment name, and a controlled source IP.

# Lab Objectives

- Examine a simulated phishing email from a SOC analyst perspective.
- Identify suspicious characteristics in the sender, Reply-To address, subject, and email content.
- Analyze relevant email headers to identify message-routing and source information.
- Review SPF, DKIM, and DMARC results and understand what the authentication results do and do not establish.
- Extract email addresses, URLs, domains, IP addresses, and attachment information from the available evidence.
- Break down suspicious URLs into their individual components and examine hostname versus registered-domain relationships.
- Validate automatically extracted artifacts against their original email context before treating them as IOCs.
- Investigate the referenced attachment and document acquisition or analysis failures when the actual file is unavailable.
- Organize identified artifacts into a structured IOC dataset while distinguishing complete, incomplete, and unverified values.
- Correlate email, network, URL, domain, and attachment artifacts within the same investigation.
- Construct a timeline using only timestamps supported by the available evidence.
- Distinguish confirmed observations from assumptions about user interaction, execution, credential theft, or compromise.
- Document investigation gaps and troubleshooting findings without replacing missing evidence with assumed results.
- Produce an evidence-based assessment that reflects both the identified phishing indicators and the limitations of the investigation.
  
# Lab Scenario

A controlled phishing email was introduced into the investigation environment to simulate a SOC analyst receiving a potentially malicious message. The email claimed to require Microsoft account verification and contained characteristics that required validation rather than immediate classification.

The investigation focused on examining the message and its available artifacts, including:

- Sender and Reply-To addresses
- Email headers and authentication results
- Embedded URLs and associated domains
- Source IP information
- The suspicious attachment `Invoice_September.pdf.exe`
- Extracted and exported IOC values
- Available timestamps for timeline reconstruction

The analyst was expected to correlate these artifacts and determine which observations were supported by the available evidence. Particular attention was given to the difference between the visible Microsoft-themed hostname and its registered domain, as well as the mismatch between the sender and Reply-To addresses.

The investigation also included validation of the referenced attachment. The attachment could not be retrieved from the expected evidence locations, so file-level analysis could not be completed. As a result, no SHA-256 hash, file signature, PE validation, or execution evidence could be established.

The scenario intentionally required the analyst to distinguish confirmed observations from assumptions. There was no evidence available to confirm that the recipient opened the attachment, accessed the URL, submitted credentials, executed a file, or experienced endpoint or account compromise.

The final assessment should therefore reflect both the suspicious characteristics identified during email triage and the limitations created by missing or incomplete evidence.

> **Investigation principle:** Follow the evidence, not the assumption.


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

