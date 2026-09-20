# Investigation Timeline

## Overview

This timeline records the events and observations supported by the available phishing email evidence.

Only timestamps directly present in the evidence are included as confirmed timestamps. Events without an observed timestamp are marked as `Unknown`.

## Timeline

| Time | Event | Evidence | Status |
|---|---|---|---|
| `2026-09-21 09:15:05 +0530` | Email timestamp | `Date` header | Confirmed |
| `2026-09-21 09:15:10 +0530` | Mail server receipt | `Received` header | Confirmed |
| `Unknown` | URL presented | Email body | Event identified, timestamp unavailable |
| `Unknown` | Attachment referenced | Attachment field | Event identified, timestamp unavailable |
| `Unknown` | IOC extraction | PowerShell investigation | Investigation activity |
| `Unknown` | IOC export | `phishing-iocs.csv` | Investigation activity |
| `Unknown` | Attachment acquisition attempt | `Copy-Item` command | Attempt failed |

## Detailed Timeline

### 09:15:05 +0530 — Email Timestamp

The email contained:

    Date: Mon, 21 Sep 2026 09:15:05 +0530

This establishes the timestamp recorded by the email's Date header.

### 09:15:10 +0530 — Mail Server Receipt

The Received header contained:

    Received: from mail.example.net (203.0.113.10)
        by mx.example.org with ESMTPS;
        Mon, 21 Sep 2026 09:15:10 +0530

This provides the observed mail-server receipt time and associates the message with:

    203.0.113.10

### Unknown — URL Presented

The controlled phishing scenario specified:

    https://microsoft-login.example.com/verify?session=12345

The available evidence does not provide an independent timestamp for when the URL was presented to the recipient.

Therefore:

    Time: Unknown

The captured URL extraction command returned:

    https://example.com

This discrepancy is documented in the investigation notes.

### Unknown — Attachment Referenced

The email contained:

    Attachment:
    Invoice_September.pdf.exe

No independent timestamp was available for attachment delivery.

Therefore:

    Time: Unknown

The referenced attachment could not be acquired from the expected previous-lab location during the captured investigation.

### Unknown — IOC Extraction

PowerShell commands were used to extract:

- URLs
- Email addresses
- Domains
- Source IP

The investigation produced observable extraction results including:

    account-review@example.net
    security@example.net
    user@example.org

and:

    example.net
    example.org
    header.from
    mail.example.net
    microsoft-login.example.com
    mx.example.org
    pdf.exe
    smtp.mailfrom

No execution timestamp was captured for these analyst actions.

### Unknown — IOC Export

The IOC collection was exported to:

    .\iocs\phishing-iocs.csv

The captured export contained fields for:

- IP
- URL
- Domain
- Filename
- SHA256

Several values were incomplete or empty.

The export therefore represents an intermediate investigation artifact rather than a fully validated IOC dataset.

### Unknown — Attachment Acquisition Attempt

The investigation attempted to copy:

    C:\SOC-Lab\Day46-Attachment-Analysis\Invoice_September.pdf.exe

into:

    .\evidence\Invoice_September.pdf.exe

The operation failed because the source file could not be found.

A subsequent attempt to access the destination file also failed.

This means that the attachment was referenced by the email, but the actual file was not successfully acquired during the captured session.

## Evidence-Based Sequence

    2026-09-21 09:15:05 +0530
            |
            v
    Email timestamp recorded
            |
            v
    2026-09-21 09:15:10 +0530
            |
            v
    Mail server receipt recorded
            |
            v
    URL and attachment referenced
            |
            v
    IOC extraction performed
            |
            v
    IOC export attempted
            |
            v
    Attachment acquisition attempted
            |
            v
    Attachment unavailable

## Timeline Limitations

The following timestamps were not available:

- URL presentation time
- Attachment delivery time
- IOC extraction time
- IOC export time
- Attachment acquisition attempt time

These values are intentionally left as `Unknown`.

No user interaction timestamp was available.

No evidence establishes:

- URL accessed
- Attachment opened
- Attachment executed
- Credentials submitted
- Endpoint compromised
- Account compromised

## Timeline Conclusion

The available evidence establishes the sequence of email creation/receipt and subsequent analyst investigation, but it does not provide enough telemetry to reconstruct recipient interaction or endpoint activity.

The timeline therefore remains limited to events directly supported by the email evidence and captured investigation activity.
