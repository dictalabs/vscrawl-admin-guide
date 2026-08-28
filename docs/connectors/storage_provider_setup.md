# Set Up Google Drive and Dropbox

Before a Google Drive or Dropbox storage connector can be added, an application has to exist on the provider's side for vScrawl to authorise against. This page covers the parts that are specific to vScrawl — which options matter and which exact values to enter. Creating the account or project itself is the provider's own process, and is linked where it starts.

Each connector carries its own application. There is no shared, installation-wide one, so two connectors can sit in two entirely different Google projects or Dropbox apps.

---

## Work out your Redirect URI first

Both providers need to be told, in advance, exactly where to send the browser back to after consent. Get this wrong and consent fails at the very last step, after the user has already approved.

```
https://<your-api-host>/admin/v1/storage/oauth/callback
```

Two things to check:

- It is the **API host**, not the admin console's. The console and the API are usually different hostnames — the callback belongs to the API.
- It must match **character for character** on both sides: what you register with the provider, and what you type into the connector's **OAuth Redirect URI** field. The provider checks it once when consent starts and again during the token exchange, so a trailing slash or `http` instead of `https` is enough to fail.

---

## Google Drive

### 1. Create an OAuth client

In the [Google Cloud console](https://console.cloud.google.com/), select or create a project, then:

1. Enable the **Google Drive API** for the project.
2. Configure the **OAuth consent screen**. Choose **Internal** if everyone using it belongs to your Google Workspace organisation; otherwise **External**.
3. Under **Credentials**, create an **OAuth client ID** of type **Web application**.
4. Add your Redirect URI under **Authorised redirect URIs**.

Copy the **Client ID** and **Client secret**.

### 2. Scope and verification

vScrawl asks for one scope only:

```
https://www.googleapis.com/auth/drive.file
```

This reaches **only the files the application itself created** — nothing else in the connected account is visible to vScrawl. It is deliberately narrow: the full Drive scope is a restricted scope and would require Google's security assessment before the application could be published.

A consequence worth knowing: because the application can only see what it made, the folder vScrawl stores in is **created during consent**. You cannot point a connector at a folder that already exists in the account.

### 3. Add the connector

In vScrawl, add a Storage connector with Provider **Google Drive**, fill in Client ID, Client Secret and the OAuth Redirect URI, then press **Connect Account** and sign in as the account that should hold the documents.

Consent returns a refresh token and the created folder's id, and both are filled in for you.

---

## Dropbox

### 1. Create an app

In the [Dropbox App Console](https://www.dropbox.com/developers/apps), **Create app**:

1. Choose **Scoped access**.
2. For access type, choose **App folder** — the app is then confined to its own folder under `/Apps` and cannot see the rest of the account. Choose **Full Dropbox** only if you have a reason to.
3. Name the app.

Copy the **App key** and **App secret** from the app's Settings tab, and add your Redirect URI under **OAuth 2 → Redirect URIs**.

### 2. Enable permissions

On the app's **Permissions** tab, enable at least:

| Permission | Why |
| --- | --- |
| `files.content.write` | Storing documents |
| `files.content.read` | Reading them back |
| `account_info.read` | Reporting space used on the Storage screen |

**Enable them before connecting the account.** vScrawl deliberately sends no `scope` parameter when it authorises, so Dropbox grants exactly what the app has enabled at that moment — which means nothing breaks when you change permissions later, but a token issued before a permission was enabled does not carry it.

If you enable `account_info.read` after connecting, press **Connect Account** again to re-authorise. Until you do, the Storage screen simply omits the usage figures rather than showing zero.

### 3. Add the connector

Add a Storage connector with Provider **Dropbox**, fill in App Key, App Secret and the OAuth Redirect URI, then press **Connect Account**.

After connecting, the **Root Path** field appears, defaulting to `/vscrawl`. With an App folder app, that path sits inside the app's own folder.

---

## After connecting

Press **Test Connection** on the connector. It writes a small file, reads it back and deletes it, and reports the outcome and how long it took. The same check decides whether the connector is offered as a **Default Storage** at all, so a connector that fails here will not appear in that dropdown.

---

## If something fails

| What you see | What it usually means |
| --- | --- |
| `redirect_uri_mismatch`, or consent fails on return | The Redirect URI registered with the provider is not identical to the one on the connector. Compare them character for character, including scheme and any trailing slash. |
| Consent succeeds but the connector still will not save | Consent did not return a refresh token. For Google this happens when the account has consented before; vScrawl sends `prompt=consent` to force a fresh one, so retry Connect Account. |
| Storage Usage shows no figures for Dropbox | The token was issued before `account_info.read` was enabled. Enable it, then Connect Account again. |
| Test Connection fails with an authorisation error | The App Key or Client ID was changed after connecting. The stored token belongs to the old credentials — reconnect the account. |
