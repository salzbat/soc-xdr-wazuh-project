# Task 3: SOAR Automation Using Shuffle

**Part of:** IT8510 Threat Intelligence & Threat Hunting — Group Project (Part B, Task 3)
**My contribution:** Full SOAR pipeline — Shuffle setup, Wazuh webhook integration, VirusTotal enrichment, and automated email response.

> **Note:** a few screenshots contained a live webhook URL, personal email, account name, or student ID and have been redacted before publishing — flagged `-REDACTED` in the filename. The workflow logic and evidence of successful execution are unaffected.

## Objective

Implement Security Orchestration, Automation, and Response (SOAR) using **Shuffle**, integrated with Wazuh: detect a threat, raise an alert, trigger an automated playbook, enrich it with threat intelligence, and execute an automated response — with evidence of the full working chain.

## Architecture

```
Wazuh (FIM detects suspicious file)
   → Webhook alert sent to Shuffle
      → Shuffle enriches file hash via VirusTotal API
         → Automated email notification sent to analyst
```

Environment: a Windows Server running the Wazuh agent, a Wazuh manager (SIEM), and a Shuffle cloud instance for automation — connected via webhook.

## 1. Active Agent Check

Confirmed the Wazuh agent was active and connected on the Windows Server before building anything on top of it.

| | |
|---|---|
| ![Active agent dashboard](images/01-active-windows-agent-dashboard.png) | ![Agent running](images/02-wazuh-agent-running-powershell.png) |

## 2. File Integrity Monitoring (Detection Trigger)

Wazuh's FIM (`syscheck`) was configured on the Windows agent to monitor the Downloads directory in real time.

![FIM syscheck config](images/03-fim-syscheck-config.png)

A test file write confirmed FIM fires correctly and produces a real-time alert, before wiring it into the automated pipeline.

| Trigger | Resulting alert |
|---|---|
| ![Test file trigger](images/04-fim-test-file-trigger.png) | ![FIM alert](images/05-fim-alert-single-event.png) |

Multiple file events (create, modify, delete) were logged correctly by Wazuh:

![Multiple FIM events](images/06-fim-multiple-events-log.png)

## 3. Shuffle Setup & Workflow Creation

A Shuffle account and workflow were created to receive and process Wazuh alerts — this is the automation canvas the rest of the pipeline runs on.

| Dashboard | New workflow |
|---|---|
| ![Shuffle dashboard](images/07-shuffle-dashboard-find-apps-REDACTED.png) | ![New workflow](images/08-shuffle-new-workflow-creation.png) |

## 4. Wazuh → Shuffle Webhook Integration

The Wazuh manager was configured to POST alerts to a Shuffle webhook trigger whenever FIM detects a file event, so alerts flow into the automation pipeline with no manual step.

| Webhook trigger in Shuffle | Wazuh-side XML config |
|---|---|
| ![Webhook trigger](images/09-webhook-trigger-config-REDACTED.png) | ![Wazuh webhook XML](images/10-wazuh-webhook-integration-xml-REDACTED.png) |

The webhook payload was inspected in Shuffle to confirm the full alert context — file path, MD5 hash, rule ID, agent info — arrives correctly for downstream processing.

![Webhook payload analysis](images/11-webhook-payload-analysis.png)

## 5. Workflow Design & Execution

The playbook chains three steps: **webhook trigger → VirusTotal lookup → email notification**.

![Workflow design diagram](images/12-workflow-design-diagram.png)

Execution logs confirm the workflow ran successfully and repeatedly in response to real Wazuh alerts.

![Workflow execution runs](images/13-workflow-execution-runs-log.png)

## 6. VirusTotal Threat Intelligence Integration

A VirusTotal account and API key were set up and connected to Shuffle, enabling automatic reputation lookups on file hashes received from Wazuh alerts.

| Account created | API key page |
|---|---|
| ![VirusTotal account](images/14-virustotal-account-created-REDACTED.png) | ![API key page](images/15-virustotal-apikey-page-REDACTED.png) |

The key was entered into Shuffle's VirusTotal integration (masked in the input field), and the lookup action was configured to automatically pull the MD5 hash out of the incoming Wazuh alert (`$exec.all_fields.syscheck.md5_after`) — no manual hash copying.

| Auth config in Shuffle | Hash lookup config |
|---|---|
| ![VirusTotal auth](images/16-shuffle-virustotal-auth-config.png) | ![Hash lookup config](images/17-virustotal-hash-lookup-config.png) |

## 7. End-to-End Validation (EICAR Test)

The [EICAR test file](https://en.wikipedia.org/wiki/EICAR_test_file) was downloaded into the monitored directory to trigger the full chain safely.

![EICAR download](images/18-eicar-download-powershell.png)

Shuffle's VirusTotal step returned a `200` success response, confirming the hash was received and analyzed.

![VirusTotal execution result](images/19-virustotal-execution-result.png)

## 8. Automated Response — Email Notification

The final step in the playbook sends an automated email alert containing the file path, MD5 hash, VirusTotal detection count, and timestamp — giving an analyst everything needed to triage without touching any tool manually.

| Notification config | Delivered result |
|---|---|
| ![Email config](images/20-email-notification-config-REDACTED.png) | ![Email result](images/21-automated-email-result.png) |

The test run confirmed: **66 antivirus engines** flagged the EICAR file as malicious, and the alert email was generated and delivered automatically.

## Key Takeaways

- Built a **fully automated SOAR pipeline** — from raw FIM event to enriched, human-readable alert — with zero manual intervention between detection and notification.
- Used **dynamic variable extraction** (`$exec.all_fields.syscheck.md5_after`) to pass data between playbook steps, rather than hardcoding values, so the workflow generalizes to any file event.
- Validated the pipeline safely using the industry-standard EICAR test file rather than real malware.
