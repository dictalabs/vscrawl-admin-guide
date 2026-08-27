# Consent Records

**Consent is only valid if you can prove it was given.** Article 7(1) of the GDPR requires the controller to be able to demonstrate consent, and electronic signature law makes the same demand of signing consent. This page explains what the platform records, where each record lives, and how to retrieve it when someone asks.

The platform captures three distinct kinds of consent. They are stored separately and are produced in different ways.

## 1. Electronic signature consent

The strongest record the platform holds, and the one most likely to be needed in a dispute.

When an organization has **e-signature consent** enabled, every recipient must accept a disclosure before they can sign. At that moment the platform stores:

- The **exact wording** the recipient was shown — not a reference to it, the text itself
- A **SHA-256 digest** of that wording, so it can be proven unaltered
- The **timestamp** of consent
- The recipient's **IP address**
- The recipient's **browser user agent**
- **How** the consent was collected and through which channel

A second copy is written into the document's activity timeline. This matters: the recipient record is overwritten if someone consents again, but the timeline entry is not. **The timeline is the durable record.**

### Retrieving it

The full record is visible in the **Audit Report** for the document, on the end-user side of the platform:

1. Open the **Documents** list.
2. Click the **⋮** menu on the document.
3. Select **Audit Report**.
4. Find the **Consent** entry in the Activity log and click **View details**.

The Consent Record dialog shows the recipient's name and email, when they consented, how, where from — IP address, channel and device — and the full disclosure text they accepted.

The same evidence is included in the document's downloadable **evidence report**, which is cryptographically sealed. For a legal dispute, the evidence report is what you send.

!!! note ""
    Consent evidence is only recorded when the organization has e-signature consent turned on. If it was off at the time of signing, there is no record to produce and none can be reconstructed afterwards. Check this setting before you need it.

## 2. Cookie and storage consent — web

The web application shows a consent banner on first visit, offering **Accept all**, **Reject all** and **Customize**.

What is stored, in the visitor's own browser:

- Which categories they allowed — functional, analytics
- The **time** they decided
- **How** they decided — accept all, reject all, or a custom selection
- The **policy version** in force at the time

Two behaviors are deliberate and worth knowing if you are asked to justify the banner:

- Optional categories start **switched off**. Pre-ticked boxes are not valid consent.
- **Accept all** and **Reject all** are the same size and prominence. A prominent accept next to a buried reject is not a free choice.

Only strictly necessary storage runs before a decision: the sign-in session cookie and the language selection. **The web application contains no third-party trackers** — no analytics, advertising or session-recording scripts of any kind.

### Retrieving it

This record lives in the visitor's browser, not on your servers, so there is no admin screen for it. If you are challenged about the banner, what you produce is the banner itself and the disclosure text, not per-visitor records.

A user can review or withdraw their choice using the **Cookie preferences** button at the bottom of the Privacy Policy page.

!!! note ""
    If you change the categories or materially change the banner wording, the policy version must be increased so that everyone is asked again. Consent given under an older version does not carry over.

## 3. Telemetry consent — mobile

The mobile app can send crash reports and usage analytics to Google Firebase. **Both are disabled at build time and stay off until the user opts in.**

The app asks once, on first launch, offering **Allow all**, **Decline all** or a per-item choice. Both toggles start off. The record kept on the device includes which items were allowed, the policy version, and the timestamp.

Users change their answer at any time in **Settings → Privacy** in the app. Choosing **Reset privacy choices** clears the decision, stops collection, and deletes the diagnostic data held on the device — withdrawal erases rather than merely pausing.

Declining changes nothing about how the app works.

## 4. Terms acceptance at account creation

Every new account must accept the Terms of Service and Privacy Policy before it can be created. This is enforced on **both** routes into the platform:

- **Self-registration** — the sign-up page
- **Invitation** — the account setup page an invited organization member completes

The acceptance is checked on the server, not only in the browser, so it cannot be skipped by submitting the form directly. If the box is not ticked, the account is not created.

### What is recorded

Four attributes are stored against the user in Keycloak:

| Attribute | Contains |
| --- | --- |
| `termsAcceptedAt` | When they accepted, as a UTC timestamp |
| `termsAcceptedIp` | The address they accepted from |
| `termsVersion` | Which version of the Terms they accepted |
| `privacyPolicyVersion` | Which version of the Privacy Policy they accepted |

### Retrieving it

1. Open the **Keycloak admin console** and go to **Users**.
2. Find the user and open the **Attributes** tab.
3. Read the four attributes above.

### Keeping the version numbers meaningful

The version numbers come from the login theme, not from the browser — deliberately, so the recorded version cannot be altered by whoever is submitting the form.

!!! warning ""
    **When you publish a materially changed Terms of Service or Privacy Policy, increase the matching version number** in the login theme's `theme.properties`:

    ```
    app.terms.version=2
    app.privacy.version=2
    ```

    If you do not, everyone who signs up afterwards is recorded as having accepted the old version number while actually being shown the new text — which makes the whole record misleading rather than merely incomplete.

    Keep a dated copy of the version you are replacing. See [Privacy Policy](../other_admin_operations/privacy_policy.md#keeping-track-of-versions).

!!! note ""
    Accounts created **before** this was introduced have none of these attributes. Their absence means "created before acceptance was recorded", not "did not accept" — the checkbox was always present on the sign-up page, it simply was not stored. Say exactly that if you are ever asked.

### Check the policy links resolve

The consent checkbox links to your Terms and Privacy pages using addresses set in the login theme. **They ship pointing at a development address** (`localhost:4200`).

!!! warning ""
    On a production installation, update `app.terms.url` and `app.privacy.url` in `theme.properties` to your real URLs. Until you do, users are asked to accept documents they cannot open.

## What to check periodically

| Check | Why |
| --- | --- |
| Is e-signature consent enabled for organizations that need it? | No setting, no evidence — and it cannot be backfilled |
| Does the Privacy Policy screen have real content? | It ships empty |
| Do the registration page's terms and privacy links resolve? | They ship pointing at a development address |
| Has the banner wording changed without a version increase? | Old consent would be silently reused for new terms |

## Related

- [GDPR Overview](gdpr_overview.md)
- [Data Subject Requests](data_subject_requests.md) — including how a user withdraws consent
- [Privacy Policy](../other_admin_operations/privacy_policy.md) — editing the policy text
