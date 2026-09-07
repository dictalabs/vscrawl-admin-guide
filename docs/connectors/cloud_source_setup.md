# Set Up Cloud Source Providers

A **Cloud Source** connector lets a signer bring one of their own files in from Google Drive or Dropbox. Before one can be added, an application has to exist on the provider's side for the picker to load under. This page covers the parts that are specific to vScrawl — which options matter and which exact values to enter. Creating the account or project itself is the provider's own process, and is linked where it starts.

Each connector carries its own application, so two connectors can sit in two entirely different Google projects or Dropbox apps. That is what lets one installation offer a different application per environment, or per group of organizations.

---

## What a Cloud Source connector is, and is not

This is the **signer bringing a file in**, not the platform writing documents out. The distinction decides every field on the form.

| | Cloud Source | Storage |
| --- | --- | --- |
| Direction | The signer's own file is copied **into** vScrawl, once | Documents vScrawl holds are written **out** to a provider |
| Who consents | The signer, in the provider's own window, for that one file | An administrator, once, for the whole installation |
| Credentials stored | A client id and an API key, or an app key — **public values** | A client secret and a long-lived refresh token |
| Available providers | Google Drive, Dropbox | Server Storage only |

Storage no longer offers Google Drive or Dropbox. Documents stay on the platform's own volume; see [Storage](../other_admin_operations/storage_settings.md).

!!! warning "Never enter a client secret or an app secret here"
    A Cloud Source connector's values are read by the signing app in the browser, which means they ship in the page source and are visible to anyone who opens the page. That is by design — Google and Dropbox both intend these particular values to be public, and neither picker can work without them. A client secret is not such a value. Import is never issued one and never needs one, which is why the form has no field for it.

---

## Where the values are registered

Neither provider redirects anywhere during import — everything happens in the browser, in the provider's own window. So there is **no redirect URI to register**. What each provider checks instead is *which page asked*, and each wants that written differently:

| Provider | What to register | Shape |
| --- | --- | --- |
| Google Drive | Authorised JavaScript origin, on the OAuth client | Full origin, with scheme and port: `https://sign.example.com` |
| Google Drive | HTTP referrer, on the API key | The same origin **plus `/*`**: `https://sign.example.com/*` |
| Dropbox | Chooser / Saver / Embedder domain | Bare domain, **no scheme and no port**: `sign.example.com` |

The exact strings for your installation are printed inside the connector form itself, under **How to set up the provider account**, with a copy button on each. They are built from the signing app's address as configured in **Configurations**, so they are already correct for the environment you are working in — use those rather than typing them from this page.

!!! note "Every environment needs its own entry"
    Staging and production are different origins, so each one has to be added to the provider's application separately. An origin that has not been registered fails at the moment the signer clicks the provider, with an error naming an address they were never told to add.

---

## Google Drive

### 1. Create the project and enable the APIs

