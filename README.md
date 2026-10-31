# Dispatch

Dispatch is an independent, Dispatch-to-Dispatch communications platform with its own personal identities, messaging, People directory, calendars, and organization workspaces.

## Product identity

- Personal addresses use the `@dispatch.com` namespace.
- Organization addresses use Dispatch-internal namespaces such as `@house.dp`.
- Dispatch is its own network: no Gmail, Outlook, Yahoo, IMAP, SMTP, or external email federation in the initial product.
- Organization affiliation, organization verification, and official role attestation are separate decisions.
- A person's personal identity remains intact if an organization address is granted or revoked.

## Current interface

The root `index.html` is a responsive Outlook-inspired, five-zone Dispatch workspace prototype:

1. Top command bar with Dispatch branding, global search, help, settings, notifications, and profile.
2. Application rail for Mail, Calendar, People, and Tasks.
3. Navy folder and category navigation with compose and storage indicator.
4. Message list with Focused/Other tabs, search, selection, flags, quick actions, and bulk actions.
5. Reading pane with message actions, sender details, body, and attachment placeholder.

The prototype also includes compose, reply, reply-all, forward, draft saving, folders, categories, archive, junk, trash, and keyboard shortcuts. Calendar, People, Tasks, and Settings have initial workspace screens.

### Important implementation boundary

This is still a **front-end demonstration**, not a production communications service. Mail data is stored in the current browser's local storage. Sending adds an item to local Sent Items; it does not deliver a message to another person. Calendar, People, Tasks, organization administration, notifications, and settings are UI foundations/placeholders. There is no real account registration, authentication, cross-device synchronization, server-side message delivery, attachment upload/scanning, or organization verification workflow yet. Do not enter real or sensitive information.

## Detailed product specification

See [docs/PRODUCT_SPEC.md](docs/PRODUCT_SPEC.md) for product requirements, identity and affiliation workflows, organization verification, messaging, People, Calendar, administration, data model, authorization, architecture, roadmap, and acceptance criteria.

## Namespace note

The proposed `.dp` addresses are internal Dispatch identities in the initial product. This does not claim that `.dp` is a publicly registered DNS top-level domain or that these addresses can receive mail from the public internet.

## Production architecture direction

A production implementation needs secure authentication, Postgres with strict row-level authorization, trusted server-side message delivery, private attachment storage and scanning, realtime updates, background jobs, audit logging, abuse controls, backups, and operational monitoring. Never put service-role credentials in browser code. All personal and organization data must be tenant-isolated and permission-checked on the server.

## Run the prototype

Open `index.html` in a browser or serve the repository with any static file server. No build step is required.
