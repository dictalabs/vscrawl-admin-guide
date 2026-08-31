# Data Subject Requests

**A data subject request is someone asking what you hold about them, or asking you to change or delete it.** Under GDPR you must respond within **one month**, free of charge. This page is the operational procedure for handling one using the admin console.

## The clock

One month from **receipt**, not from when you got around to it. You may extend by two further months for genuinely complex requests, but you must tell the requester **within the first month** — the extension notice is itself deadline-bound.

Record every request: what was asked, what you did, what you refused and why, and when you responded. That record is your evidence of compliance, and a supervisory authority will ask for it.

## Step 1 — Establish who is the controller

This determines whether you answer or forward.

| The requester is | Controller | Your action |
| --- | --- | --- |
| A registered user of your platform | You | Answer it |
| A recipient who was sent a document to sign, and has no account | **The organization that sent it** | Forward to that organization; tell the requester who they are |
| Asking about a document another organization sent them | **That organization** | Forward |

!!! warning ""
    Do not delete or alter a document because a recipient asked you to. The sending organization is the controller for that document — acting unilaterally on a recipient's instruction would breach your obligations to the sender.

    The exception is data you hold in your own right, such as log entries about that person's signing session. Answer that part directly.

## Step 2 — Verify identity

You must be reasonably sure the requester is who they claim to be — releasing someone's data to an impostor is itself a breach.

| Requester | How to verify |
| --- | --- |
| Signed in already | The authenticated session is enough |
| Emailing from the address on the account | Send a confirmation link to that address |
| Emailing from any other address | Do not proceed on that alone; contact the address on file |
| A lawyer or agent acting for someone | Written authority, plus verification of the person themselves |

!!! warning ""
    **Never ask for a passport or ID scan to verify a privacy request.** Collecting a high-risk identity document in order to honor a privacy right is a disproportionate collection and will not be viewed kindly.

## Step 3 — Handle the request

### Access — "what do you hold about me?"

The requester is entitled to a copy of their data **and** to supporting information: why you process it, who you share it with, how long you keep it, where it came from, and their remaining rights. A data dump alone does not satisfy the request.

**Point them at the self-service export first.** In their own account, the
requester can go to **Settings → Privacy → Download my data** and get a ZIP of
their account details, settings, documents, signing activity, signatures,
certificates, notifications and account history. That is faster than anything
you can assemble, and it is already redacted of other people's details. Every
download is recorded in their activity log.

The archive does **not** carry the supporting information — the purposes,
recipients, retention periods and their remaining rights. Send those with your
reply, or point to the published Privacy Policy if it states them.

Assemble by hand when the requester cannot sign in, when the request arrives by
email and you must answer it yourself, or when they ask for more than the
archive holds:

| Source | Where |
| --- | --- |
| Account details | Customers → **Users**, search by name or email |
| Their organization and role | Customers → **Organizations** |
| Everything they did on the platform | Audit → **[Activity Logs](../other_admin_operations/activity_logs.md)**, search by their name or email |
| Anything an administrator did to their account | Audit → **[Audit Logs](../other_admin_operations/audit_logs.md)** |
| Their documents and signing history | Their own **Documents** list and per-document **Audit Report** |
| Identity verification data, if any | Administration → **[Qualified Certificate Requests](../other_admin_operations/qualified_cert_requests.md)** |

!!! note ""
    Activity Logs and Audit Logs are viewable and searchable, but cannot be exported from the console. For a large history, ask your database administrator to extract the rows for that user rather than transcribing screens.

**Redact other people.** A document record names other recipients. You must not disclose their details to the requester — remove them unless they are unavoidably part of a document the requester is already a party to.

### Rectification — "this is wrong, fix it"

Most of it is self-service: the user edits their own profile under **Settings → Account Settings**.

If they cannot reach their account, an administrator can view the user under Customers → **Users** and use the **⋮** menu to recover their password or change their status. Note that some identity fields are held in Keycloak rather than the application, and may need to be corrected there.

### Erasure — "delete me"

Preferred route: **the user deletes their own account** from **Settings → Delete Account**. This runs the complete deletion path — it removes their Keycloak login, deletes draft documents, voids documents already sent, and deletes their signature images, certificates and signing keys.

An administrator can also delete a user from Customers → **Users**.

Two things to tell the requester before they proceed:

