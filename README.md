# n8n Automation Projects

A collection of hands-on **n8n workflow automation** projects covering integrations, data processing, scheduled jobs and AI-assisted workflows. Each project has its own folder, importable example exports and documentation.

## Projects

| Project | Description |
| --- | --- |
| [IT Support Ticketing — SmartDesk](it-support-ticketing/) | Email-to-ticket logging, AI-assisted classification, acknowledgements, urgent alerts and daily overdue-ticket reporting using Gmail, Google Sheets, Google Gemini and n8n. |
| [Supplier Invoice Processing — AI-powered Accounts Payable](supplier-invoice-processing/) | PDF invoice extraction with Gemini, financial validation, Google Drive archiving, human approval and draft bill creation in Xero through a modular n8n sub-workflow. |

## About this collection

These are technical demonstrations and learning projects, **not packaged production services**. The workflow exports have been sanitised for public sharing: credentials, Google resource identifiers, Gmail label IDs and personal notification addresses have been removed or replaced with placeholders. Reconnect your own credentials and update configuration before activating imported workflows.

New n8n projects will be added in separate folders. Each folder will document the implementation, limitations and setup requirements.

> **Security:** Never commit credential values, tokens, customer messages or unsanitised n8n instance exports. Imported workflows can send messages or update sheets after activation; test them with non-production accounts and data first.