In the [Google Cloud console](https://console.cloud.google.com/apis/credentials), select or create a project, then enable **both**:

- **Google Drive API**
- **Google Picker API**

The Picker API is the one that is easy to miss. Without it sign-in succeeds and the file window then fails to load.

### 2. Configure the consent screen and scope

Add one scope only:

```
https://www.googleapis.com/auth/drive.file
```

It grants access to **only the files a signer actually picks** — nothing else in their Drive is visible to vScrawl, before or after. It is also what keeps the project out of Google's **restricted-scope tier**, which would otherwise require a paid third-party security assessment, repeated every year for as long as the application is published.

### 3. Create the OAuth client

Under **Credentials**, create an **OAuth client ID** of type **Web application**.

Add the origin shown in the connector form under **Authorised JavaScript origins** — **not** under *Authorised redirect URIs*. A value placed in the redirect list has no effect on import, because import never redirects.

### 4. Create the API key

Still under **Credentials**, create an **API key** and:

- Under **API restrictions**, restrict it to the **Picker API**.
- Under **Application restrictions**, choose **Websites** and add the referrer shown in the connector form — the one ending in `/*`.

The trailing `/*` is required, and is the whole difference between this value and the origin above. A website restriction is matched against the entire address including its path, so an entry without the wildcard rejects every call the Picker makes and import fails with *"The API developer key is invalid"*.

### 5. Add the connector

In vScrawl, add a connector with Purpose **Cloud Source** and Provider **Google Drive**, then fill in:

- **Client ID** — from the OAuth client.
- **API Key** — from the API key.

Both are required. Set **Status** to **Active** and save. There is no consent step and no Test Connection here: nothing is connected until a signer opens the picker themselves.

---

## Dropbox

### 1. Create the app

In the [Dropbox App Console](https://www.dropbox.com/developers/apps), **Create app** and choose **Scoped access**.

The access type does not matter for import. The Chooser hands back a link to the single file a signer selects, and the application is never granted the account itself — so the choice between *App folder* and *Full Dropbox* has no effect on what vScrawl can reach.

No permissions need to be enabled either. Import does not call the Dropbox API on the account's behalf.

### 2. Register the domain

On the app's **Settings** tab, add the domain shown in the connector form under **Chooser / Saver / Embedder domains**.

Enter it exactly as shown: **no scheme, no port**. Dropbox supplies those itself and rejects a value that carries them — which is what produces the *"This app is misconfigured"* page instead of the file list.

### 3. Add the connector

Add a connector with Purpose **Cloud Source** and Provider **Dropbox**, and fill in the **App key** from the app's Settings tab.

The **app secret is not used and must not be entered.** The app key is public by design and ships in the page source; the secret is not, and would be exposed there too.

---

## Offering it to organizations

Adding the connector does not by itself put anything on a signer's screen. A Cloud Source is offered only where a **service plan** grants it, and every plan names **which connector** it uses.

Switch the provider on for a plan under **Finance → Service Plan**, then choose the connector. See [Service Plan](../finance_settings/service_plans.md).

Both halves must be in place. A plan with the provider switched on but no connector chosen cannot be saved; a connector that is deactivated or repurposed later stops being offered, without the plan itself changing.

---

## If something fails

| What you see | What it usually means |
| --- | --- |
| `Not a valid origin for the client`, or sign-in refuses immediately | The signing app's origin is not registered under **Authorised JavaScript origins** on the OAuth client — or it was added under *Authorised redirect URIs*, which import never uses. Check it is this environment's origin, exactly, including the port. |
| `The API developer key is invalid` | Three different causes, in the order worth checking. (1) The key stored on the connector is **not the key that exists in the console any more** — it was rotated, deleted and recreated, or copied from another project. (2) The key's website restriction is missing the trailing `/*`. (3) The key is not permitted to call the Picker API. See [Telling those three apart](#telling-those-three-apart) below. |
| Google sign-in works, then the file window never appears | The **Google Picker API** is not enabled on the project. Enabling the Drive API alone is not enough. |
| Dropbox shows *"This app is misconfigured"* | The domain under Chooser / Saver / Embedder domains does not match, or was entered with `https://` or a port. Enter the bare hostname. |
| The provider is not offered on the upload screen at all | Something upstream of the provider: the organization's service plan does not have it switched on, no connector is chosen on the plan, the chosen connector is Inactive, or one of its required fields is empty. Google Drive needs **both** the Client ID and the API Key — with either one missing it is not offered at all, rather than being offered and then failing. |
| The **API Key** field is empty on a connector that was working before | Expected once, after upgrading to the release that corrected how this key is stored. The old value was unreadable and has been cleared rather than left in place looking like a setting. Paste the key in again and save; it does not recur. |

### Telling those three apart

The restrictions are visible in the Google console, but the **key the platform actually sends** is not — and a key that was replaced in the console looks perfectly valid there while the connector still holds the old one.

To see what is being sent: sign in to the signing app with the browser's developer tools open, find the `profile` response on the Network tab, and read `googleDriveApiKey`. Then compare it with **Show key** on the API key in the Google console.

- **The two differ** → the connector holds a stale key. Paste the console's key into the connector and save. This is the common case after anyone regenerates or recreates a key, because nothing in Google tells this platform that happened.
- **The two match** → the key is right and the restrictions are wrong. Check the website entry ends in `/*`, and that **Google Picker API** is among the key's permitted APIs. Restriction changes take **up to five minutes** to take effect, so retry after a wait and a hard reload rather than immediately.
- **The value starts with `v2.`** → it is stored encrypted and cannot work. Re-enter it; see the last row of the table above.

More than one API key in the project is worth ruling out first. Editing the restrictions of a key the connector does not use produces exactly the same error, and no amount of correcting it helps.

---

## Replacing a key, or replacing a connector

**To change a key**, edit the connector and save. Signers pick the new value up at their next sign-in, because the credentials travel on the profile.

**To move to a different connector** — a new Google project, say — the order matters, because a service plan records the connector's **id**:

1. Add the new connector and make it **Active**.
2. Open every service plan that offers the provider and select the new connector there. Nothing switches by itself; a new connector is ignored until a plan names it.
3. Only then deactivate or delete the old one.

!!! warning "Deleting first breaks it silently"
    Nothing stops a Cloud Source connector from being deleted while a service plan still points at it. The delete succeeds, the plan is left holding an id that resolves to nothing, and the provider stops being offered — with no error anywhere, because a missing connector is indistinguishable from one that was never configured.

    Prefer setting the old connector **Inactive** over deleting it. An inactive connector is neither offered to signers nor listed in the plan's dropdown, and the decision stays reversible.

---

!!! note "Nothing here is per signer"
    These values belong to the installation, not to any account. vScrawl holds no token, no password and no session for a signer's Drive or Dropbox — the consent lives in the provider's own window and lasts for that one file. There is nothing to revoke on this side, and nothing to disconnect.
