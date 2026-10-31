# Dispatch — Product and Platform Specification

## 1. Product definition

Dispatch is an independent communications platform with its own identity system, private messaging network, calendar, event scheduling, and address book. A person can create a Dispatch account without owning any other email account. Dispatch-to-Dispatch is the initial and intended messaging boundary: only active Dispatch identities can send messages to and receive messages from other Dispatch identities. External email federation (Gmail, Outlook, SMTP, etc.) is out of scope unless separately approved as a future product decision.

The interface uses:
- Primary navy: `#002147`
- Background / surface: `#F8FAF9`

The current static interface is a prototype only. This specification describes the product that must be built; it is not a claim that backend services already exist.

## 2. Identity and account types

### 2.1 Personal accounts

A person signs up directly with Dispatch, verifies a recovery/contact method, creates credentials, and chooses an available personal handle. Example: `danielthrown@dispatch.com`.

Required profile fields:
- Display name
- Unique Dispatch handle
- Profile photo (optional)
- Short biography (optional)
- Time zone and locale
- Account recovery method(s)
- Privacy and discoverability preferences

A user may have a stable internal user ID that never changes even if a permitted address/handle changes. Handles are unique, reserved against impersonation, and protected by cooldown/history rules.

### 2.2 Organization accounts

Dispatch supports organization workspaces for entities such as companies, nonprofits, campaigns, political parties, public offices, government departments, legislative offices, and other institutions. Organization accounts are not ordinary personal accounts with a logo: they have an organization profile, domain/namespace, administrators, membership controls, policies, and auditable verification status.

Examples of organization namespace addresses:
- `person@house.dp`
- `office@house.dp`
- `press@campaign.dp`
- `contact@company.dp`

The `.dp` suffix is a proposed Dispatch-internal namespace, not an assertion that `.dp` is a publicly registered DNS top-level domain. Initially these addresses should resolve only inside Dispatch. If public internet email or domain ownership is ever desired, it requires a separate domain strategy, DNS control, abuse operations, and external mail infrastructure.

An organization requests a namespace, for example `house.dp`. Dispatch must check uniqueness, reserved names, impersonation risk, and whether the applicant is authorized to represent the claimed entity. High-risk names (government bodies, elected offices, public institutions, political parties, major brands) require enhanced manual review and must not be issued solely because someone can pay or submit a form.

### 2.3 Organization roles

Suggested roles, scoped to each organization:
- Owner: legal/organizational responsibility; transfer and close workspace.
- Organization administrator: manage members, settings, namespace, policies.
- Membership approver: review affiliation requests.
- Identity verifier: review evidence and make verification decisions.
- Mailbox manager: create shared mailboxes and manage delegates.
- Calendar/event manager: manage shared calendars and events.
- Member: standard approved organization member.
- Guest: limited, time-bounded access where allowed.

Use least-privilege permissions. Separate organization ownership from the public-facing verified-entity status. Require multi-factor authentication for privileged roles, and require at least two administrators for sensitive/high-profile organizations where feasible.

## 3. Affiliation and address-change workflow

Example: Daniel already has `danielthrown@dispatch.com` and requests affiliation with a verified House organization.

1. Daniel opens the organization's Dispatch profile and selects **Request affiliation**.
2. He chooses the requested relationship/role (for example staff, member, representative, contractor, press, or other approved category) and submits only the minimum required information.
3. Dispatch creates a pending affiliation request. Daniel remains a personal Dispatch user and keeps his original address while it is pending.
4. Authorized organization approvers see the request in an administrative queue. They can approve, reject, request more information, or let it expire.
5. The organization verifies the real-world relationship using its own approved process. Dispatch should not decide that a person is a representative merely because the user claims the title.
6. If approved, Dispatch records the membership, approver, timestamp, evidence/check method, scope, expiry/review date, and audit event.
7. The user may then request an organization address, such as `repthrown@house.dp`. The organization can approve the handle and role-based address according to its naming policy.
8. The address is added to the same underlying Dispatch identity; it does not create a second person or silently erase the personal address. The user can choose a primary sending identity where organization policy permits.
9. The organization may revoke the affiliation or address. Revocation removes organization access and the organization address after a clear notice/grace process, but does not delete the person's personal Dispatch account or personal mailbox.
10. For sensitive roles, require periodic re-verification and immediately suspend privileged access when an organization reports a role has ended.

