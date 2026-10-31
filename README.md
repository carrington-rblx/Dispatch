# Dispatch

Dispatch is an email-client interface prototype using the requested brand palette:
- Navy: `#002147`
- Surface: `#F8FAF9`

## Current implementation
A responsive, browser-based mail workspace with seeded demo messages and local persistence. It includes Inbox, Starred, Drafts, Sent, Archive, Spam, and Trash navigation; search; unread filtering; starring; message reading; compose; reply; forward; draft saving; and moving messages between folders.

## Important: real email delivery is not connected yet
This repository currently contains a front-end prototype. Messages are stored in the browser's localStorage; sending creates a local Sent item and does **not** deliver email. Receiving, mailbox synchronization, attachments, contacts, server-side search, spam filtering, account authentication, and multi-device sync require a backend and a mail provider integration.

## Production architecture
To turn Dispatch into a real email service/client:
1. Add authentication (for example, Supabase Auth) and a Postgres schema for accounts, connected mailboxes, message metadata, folders, contacts, drafts, and sync cursors.
2. Connect mailboxes through OAuth: Microsoft Graph for Outlook/Microsoft 365 and Gmail API for Google accounts. For arbitrary providers, implement IMAP/SMTP with encrypted credential/token storage and provider-specific OAuth where available.
3. Implement provider webhooks/delta sync and background workers for inbound sync, sent-message reconciliation, attachment storage, and retries.
4. Send through the connected provider (Graph sendMail, Gmail messages.send, or authenticated SMTP). Never expose provider tokens in the browser.
5. Add server-side authorization, encryption at rest, CSRF protection, rate limits, audit logging, attachment malware scanning, and robust MIME parsing/rendering before handling real user mail.

## Run locally
Open `index.html` in a browser, or serve the repository with any static file server. No build step is required for this prototype.
