# Service Plan  

After configuring finance settings such as currency and pricing details, the next step is to create one or more service plans. A service plan defines which types of signature qualifiers are available to an organization or to a group of signing users assigned to that plan.

From the left navigation pane, click on **Service Plan** under **FINANCE** to open the Service Plan page.  

![Service Plan 1](../images/service-plan-list.png)

From this page, administrators can view a list of current service plans configured in this deployment of vScrawl.  These plans may be purchased by / assigned to particular organizations and/or signing users.  From this list an administrator can click the three-dots icon under **Actions** and choose **Edit plan** to update an existing service plan, or **Delete plan** to remove it.  A new service plan can always be added by clicking the **Add Plan** button on the top.

Clicking Add Plan button shows the following screen:

![Service Plan 2](../images/add-service-plan.png)

To add a new service plan, provide:

- A **Name** for the service plan, and optionally provide its description.
- Status whether **ACTIVE** or **INACTIVE**.
- Select the signature qualifiers i.e. **SES**, **AES** and/or **QES**.  In case any of **AES** and/or **QES** is selected in this service plan, it will be additionally required to select an option from the list of available signing connectors, for each of these.
- Administrator may additionally choose to turn on 2-Factor Authentication for users who use this service plan.
- Allows administrators to control whether users can share workflow documents with other users. When enabled, users can share documents for collaboration; when disabled, document sharing is restricted.
- Allows users to log in to the system using **SmartCard authentication**. When enabled, SmartCard login is available; when disabled, users must use the standard login methods.
- **Compliance Mode** – Restrict which signing modes this service plan allows (cannot exceed what the uploaded license permits — see [License Manager](../other_admin_operations/license_manager.md)):
    - **eIDAS (EU)** – Advanced & Qualified Electronic Signatures.
    - **ESIGN + UETA (US)** – Simple Electronic Signatures with consent disclosure.
    - These toggles only appear when the corresponding mode is enabled on the license. When eIDAS mode is on, at least one AES/QES connector must be selected above.
- **Cloud Source** – Choose whether organizations on this plan may import documents from **Google Drive** or **Dropbox**, and which connector each provider uses. See [Cloud Source](#cloud-source) below.
- **Custom Email Connector** – Toggle **Allow custom email connector** to let organizations on this plan use their own email connector(s) instead of the system default, then select which connectors they may use from **Available email connectors**. This option is only available when the license permits custom SMTP.

![Compliance Mode and Custom Email Connector](../images/admin-service-plan-compliance-custom-email.png)

---

## Cloud Source

A signer can bring a document in from their own Google Drive or Dropbox instead of browsing their computer. Whether that is offered is decided **per service plan**, so it can be granted to some organizations and withheld from others on the same installation.

Two things are set for each provider, and both are required:

- **Import from Google Drive** / **Import from Dropbox** – whether the provider is offered at all.
- **Connector** – which [Cloud Source connector](../connectors/add_connectors.md#cloud-source-connectors) supplies the credentials. The plan cannot be saved with a provider switched on and no connector chosen.

The connector is named explicitly rather than picked automatically because more than one can exist. An installation may keep a separate Google project or Dropbox app per environment, per customer, or per brand, and "the newest active one" would be a guess at which of those an organization consented to.

Only **Active** connectors of that provider are listed. If a plan points at a connector that has since been deactivated, deleted or changed to another purpose, the dropdown shows it as *no longer available* so the stale choice is visible rather than silently kept.

### What a signer sees

- The provider appears on the upload screen only when the plan grants it **and** the named connector is still usable. Anything missing means the button is simply not shown — a signer has no use for the difference between "your plan does not include this" and "an administrator misconfigured it", and a half-configured provider would put a button on screen that cannot work.
- Switching a provider off removes it at the users' next sign-in. Documents already imported are unaffected: an imported file is an ordinary uploaded document from the moment it arrives.

### Swapping the connector

Because the plan records the connector's **id**, moving to a different connector is a change to every plan that offers the provider — not only to the connector list.

Add the new connector, select it on each plan, and **only then** deactivate the old one. Nothing prevents the old connector from being deleted while a plan still names it: the delete succeeds, the plan is left holding an id that resolves to nothing, and the provider stops being offered with no error shown anywhere.

> **Note:** The connector itself is added under [Connectors](../connectors/add_connectors.md#cloud-source-connectors), and the provider-side application it needs is described in [Set Up Cloud Source Providers](../connectors/cloud_source_setup.md).