Important: affiliation is not proof of public office by itself. Public profile labels must distinguish **organization verified**, **membership confirmed by organization**, and any separately verified official role. Never display an elected/appointed title as verified unless the organization has attested to that exact claim.

## 4. Organization and account verification

Verification is a trust and safety workflow, not a decorative checkmark.

### Organization verification states
- Unsubmitted
- Pending review
- More information required
- Verified
- Rejected
- Suspended
- Expired / re-verification required

### Verification methods
Use a risk-based combination:
- Verify control of an official domain through DNS TXT or a file challenge when the organization has a domain.
- Confirm using an independently sourced official contact channel, not only contact details supplied by the applicant.
- Request official documentation or authorization letters when appropriate, with secure access and retention limits.
- For public bodies and elected offices, verify against authoritative official directories and require human review.
- Confirm the applicant's authority to administer the organization, not merely their employment or membership.
- Record reviewer, method, date, evidence reference, confidence, and next review date.

Never imply that a Dispatch-internal namespace proves the entity owns a similarly named external website. Names that could impersonate public institutions must be reserved or escalated. Provide appeal and correction processes. Verification evidence must be access-controlled, encrypted, and retained only as long as necessary.

### Member verification
- An organization may require invitation, official work address/domain proof, approval by a named approver, or manual evidence review.
- A verified organization must be able to approve/reject individual affiliation requests.
- A verified member badge should be scoped and worded precisely (for example, "Affiliated with House organization on Dispatch"), not a blanket claim that all statements by the account are official.
- Organization administrators can revoke memberships; users can appeal mistaken revocations.

## 5. Messaging and mailboxes

### Core mail capabilities
- Inbox, unread, starred, sent, drafts, archive, spam, trash.
- Compose, reply, reply-all, forward, search, labels/folders, pinning, message threads.
- Read receipts only when explicitly enabled and disclosed.
- Message delivery and read states with clear semantics.
- Attachments with size/type limits, malware scanning, private object storage, and signed access.
- Block, report, mute, and anti-spam controls.
- Message requests or restricted delivery for unknown senders, configurable by each user/organization.
- Shared organization mailboxes (for example `press@house.dp`) with delegated access and audit trails.
- Organization-wide retention and legal hold controls only where lawful, disclosed, and properly permissioned.

Messages are addressed to Dispatch identities and organization namespaces only. The backend must reject unknown or inactive Dispatch recipients with a safe response and must not expose private account existence to unauthenticated users.

### Sending identity
A user with both `danielthrown@dispatch.com` and `repthrown@house.dp` can select the permitted From identity when composing. Organization policy may restrict sending from an official role address. Clearly label messages sent as a shared mailbox or on behalf of an organization. Preserve sender identity and audit events.

### Security and privacy
- Enforce mailbox ownership and participant access on the server, not just in the UI.
- Use row-level authorization and tests for every read/write path.
- Protect against IDOR, spoofed sender fields, forged organization membership, and unauthorized shared mailbox access.
- Rate-limit account creation, messages, recipient fan-out, and attachment uploads.
- Add spam/abuse detection, reporting queues, account recovery safeguards, and admin audit logs.
- Encrypt traffic and sensitive data at rest; never expose service-role credentials in the browser.
- Establish retention, deletion, backup, and incident-response policies before production.

## 6. People — Contacts and address book

People is the first-class contacts and directory area.

### Personal address book
- Create, edit, merge, and delete personal contacts.
- Save Dispatch address, display name, photo, organization, role/title (user-entered and clearly unverified unless attested), phone/notes (private), and custom fields.
- Add favorites and groups/lists.
- Search contacts and start a message or event invitation.
- Import/export with explicit consent and safe validation (CSV/vCard as appropriate).
- Control whether the user's profile is discoverable and which profile fields are visible.

### Organization directory
- Search an organization's public member directory if that organization enables it.
- Show official organization addresses, approved display names, and only those roles the organization has attested to.
- Directory visibility controls: public within Dispatch, organization members only, hidden/unlisted.
- Never expose private contact notes, private email aliases, or personal profile fields through organization search.
- Provide report/correction controls for impersonation or inaccurate directory entries.

## 7. Calendar and event scheduling

Calendar is integrated with People and messaging.

