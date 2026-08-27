# Data Retention

**GDPR Article 5(1)(e) says personal data may be kept only as long as it is needed.** This page describes what vScrawl keeps, what it deletes, and what you must currently do yourself.

## The short version

!!! warning ""
    **The platform does not delete anything automatically.** There is no retention setting, no scheduled cleanup, and no age limit on any table.

    Every IP address, browser user agent, approximate location, name and email ever written to the activity and audit logs is still stored, and will remain so until someone removes it.

This is the largest outstanding compliance gap in the product, and it is stated plainly here so you can plan around it rather than discover it during an audit.

## What is deleted, and when

Deletion happens only when a person or an administrator triggers it.

| Trigger | What is removed |
| --- | --- |
| **A user deletes their account** (Settings → Delete Account) | Their Keycloak login, draft documents, incomplete self-signed documents, their recipient entries, form fields, inbox items, invitations, signature certificates and signing keys. Documents already sent are set to **Void**. |
| **An administrator deletes a user** (Customers → Users) | The same path. |
| **The last member of an organization leaves** | The organization itself, its templates, its documents and its credit history. |
| **A user deletes a signature or stamp** | That image only. |

### What survives a deletion

| Survives | Why |
| --- | --- |
| The user's name and email on their platform record | **Not intended** — a known gap, see below |
| Their name and email in Activity Logs and Audit Logs | **Not intended** — a known gap |
| Identity verification data in Qualified Certificate Requests | **Not intended** — deletion does not reach this table |
| Their name on documents they have already signed | **Correct and deliberate.** Signed documents are evidence; other parties rely on them. |
| The consent record for those signatures | **Correct.** Evidence of a signature is worthless without it. |

## What you must do in the meantime

Until retention settings exist, these are manual tasks. Agree them with your database administrator and put them on a schedule.

### Periodically

| Task | Suggested frequency |
| --- | --- |
| Remove activity log rows older than your retention period | Quarterly |
| Remove audit log rows older than your retention period | Quarterly |
| Remove read notifications older than a few months | Quarterly |
| Remove expired session and refresh tokens | Monthly |
| Remove expired invitation and password-reset codes | Monthly |
| Review rejected qualified certificate requests and delete the attached identity documents | Monthly |
| Identify accounts with no sign-in for your inactivity threshold, and close them | Annually |

### On every erasure request

Complete by hand what account deletion misses:

1. Replace the name and email on the user's record with anonymized values.
2. Remove or hash their name and email in the activity and audit log rows.
3. Mask the final part of the IP addresses in those rows.
4. Clear the derived location fields.
5. Delete any qualified certificate request that did not result in an issued certificate.

!!! note ""
    Anonymize rather than delete where a record is referenced elsewhere. Removing a log row outright can break the audit trail's continuity, which is itself a compliance problem. Replacing the identifying fields keeps the trail intact while removing the person from it.

### Identity documents

!!! warning ""
    Qualified certificate requests store a **scan of the applicant's identity document** in the database. This is the most damaging single field in the system if it is ever exposed.

    Once a request has been reviewed and the certificate issued or rejected, the scan itself is no longer needed — only the verification outcome. Delete the stored scans as soon as review completes, and do not wait for a general retention policy to arrive.

## Deciding your retention periods

These are business and legal decisions, not technical ones. They depend on your jurisdiction and your contracts, so take advice rather than adopting a default. As a starting point for that conversation:

| Data | Typical range | Consideration |
| --- | --- | --- |
| Completed signed documents | Long — often 10 years | This is the value your customers paid for. Deleting too early destroys it. |
| Draft documents never sent | Short — months | Abandoned work |
| Documents sent but never completed | Around a year, then void | The sender can always resend |
| Activity logs | Around a year | Long enough to investigate an incident |
| Audit logs of administrator actions | Longer than activity logs | Privileged actions deserve more scrutiny |
| Derived location data | Much shorter than the log row it sits on | The most intrusive field, with the weakest justification |
| Inactive accounts | Around two years, with a warning first | Never close an account silently |
| Billing records | Set by tax law, not by you | Usually overrides a deletion request |

!!! note ""
    Whatever you choose, **write it down and publish it in your Privacy Policy**. Articles 13 and 14 require you to tell people how long you keep their data. "Indefinitely" is not an acceptable answer, and neither is silence.

## When retention settings arrive

The planned implementation adds configurable periods per data type, plus a daily job that deletes or anonymizes anything past its window and writes a summary to the audit log.

Two things to expect when it ships:

- It will be **off by default**. A retention job that switches itself on during an upgrade and starts deleting customer documents is far worse than late compliance. You will have to enable it deliberately.
- You will need to **set your periods before enabling it**, and confirm them against what you have published in your Privacy Policy.

## Backups

Retention applies to backups too, and this is routinely forgotten.

- Know your backup schedule, how long each backup is kept, and where it is stored.
- Backups need the same protection as production: encryption, access control, and a defined destruction point.
- When you erase someone's data, it will persist in backups until those backups expire. The accepted approach is to say so, guarantee that the data is not restored into production, and re-apply the erasure if a restore ever happens.

## Related

- [GDPR Overview](gdpr_overview.md)
- [Data Subject Requests](data_subject_requests.md)
- [Activity Logs](../other_admin_operations/activity_logs.md) · [Audit Logs](../other_admin_operations/audit_logs.md)
