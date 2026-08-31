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

The decision is kept in the visitor's own browser, which is what the banner reads to know whether to appear again.

**For a signed-in user it is also recorded on the server**, as a `COOKIE_CONSENT_RECORDED` entry in [Activity Logs](../other_admin_operations/activity_logs.md) naming the categories allowed, the method and the time. That is the copy you can produce: a record only the visitor can clear demonstrates nothing under Article 7(1).

For a visitor who never signs in there is no server-side record, and nothing identifies them to hold one against. If you are challenged about that case, what you produce is the banner itself and its disclosure text.

A user can review or withdraw their choice using the **Cookie preferences** button at the bottom of the Privacy Policy page.

!!! note ""
    If you change the categories or materially change the banner wording, the policy version must be increased so that everyone is asked again. Consent given under an older version does not carry over.

## 3. Telemetry consent — mobile

The mobile app can send crash reports and usage analytics to Google Firebase. **Both are disabled at build time and stay off until the user opts in.**

The app asks once, on first launch, offering **Allow all**, **Decline all** or a per-item choice. Both toggles start off. The record kept on the device includes which items were allowed, the policy version, and the timestamp.

Users change their answer at any time in **Settings → Privacy** in the app. Choosing **Reset privacy choices** clears the decision, stops collection, and deletes the diagnostic data held on the device — withdrawal erases rather than merely pausing.

Declining changes nothing about how the app works.

## 4. Terms acceptance at account creation

Every new account must accept the Terms of Service and Privacy Policy. This is enforced on **all four** routes into the platform:

- **Self-registration** — the sign-up page
- **Invitation** — the account setup page an invited organization member completes
- **Social sign-in (Google)** — a one-time "Before you continue" page shown at login
- **The mobile app** — the tick box on the app's own sign-up screen

The third case works differently because a social sign-up never passes through the sign-up form, so there is no checkbox to put on it. Instead, the platform checks at every login: if an account is linked to an identity provider and has no acceptance on record, it shows the terms page once and will not let the user past it until they accept.

!!! note ""
    Accounts created with an email and password are **not** affected by that check, even if they predate this feature. They were shown the checkbox at sign-up and did tick it — it simply was not being stored at the time. Only social accounts, which were never asked at all, are prompted.

The acceptance is checked on the server, not only in the browser, so it cannot be skipped by submitting the form directly. If the box is not ticked, the account is not created.

### What is recorded

Five attributes are stored against the user in Keycloak:

| Attribute | Contains |
| --- | --- |
| `termsAcceptedAt` | When they accepted, as a UTC timestamp |
| `termsAcceptedIp` | The address they accepted from |
| `termsAcceptedSource` | Which screen collected it — `WEB_SIGNUP`, `WEB_INVITE`, `WEB_PROMPT`, `MOBILE_APP` or `API` |
| `termsFingerprint` | A short mark identifying the exact Terms wording they accepted |
| `privacyPolicyFingerprint` | The same for the Privacy Policy |

#### Why a fingerprint and not a version number

The mark is derived from the policy text itself, so **it changes the moment you edit the policy** —
nobody has to remember to raise a number. That matters because this screen keeps no history: if the
record only said "version 1", and you rewrote the policy without changing that number, two people
who accepted materially different documents would both be recorded as having accepted "version 1".

Two consequences worth knowing:

- **Publishing a policy changes the mark.** Everyone who accepts from that moment on carries the new
  one; people who accepted the old text keep the old mark. That is exactly what you want if anyone
  ever asks what a particular person agreed to.
- **Keep a dated copy of each published version** outside this screen, as
  [Privacy Policy](../other_admin_operations/privacy_policy.md) already advises. The mark tells you
  *which* text was accepted; only your own copy can show *what it said*.

If a policy has never been published, the mark is empty and the attribute is simply not written.

### Retrieving it

!!! warning ""
    **The Keycloak admin console hides these by default.** Keycloak's user profile ships with its "unmanaged attributes" setting switched off, and while that is off the Users screen shows no Attributes tab and the admin API returns nothing — even though the values are stored correctly.

    This has caught people out: the attributes look missing when they are simply not being displayed.

To make them visible in the console, an administrator enables unmanaged attributes once:

1. Open the **Keycloak admin console** and select the **vScrawl** realm.
2. Go to **Realm settings → User profile → Attributes**.
3. Set **Unmanaged attributes** to **Enabled** (or **Only administrators can view** if you want them read-only).
4. Save.

After that: **Users → find the user → Attributes tab**, and the four values are listed.

This is a display setting only — it changes nothing about what is stored, and the acceptance record is written whether or not it is switched on.

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

The consent checkbox links to your Terms and Privacy pages. Those addresses come from two environment variables set on the Keycloak service:

| Variable | Points at |
| --- | --- |
| `VSCRAWL_TERMS_URL` | Your public Terms of Service page |
| `VSCRAWL_PRIVACY_URL` | Your public Privacy Policy page |

!!! warning ""
    **Set both on every installation that is not a local development machine.** If they are not set, the login theme falls back to a `localhost` address — which resolves to the *user's own computer*, so the policy never opens for them.

    That matters beyond broken links: consent has to be informed, and it is hard to argue someone was informed by a page that never loaded.

They are set alongside the other Keycloak settings in the deployment's compose file, for example:

```
VSCRAWL_TERMS_URL: "https://app.example.com/terms-of-services"
VSCRAWL_PRIVACY_URL: "https://app.example.com/privacy-policy"
```

The same two addresses are used by all three consent screens — sign-up, invited-member setup, and the social sign-in terms page — so setting them once covers every route.

To check they are right, open your sign-up page and click both links. They should open your real published pages.

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
