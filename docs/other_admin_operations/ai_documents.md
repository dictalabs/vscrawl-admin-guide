# AI Documents

The **AI Documents** screen — the **AI Documents** tab of **Configurations** — decides whether users may draft a document by describing it in a chat, which AI connector writes it, and how much one person may use it per day.

When it is on, users see a **Create with AI** button on the upload screen. They describe the document they need ("a payslip for one month with earnings, deductions and net pay"), see it as a preview, ask for changes in follow-up messages, and press **Use this document**. The document becomes a PDF and is added to the upload exactly like a file they had browsed for, so recipients, fields, sending and signing work as they always do.

The feature is **off** on a new installation.

## Before you start

1. Add an **AI connector** under **Connectors** — see [Add Connectors → AI Connectors](../connectors/add_connectors.md#ai-connectors). It holds where the model is served, the model name and the key.
2. Whoever runs the installation must give admin-service and the document service the same **internal service token** (the `INTERNAL_SERVICE_TOKEN` **environment variable**, at least 32 random characters, kept out of any file that is shared or committed). The document service uses it to read the AI connector's key. Without it, AI drafting stays unavailable even when it is switched on here — and **Test connection** can still succeed, because the test does not use the token.

## AI document drafting

- **Allow drafting documents with AI** – the master switch. When it is on, users can describe a document in a chat on the upload screen.
- **AI connector** – which AI connector writes the documents. Only **Active** AI connectors are listed. Required while the switch is on: without one, the field shows "Choose an AI connector before turning this on." and **Save** stays disabled. If the chosen connector is later made inactive, users lose the **Create with AI** button.
- **Requests per user per day** – how many messages one user may send to the AI per day. Each message counts as one request, including one that fails. Enter a whole number from 0 to 10000; **0** means no limit. The default is 20.

Click **Save** to apply the changes ("AI settings saved."). The button stays disabled until something on the tab has changed. A change can take a few minutes to reach users who already have the app open.

!!! warning "Where the text goes"
    Hosted providers receive the text users type and the documents drafted from it. Check that this is acceptable for your data before turning it on. A model server you run on your own network keeps that text inside the installation.

## Test connection

**Test connection** sends one very short request to the model server of the selected connector and shows the result under the button:

- **Connected in … ms.** – the server answered and the model works.
- Otherwise the stage that failed and what to fix, for example `AUTH: The model server rejected the API key.`

| Stage | What it means |
| --- | --- |
| `CONFIG` | A setting on the connector is missing or wrong — the address, the model name or the provider |
| `CONNECT` | The server could not be reached, or did not answer in time |
| `AUTH` | The server rejected the API key |
| `MODEL` | The model or the address path was not found — check the model name and the server address |
| `RESPONSE` | The server answered, but with an error, a rate limit, or not in the format the provider uses |

The test uses the connector picked in the dropdown **as that connector is saved**, including its stored key, so save any change to the connector first. Nothing is stored by the test. The test waits at most 120 seconds, while real requests use the connector's full timeout, so a slow model on a CPU can fail the test with `CONNECT` and still work for users.

## What users get

- A draft per conversation. Each message makes a new version, and users can go back to an earlier version from the chat.
- Limits that protect cost and the server: a message can be up to 4000 characters, one draft can have up to 30 versions, a user can keep up to 20 drafts that have not been used yet, and only a few documents (4 by default) are generated at the same time by each document-service instance; beyond that users are told "Creating documents with AI is not available right now."
- Drafts are deleted automatically 14 days after they last changed (the installation can set a different period). A document already uploaded from a draft is not affected.
- The screen tells users that AI-generated content can be wrong and must be reviewed before it is sent for signing.

The **Activity Logs** record that a draft was created, generated, turned into a PDF or deleted, with ids, counts and the provider name only. What users type and the documents themselves are never written to any log.
