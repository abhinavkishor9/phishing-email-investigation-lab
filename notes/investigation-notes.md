# Investigation Notes

## Investigation Overview

This investigation analyzed a controlled phishing email from a SOC analyst perspective.

The investigation followed an evidence-first workflow:

    Email
      |
      v
    Headers
      |
      v
    Authentication
      |
      v
    URL
      |
      v
    Domains
      |
      v
    Attachment
      |
      v
    IOCs
      |
      v
    Correlation
      |
      v
    Timeline
      |
      v
    Assessment

The objective was to identify observable phishing indicators while keeping the final assessment within the limits of the available evidence.

## Initial Triage

The controlled email contained:

    From:
    security@example.net

    Reply-To:
    account-review@example.net

    To:
    user@example.org

    Subject:
    Urgent: Verify Your Microsoft Account

The message used urgent account-verification language.

These observations were treated as suspicious characteristics during initial triage rather than immediate proof of phishing.

## Sender Analysis

The sender was:

    security@example.net

The Reply-To address was:

    account-review@example.net

The message claimed to represent Microsoft, while the sender domain was `example.net`.

The difference between the From and Reply-To addresses was recorded as an additional observation.

## Received Header

The captured Received header contained:

    Received: from mail.example.net (203.0.113.10)

The source IP identified from the header was:

    203.0.113.10

This was recorded as a network indicator associated with the controlled email.

## Authentication Analysis

The email contained:

    SPF: PASS
    DKIM: PASS
    DMARC: PASS

The results were documented as authentication observations.

They were not interpreted as:

    Email is legitimate

Instead, the authentication results were considered alongside the sender, URL, domain, and attachment observations.

## URL Analysis

The controlled scenario specified:

    https://microsoft-login.example.com/verify?session=12345

The URL components were:

| Component | Value |
|---|---|
| Scheme | `https` |
| Hostname | `microsoft-login.example.com` |
| Registered Domain | `example.com` |
| Path | `/verify` |
| Parameter | `session=12345` |

The hostname contains a Microsoft-looking name, while the registered domain is `example.com`.

This was recorded as a domain-deception indicator within the controlled lab.

## URL Extraction Result

The URL extraction command was executed against the email evidence.

The captured output was:

    https://example.com

This did not match the URL specified in the controlled scenario:

    https://microsoft-login.example.com/verify?session=12345

The discrepancy was retained rather than modifying the result to match the expected value.

This is important because an investigation record should reflect what the command actually returned.

## URL Parameter Check

A search for URL parameters was performed using:

    Select-String `
        -Path ".\evidence\phishing-email.txt" `
        -Pattern "\?|\&|session="

The captured output was:

    evidence\phishing-email.txt:25:https://example.com

The expected controlled parameter `session=12345` was therefore not visible in this captured command output.

## Email Address Extraction

The regex extraction produced:

    account-review@example.net
    security@example.net
    user@example.org

These addresses were reviewed in the context of the email rather than automatically treated as malicious indicators.

## Domain Extraction

The domain-oriented extraction returned:

    example.net
    example.org
    header.from
    mail.example.net
    microsoft-login.example.com
    mx.example.org
    pdf.exe
    smtp.mailfrom

The output contained both meaningful domains and strings that should not automatically be treated as confirmed IOCs.

For example:

    pdf.exe
    header.from
    smtp.mailfrom

require contextual interpretation.

This demonstrates why automated extraction should be followed by analyst validation.

## Attachment Analysis

The email referenced:

    Invoice_September.pdf.exe

The filename is notable because it presents a PDF-like name while ending in:

    .exe

An attempt was made to retrieve the controlled executable from:

    C:\SOC-Lab\Day46-Attachment-Analysis\Invoice_September.pdf.exe

The command returned a path-not-found error.

A second attempt to access:

    C:\SOC-Lab\Day49-Phishing-Email-Investigation\evidence\Invoice_September.pdf.exe

also failed.

Therefore, the actual file was not available during the captured investigation.

## Attachment Evidence Status

| Artifact | Status |
|---|---|
| Actual file | Not available |
| SHA-256 | Not captured |
| MZ/PE signature | Not independently verified |
| Authenticode result | Not captured |

The filename remains an observed email artifact but should not be confused with verified file-analysis evidence.

## IOC Extraction

The intended IOC structure contained:

    IP
    URL
    Domain
    Filename
    SHA256

The captured IOC export showed:

    Type        Value                              Source
    ----        -----                              ------
    IP                                             Email
    URL         h                                  Email body
    Domain      microsoft-login.example.com        URL
    Filename    Invoice_September.pdf.exe          Attachment
    SHA256                                         Attachment

The empty IP and SHA-256 fields indicate that the IOC generation/export stage was not fully populated in the captured session.

The URL field also did not contain the complete expected URL.

Therefore, the CSV should be treated as an incomplete investigation artifact rather than a fully validated IOC dataset.

## IOC Correlation

The evidence can be correlated at the email level:

    Controlled Email
    |
    ├── security@example.net
    |
    ├── account-review@example.net
    |
    ├── 203.0.113.10
    |
    ├── microsoft-login.example.com
    |
    └── Invoice_September.pdf.exe

These artifacts were associated with the same controlled phishing scenario.

Correlation does not establish that the recipient interacted with the URL or attachment.

## Assessment

The following observations were consistent with a phishing attempt in the controlled scenario:

    Urgent account-verification request
            +
    Microsoft-looking hostname
            +
    Registered domain unrelated to Microsoft
            +
    Different Reply-To address
            +
    Executable attachment filename
            +
    Associated source IP

The assessment is limited to the email and its observable artifacts.

There is no captured evidence demonstrating:

- Credential submission
- Attachment execution
- Malware execution
- Endpoint compromise
- Account compromise

## Evidence Confidence

### Confirmed From Captured Evidence

- Sender address
- Reply-To address
- Source IP appearing in the Received header
- SPF PASS
- DKIM PASS
- DMARC PASS
- Attachment filename
- Extracted email addresses
- Extracted domain strings
- Email and mail-server timestamps

### Partially Supported

- URL analysis
- IOC CSV generation
- Attachment analysis

### Not Demonstrated

- User interaction
- URL access
- Attachment execution
- Malware execution
- Credential theft
- Endpoint compromise
- Account compromise

## Final Investigation Principle

The investigation demonstrates the difference between:

    Indicator identified

and:

    Impact confirmed

The available evidence supports identifying multiple phishing indicators, but it does not support extending the conclusion to confirmed endpoint or account compromise.