### Personal calendar
- Day, week, month, agenda views.
- Create, edit, cancel, duplicate, and search events.
- Title, description, location or video/meeting link, start/end, time zone, recurrence, reminders, visibility, and invitees.
- Availability/free-busy controls with granular sharing.
- Accept, tentative, decline, propose a new time, and track RSVP status.
- Invitations and updates delivered as Dispatch messages and in-app notifications.
- Prevent accidental double-booking where availability is shared; never reveal private event details through free/busy.

### Organization calendars
- Shared calendars for teams, departments, offices, campaigns, and public events.
- Permission levels: view free/busy, view details, create events, edit own events, manage calendar.
- Event owners, co-hosts, attendee lists, RSVP exports, and event activity history.
- Internal meetings and public events with distinct visibility.
- Optional registration/guest workflows for events, with capacity limits, waitlists, cancellation, and attendee notifications.
- Events can be associated with an organization, team, project, or shared mailbox.

### Scheduling assistant
- Suggest meeting times based on invitee availability that the invitees have chosen to share.
- Let invitees propose alternatives.
- Respect time zones and daylight-saving changes.
- Support recurring meetings, conflict warnings, buffer time, working hours, and configurable reminders.
- Do not expose the names or details of private events to other attendees.

## 8. Notifications

- In-app notifications for new messages, invitations, RSVP changes, affiliation requests, approvals, and security events.
- Per-category controls, quiet hours, digest options, and device/session management.
- Push notifications only after platform permission and with privacy-safe previews.
- Security alerts (new sign-in, password change, new admin, namespace change, membership revocation) should be distinguishable from ordinary activity.

## 9. Administration and governance

### Personal settings
Profile, addresses/aliases, security, sessions/devices, privacy, blocked users, notifications, language, time zone, accessibility, export, and account deletion.

### Organization console
Organization profile, verification, namespace and handle policies, admins/roles, affiliation queue, member directory, shared mailboxes, calendars, retention settings, security, audit log, and organization closure/transfer.

### Platform operations
Dispatch staff tools must be permissioned, logged, and limited. Support access should be time-bound and auditable. Sensitive government, political, and public-interest organizations require heightened anti-impersonation review, abuse escalation, and transparent suspension/appeal procedures. Do not sell or expose private communications to organization administrators; define clear legal and privacy boundaries.

## 10. Suggested data model

Use stable UUID primary keys and explicit tenant boundaries.

- `profiles`: user_id, display_name, avatar, bio, locale, timezone, discoverability, created_at.
- `addresses`: id, owner_user_id, local_part, namespace_id, address_type, status, is_primary, created_at, revoked_at. Unique constraint on normalized namespace + local_part.
- `namespaces`: id, slug, domain_label, owner_organization_id, status, verification_state, created_at.
- `organizations`: id, legal_name, display_name, category, description, website, country/region, verification_state, public_directory_setting.
- `organization_roles`: id, organization_id, role_key, permission_set.
- `organization_memberships`: id, organization_id, user_id, role_id, status, approved_by, approved_at, expires_at, verification_level.
- `affiliation_requests`: id, organization_id, user_id, requested_role, message, status, reviewer_id, decision_reason, created_at, decided_at.
- `verification_cases`: id, subject_type, subject_id, status, methods, evidence_refs, reviewer_id, reviewed_at, next_review_at.
- `mailboxes`: id, owner_type, owner_id, mailbox_kind, settings.
- `messages`: id, mailbox_id, thread_id, sender_address_id, subject, body_format, body, sent_at, received_at, status.
- `message_recipients`: message_id, recipient_address_id, recipient_type, delivery_state, read_at.
- `attachments`: id, message_id, storage_key, filename, media_type, size, scan_state.
- `threads`, `folders`, `labels`, `message_labels`, `drafts`, `blocked_senders`, `reports`.
- `contacts`, `contact_groups`, `contact_group_members`.
- `calendars`, `calendar_permissions`, `events`, `event_attendees`, `event_recurrence`, `event_reminders`.
- `notifications`, `devices`, `audit_events`.

Use database constraints for unique addresses, membership uniqueness, valid state transitions, and referential integrity. Keep verification evidence in private storage with short-lived signed access; do not store raw sensitive documents in public tables.

## 11. Authorization and tenant isolation