1. **An organization owner cannot delete their account while other members remain.** They must transfer ownership or remove the other members first.
2. **Signed documents are not erased.** Their name stays on documents they have already signed, together with the evidence that they signed them.

!!! note ""
    Refusing to erase signed documents is lawful and expected. Article 17(3) allows retention where it is needed for a legal obligation or for legal claims — a signature that could be erased would not be a signature, and other parties rely on it.

    Explain this to the requester rather than silently keeping the data. Partial erasure is a normal outcome, not a failure, but it has to be disclosed.

!!! note ""
    **Deletion anonymizes as well as deletes, on both paths.** Whether the user deletes their own account or an administrator deletes it, their platform record is replaced with a placeholder, their name and email are removed from the activity and audit logs, IP addresses are masked, and their Qualified Certificate Request is removed if it was never approved or anonymized if it was.

    No manual database work is needed for an ordinary erasure request. What is kept is kept deliberately — see the note above on signed documents.

!!! warning ""
    **This depends on a setting, and you should check it.** **Configurations → Privacy → Account deletion** carries a switch, *Erase personal data on deletion*.

    - **On** (the default) — the behaviour described above.
    - **Off** — deleting an account only stops the sign-in and marks it closed. The name, email address and every log entry stay in the database.

    With it off you can still fulfil an erasure request, but not from the product: it becomes manual database work. If your installation has it off, know that before you promise a requester anything.

#### If the same person signs up again

Worth knowing, because the two settings behave differently:

| Setting | Signing up again with the same email |
| --- | --- |
| **On** | The old email is gone from the record, so this is a **brand-new account**. Nothing from before carries over |
| **Off** | The old email is still on the closed record, and the platform **reuses that same record**. The person returns to their previous account with a new name and password — old activity history, completed documents and usage records still attached to it |

In both cases the Keycloak login is deleted, so a new password must be set. Draft documents, saved signatures and tokens are removed either way.

### Portability — "give me my data to take elsewhere"

Narrower than access: it covers only data the person gave you, held by consent or contract, in automated form. Their profile, their uploaded documents, the values they typed into fields, their signature images.

It does **not** cover activity logs, inferred location, or data other people provided about them.

The requester serves this themselves: **Settings → Privacy → Download my data** returns JSON, which is structured and machine-readable. The document files sit outside the archive, but they are downloadable one by one from the Documents screen, which meets the same obligation.

A download is a compliant answer; you are not obliged to transfer directly to another provider.

### Withdrawing consent

- **Cookies and site storage** — the user clears the site's stored data in their browser. That resets the choice and brings the banner back on their next visit. **Settings → Privacy** shows the answer currently on record.
- **Mobile telemetry** — **Settings → Privacy** in the app, or **Reset privacy choices**.
- **Electronic signature consent** — cannot be withdrawn after signing. The consent record is evidence of a completed act, and it is retained for as long as the document.

## Step 4 — Respond

Your response should state:

- What you found and what you are enclosing
- Why you process each category, and who you share it with
- How long you keep it
- **What you refused and under which exemption** — never leave this implicit
- Their right to complain to a supervisory authority

## Step 5 — Record it

Keep, for each request: a reference, the date received, who asked and how you verified them, which right they invoked, who the controller was, what you did, what you refused and why, and the date you responded.

Retain that log — it is evidence of compliance, and it also contains personal data, so it needs a retention period of its own.

## Known limitations, summarized

Tell requesters the truth about these rather than working around them quietly.

| Limitation | Effect |
| --- | --- |
| The archive omits the Art. 15 supporting information | Send the purposes, recipients and retention periods with your reply |
| Activity and Audit Logs cannot be exported from the console | A large history needs a database extract |
| Erasure can be switched off | **Configurations → Privacy → Account deletion**. Off means an erasure request has to be handled by hand |

Fixed since this guide was first written, and no longer limitations:

| Was a limitation | Now |
| --- | --- |
| No self-service data export | **Settings → Privacy → Download my data** returns a ZIP of readable JSON |
| Deletion leaves name, email and log entries | Deletion anonymizes the account row and both log tables, empties the signature and initials images, the two-factor seed and the security answer, and removes the saved-signature library — on the self-service and administrator paths alike |
| Identity verification data is not deleted with the account | Deleted with the account — unapproved requests removed, approved ones anonymized |

## Related

- [GDPR Overview](gdpr_overview.md)
- [Consent Records](consent_records.md)
- [Data Retention](data_retention.md)
