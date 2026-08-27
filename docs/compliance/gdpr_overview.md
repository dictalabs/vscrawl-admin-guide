# GDPR Overview

**vScrawl processes personal data on your behalf.** This section explains what the platform does for you automatically, what it expects you to configure, and what remains your responsibility as the operator.

GDPR splits responsibility between two roles, and which one you hold changes what you must do:

- The **controller** decides why and how personal data is processed.
- The **processor** acts on the controller's instructions.

| Your deployment | Who is the controller |
| --- | --- |
| **On-premise** – you run the services, Keycloak, database and storage | **You are.** You decide retention, you sign your own agreements with email and storage providers, and you answer requests from your users. |
| **Hosted** – account and billing data | The platform operator. |
| **Hosted** – document content your customers upload | **Your customer is.** The platform is their processor. |

!!! note ""
    On an on-premise installation, no one else can answer a data protection request on your behalf. The platform gives you the tools; the obligations are yours.

## What the platform does automatically

These need no configuration — they are always on.

- **Records electronic signature consent.** Every time a recipient consents to sign electronically, the platform stores the exact wording shown to them, a cryptographic digest of it, the time, their IP address and their browser user agent. See [Consent Records](consent_records.md).
- **Logs every action.** End-user actions go to [Activity Logs](../other_admin_operations/activity_logs.md), administrator actions to [Audit Logs](../other_admin_operations/audit_logs.md). Both record the actor, the action, the IP address, the browser user agent and a trace ID.
- **Asks before collecting anything optional.** The web application shows a storage consent banner, and the mobile app asks before enabling crash reporting or analytics. Both default to off.
- **Keeps credentials out of the application database.** Passwords live only in Keycloak, and only as hashes.
- **Never exposes session tokens to the browser.** The gateway holds them server-side and issues the browser only an opaque session identifier.

## What you must configure

These ship blank or disabled. The platform cannot fill them in for you.

| Setting | Where | Why it matters |
| --- | --- | --- |
| **Privacy Policy** | Configurations → [Privacy Policy](../other_admin_operations/privacy_policy.md) | **Ships empty.** Until you paste your policy, users see "Privacy policy content is not available right now." Articles 13 and 14 require you to tell people what you collect and why. |
| **Terms of Service** | Same screen | Ships empty. |
| **Encryption at rest** | Configurations → [Security](../other_admin_operations/security_settings.md) | **Defaults to off.** Turn it on. The database holds identity documents and signature images. |
| **Email connector** | [Connectors](../connectors/add_connectors.md) | Whichever provider you choose receives your recipients' names and email addresses. You need your own agreement with them. |
| **Storage connector** | [Storage](../other_admin_operations/storage_settings.md) | If you point storage at Google Drive or Dropbox, **your documents go there.** You need your own agreement with that provider. |

!!! warning ""
    Enabling a remote storage connector sends document content to a third party. Do not enable one until you have a data processing agreement with that provider in place.

## What remains your responsibility

The platform cannot do these for you.

- **Deleting old data.** There is no automatic deletion. See [Data Retention](data_retention.md) — this is the most commonly missed item.
- **Answering data subject requests** within one month. See [Data Subject Requests](data_subject_requests.md).
- **Signing agreements** with every third-party provider you configure.
- **Reporting a breach** to your supervisory authority within 72 hours of becoming aware of it.
- **Keeping written records** of what you process and why — the Article 30 record.

## Where the formal records live

The source repository contains a `compliance/` folder holding the written records an auditor will ask for: the data inventory, the legal basis assessments, the sub-processor list, the retention schedule, the data subject rights procedure, the security measures, the breach response plan, and templates for a customer data processing agreement and a default privacy policy.

Ask your implementation contact for a copy if you do not have repository access. Those documents are the detailed version of this page.

## The qualified certificate feature

If you enable qualified electronic signatures (`ENABLE_RSS_ONBOARD`), the platform begins collecting identity verification data: date and place of birth, gender, nationality, national identifier or passport number, mother's maiden name, and a scan of an identity document.

!!! warning ""
    This is the highest-risk data the platform holds. Before enabling it at scale you will normally need a **Data Protection Impact Assessment** under Article 35, and the transfer of that data to the certificate provider needs its own legal basis. Take advice before switching it on.

See [Qualified Certificate Requests](../other_admin_operations/qualified_cert_requests.md) for how the review workflow itself operates.

## Where to go next

- [Consent Records](consent_records.md) — the three kinds of consent the platform captures, and how to produce the evidence
- [Data Subject Requests](data_subject_requests.md) — handling access, correction and deletion requests
- [Data Retention](data_retention.md) — what the platform keeps, and for how long