- Every organization-owned record must carry an organization/tenant boundary.
- Personal mailbox data is accessible only to its owner and explicitly authorized delegates.
- Organization mailbox access is controlled by role + mailbox delegation; organization membership alone must not grant access to all mail.
- Calendar visibility and free/busy are separately permissioned.
- Affiliation requesters may read their own request status, not internal reviewer notes.
- Only authorized approvers can decide affiliation requests.
- Only authorized verification reviewers can change verification state.
- Users cannot set their own verified flags, organization role, membership status, or sender address through client-supplied fields.
- Test cross-tenant reads/writes, revoked membership, stale sessions, and role changes.

## 12. Recommended technical architecture

A practical first implementation can use:
- Web app: Next.js/React or a comparable framework.
- Authentication: Supabase Auth with verified sign-in/recovery and MFA for administrators.
- Database: Supabase Postgres with RLS on all exposed tables.
- Realtime: Supabase Realtime for inbox, notifications, and calendar updates, with server authorization.
- File storage: private object storage with scanning pipeline and signed URLs.
- Server-side operations: trusted API routes/Edge Functions for sending, membership decisions, namespace allocation, verification transitions, and notifications.
- Background jobs: reliable queue/worker for fan-out, notifications, attachment scanning, reminders, recurring events, and cleanup.
- Observability: structured logs, metrics, tracing, abuse monitoring, audit events, backups, and incident response.

Use a transactional outbox or equivalent for reliable message delivery and notification fan-out. Create a message and recipient delivery records atomically; process delivery asynchronously and idempotently. Realtime is a notification mechanism, not the source of truth.

## 13. Delivery roadmap

### Phase 0 — Product and trust foundations
Confirm naming rules, address policy, organization categories, verification policy, privacy model, moderation rules, and whether `.dp` is internal-only. Reserve high-risk names and define appeals.

### Phase 1 — Real personal accounts
Registration, sign-in/out, verification/recovery, profile, unique `@dispatch.com` address allocation, settings, sessions, and protected routes.

### Phase 2 — Dispatch messaging
Database-backed inbox, threads, compose/send, recipient validation, drafts, folders, attachments, realtime updates, spam/report/block, and multi-device persistence. Dispatch-to-Dispatch only.

### Phase 3 — People
Personal contacts, search, groups, favorites, profile discoverability, organization directory foundations.

### Phase 4 — Calendar and scheduling
Personal calendar, invitations/RSVPs, reminders, availability sharing, time zones, recurring events, and scheduling assistant.

### Phase 5 — Organizations and namespaces
Organization creation, namespace reservation, organization console, role-based admins, shared mailboxes, and organization calendars. Start with low-risk organization categories and controlled pilot.

### Phase 6 — Affiliation and verification
Affiliation request queue, evidence collection, admin approvals, official verification, membership/role attestation, organization addresses such as `repthrown@house.dp`, revocation, expiry, appeals, and audit.

### Phase 7 — Scale and institutional readiness
Security review, penetration testing, abuse operations, data export/deletion, accessibility, disaster recovery, operational runbooks, compliance review, and carefully controlled public launch.

## 14. Acceptance criteria

- A new user can register without an external email account and create a Dispatch identity.
- A user can send a message to another active Dispatch address and the recipient receives it on another device/session.
- A user cannot send as another user or read another user's private mailbox.
- Contacts can be created, searched, grouped, and used as recipients.
- Users can create events, invite Dispatch users, and process RSVP changes.
- An organization can apply for a namespace and is not marked verified until required review succeeds.
- An organization administrator can review affiliation requests with an auditable decision.
- A user's personal identity remains intact when an organization address is added or revoked.
- Organization membership does not automatically grant access to private mailboxes.
- A member cannot self-assign an official role or verified badge.
- Organization calendars and shared mailboxes enforce explicit permissions.
- The app clearly communicates that `.dp` addresses are internal Dispatch identities unless external domain infrastructure is deliberately built.
- All core records persist securely on the server and synchronize across devices; browser localStorage is not the production source of truth.

## 15. Explicit non-goals for the initial network

- No Gmail, Outlook, Yahoo, IMAP, SMTP, or external email account linking.
- No sending to arbitrary external email addresses.
- No assumption that an organization is authentic based only on a requested name or logo.
- No self-awarded government, campaign, party, or elected-official verification.
- No automatic conversion that deletes or replaces a user's personal identity.
