# SmartDesk — IT Support Email Automation (n8n)

A practical n8n project exploring email-based IT support intake, **AI-assisted triage** and **scheduled follow-up reporting**. Built and tested locally with n8n in Docker, Gmail, Google Sheets and Google Gemini.

> **Portfolio / demonstration project, not a production ticketing platform.** These files represent stages of a prototype and share the same example SmartDesk sheet structure. They are not intended to be enabled together without reviewing their duplicate intake triggers.

## Workflows

| File | Purpose | Trigger |
| --- | --- | --- |
| [01-basic-email-ticket-intake.json](01-basic-email-ticket-intake.json) | Baseline: captures new labelled Gmail enquiries, maps sender, subject, message ID and snippet, then appends or updates a ticket row in Google Sheets. Uses Gmail message ID for matching and defaults tickets to **Normal / Open** with a one-day follow-up. | Gmail trigger (polls for matching label) |
| [02-ai-assisted-ticket-triage.json](02-ai-assisted-ticket-triage.json) | Extended intake: checks for an existing ticket, uses **Gemini** with a structured JSON output parser to classify **category**, **priority**, **summary** and **human-review flag**, writes to Sheets, acknowledges the customer, and attempts a one-time internal alert for urgent tickets. | Gmail trigger (polls for matching label) |
| [03-daily-overdue-ticket-report.json](03-daily-overdue-ticket-report.json) | Reads ticket rows, filters out resolved/closed tickets, selects overdue **Open / In Progress** items, sorts by priority and emails an internal daily summary. | Scheduled trigger (08:00 in the exported settings) |

### Flow 1 — Basic email intake

```text
Gmail trigger → Normalise email fields → Google Sheets append/update
```

This is the simpler earlier stage, suitable for demonstrating a Gmail-to-Sheets integration. It does **not** classify messages with AI or send a customer reply.

### Flow 2 — AI-assisted ticket triage

```text
Gmail trigger → Normalise email → Check existing ticket → New ticket?
  → Gemini LLM + structured output parser → Prepare ticket data
  → Google Sheets → Customer acknowledgment
                  → Urgent? → Check notification → Internal Gmail alert
```

The model is instructed to select a category (Networking, Hardware, Software, Printer, Email or Other), priority (Low, Normal, High or Urgent), a short summary and whether manual review is needed. Emails are treated as **untrusted input**. The separate branches are intended to avoid duplicate tickets and duplicate urgent notifications; they still need real-world validation for edge cases.

The exported acknowledgment is demo copy and should be customised for the organisation using it. The workflow is **not** a complete IT service-management application.

### Flow 3 — Daily overdue follow-ups

```text
Scheduled 08:00 → Read Google Sheets → Filter overdue tickets
              → Rank and build report → Email internal support team
```

Only unresolved tickets marked `Open` or `In Progress` with a valid `ticket_id` and past `follow_up_due_at` are included. Urgent tickets appear before High, Normal and Low tickets. The report is sent internally, **not to the customer**.

## Import and configure

1. Run an n8n instance (the original development environment was local **Docker Desktop**).
2. Import an individual `.json` file through **Import from File** in the n8n editor.
3. Connect your **own** Gmail OAuth2, Google Sheets OAuth2 and, for Flow 2, Google Gemini credentials.
4. Replace `REPLACE_WITH_GMAIL_LABEL_ID` in Gmail Trigger nodes with a label from your mailbox and `REPLACE_WITH_SHEET_ID` in Google Sheets nodes with your own spreadsheet ID. Verify the sheet tab matches your setup.
5. Configure internal notification addresses (the public exports use `support-team@example.com`).
6. Create the expected ticket columns in your sheet (see below). Test with disposable email messages and sample ticket data.
7. Check schedule timezone, test execution paths and error handling before activating any workflow.

### Expected Sheets fields

The workflows refer to these columns (depending on the stage):
`ticket_id`, `message_id`, `received_at`, `customer_email`, `subject`, `description`, `priority`, `status`, `category`, `assigned_to`, `summary`, `requires_human_review`, `follow_up_due_at`, `last_contact_at`, `notification_sent`, `acknowledgement_sent`.

The basic flow and AI flow overlap; use the AI variant as the more complete intake prototype, not alongside the basic intake on the same email label.

## Public-export safeguards

The exports have been renamed, **deactivated**, and stripped of local credential references, internal email destinations, Google Sheets document IDs, Gmail label IDs, instance metadata and webhook identifiers. They therefore **must be reconfigured after import**. Some node settings and mappings are deliberately retained to illustrate the original implementation.

Never store OAuth tokens, API keys, customer email content or real ticket records in Git. Keep screenshots and workflow exports free of sensitive data.

## Status and learning

This is a working-learning project covering Gmail OAuth integration, Google Sheets row matching, n8n conditional routing, structured LLM classification, automated responses and scheduled reporting. It was not developed into a full ticketing system with an SLA engine, technician portal, robust error recovery, role-based administration or production monitoring.

The original examples were tested locally. **Importability and production readiness of the sanitised exports have not been independently validated here.**
