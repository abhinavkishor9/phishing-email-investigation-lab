# Troubleshooting Notes

This document records issues encountered during the phishing email investigation and how they affected the evidence.

## 1. Source IP Variable Returned Empty

The investigation attempted to extract the source IP using:

    $sourceIP = (
        Select-String `
            -Path ".\evidence\phishing-email.txt" `
            -Pattern "^Source IP:"
    ).Line -replace "^Source IP:\s*", ""

The captured terminal output did not display the expected value after the variable was evaluated.

However, the email evidence contained the source IP:

    203.0.113.10

The Received header also contained:

    203.0.113.10

### Impact

The source IP was observable in the email evidence, but the variable extraction/export stage did not successfully populate the value in the captured IOC table.

### Lesson

When an extraction variable appears empty, verify the original evidence directly before treating the value as unavailable.

## 2. URL Extraction Did Not Match the Controlled Scenario

The controlled scenario specified:

    https://microsoft-login.example.com/verify?session=12345

The captured URL extraction returned:

    https://example.com

A parameter search also returned:

    evidence\phishing-email.txt:25:https://example.com

### Impact

The complete controlled URL was not reproduced by the captured extraction command.

### Lesson

Do not silently replace command output with the expected value.

The discrepancy should remain documented so that the investigation record reflects the actual evidence observed during execution.

## 3. Domain Regex Produced Non-IOC Strings

The domain extraction returned:

    example.net
    example.org
    header.from
    mail.example.net
    microsoft-login.example.com
    mx.example.org
    pdf.exe
    smtp.mailfrom

Some results are valid domain-like strings, while others are field names or filename-related text.

### Impact

The raw regex output could not be treated as a final IOC list.

### Lesson

Regex extraction is an initial collection step.

Every extracted value should be reviewed against its source context before being promoted to a confirmed IOC.

## 4. Attachment File Was Missing

The investigation attempted:

    Copy-Item `
        "C:\SOC-Lab\Day46-Attachment-Analysis\Invoice_September.pdf.exe" `
        ".\evidence\Invoice_September.pdf.exe" `
        -Force

The command failed because the source file did not exist at the specified location.

A subsequent command attempted to access:

    .\evidence\Invoice_September.pdf.exe

This also failed because the file had not been copied successfully.

### Impact

The attachment could not be independently analyzed during the captured session.

The following could therefore not be verified:

- SHA-256
- MZ/PE signature
- Authenticode signature
- File metadata

### Lesson

An attachment filename in an email is evidence of a referenced attachment, not proof that the underlying file has been successfully acquired.

## 5. IOC CSV Was Incomplete

The captured IOC export displayed:

    Type        Value                              Source
    ----        -----                              ------
    IP                                             Email
    URL         h                                  Email body
    Domain      microsoft-login.example.com        URL
    Filename    Invoice_September.pdf.exe          Attachment
    SHA256                                         Attachment

The IP and SHA-256 values were empty.

The URL value was also incomplete.

### Impact

The CSV should not be presented as a fully validated IOC dataset.

### Lesson

Always inspect the exported IOC file after creation.

An export operation succeeding does not mean the underlying data was correctly populated.

## 6. Expected Attachment Analysis Could Not Be Completed

The planned workflow included:

    File acquisition
          |
          v
    File signature check
          |
          v
    SHA-256 calculation
          |
          v
    Digital signature check

The captured investigation stopped before these steps could produce verified results because the attachment was unavailable.

### Correct Documentation

| Item | Status |
|---|---|
| Attachment referenced | Confirmed |
| Attachment file acquired | Not confirmed |
| SHA-256 | Not captured |
| File signature | Not captured |
| Digital signature | Not captured |

This avoids turning a planned analysis step into an undocumented finding.

## 7. Timeline Contained Unknown Events

The timeline included:

    2026-09-21 09:15:05 +0530
    Email timestamp

    2026-09-21 09:15:10 +0530
    Mail server receipt

The following events had no independent timestamp:

- URL presented
- Attachment delivered

### Lesson

Unknown timestamps should remain `Unknown`.

No timestamp should be created simply to make the timeline appear complete.

## 8. Investigation Principle

The main troubleshooting lesson from the lab was:

> When evidence and expectations differ, document the evidence rather than forcing the expected result.

This applies to:

- Empty variables
- Unexpected regex output
- Missing files
- Incomplete IOC exports
- Missing timestamps
- Differences between planned and observed results

The resulting investigation is more defensible when limitations are explicitly recorded.
