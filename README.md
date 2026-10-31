# Dispatch

Dispatch is an independent, Dispatch-to-Dispatch communications platform. It is designed to provide its own personal accounts, `@dispatch.com` identities, messaging, People/address book, calendars, event scheduling, and organization workspaces with verified affiliation and organization-specific Dispatch namespaces (for example, `@house.dp`).

**Brand**
- Navy: `#002147`
- Surface: `#F8FAF9`

## Product direction

- Users create accounts directly with Dispatch; no Gmail, Outlook, Yahoo, or other existing email account is required.
- The initial network is Dispatch-to-Dispatch only. Users message other active Dispatch identities, not external email providers.
- Personal identities can request affiliation with organizations. Approval is controlled by authorized organization administrators.
- Organizations can have their own Dispatch namespace, administrators, member directory, shared mailboxes, and calendars.
- Organization verification and individual membership/role attestation are separate trust decisions.
- A person's personal identity remains intact when an organization address is granted or revoked.

## Detailed specification

See [docs/PRODUCT_SPEC.md](docs/PRODUCT_SPEC.md) for the detailed product requirements, workflows, roles, verification model, security and authorization rules, suggested data model, technical architecture, roadmap, and acceptance criteria.

## Current implementation status

The current `index.html` is an early front-end prototype with seeded demonstration messages and browser-local persistence. It is **not yet** a production email service: it does not yet provide real registration, server-backed identities, cross-device messaging, organization administration, People, or calendar functionality. Do not use real or sensitive information in the demo.

## Important namespace note

The proposed `.dp` addresses are Dispatch-internal identities and resolve only within the Dispatch network in the initial product. This does not claim that `.dp` is a publicly registered DNS top-level domain or that these addresses can receive mail from the public internet. Public email interoperability would be a separate product decision and is explicitly outside the initial scope.

## Suggested implementation foundation

A production implementation needs secure authentication, a shared database with strict tenant isolation and row-level authorization, server-side message delivery, private attachment storage and scanning, realtime updates, background jobs, audit logging, abuse controls, backups, and operational monitoring. The product specification describes one practical architecture and a phased build plan.

## Run the current prototype

Open `index.html` in a browser, or serve the repository with any static file server. No build step is required for the current prototype.
