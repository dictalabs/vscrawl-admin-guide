# License Manager  

From the left navigation pane, click on **License** under **APPLICATION** to open the License Manager.  

![License Manager 1](../images/license-manager-one.png)

![License Manager 2](../images/license-manager-two.png)

From this page, administrators can:  

- View details relevant to the configured vScrawl license file, including:  
    - License type  
    - **Compliance Mode** – shows which signing regulation regimes this license permits: **eIDAS (EU)**, **ESIGN / UETA (US)**, or **eIDAS + ESIGN / UETA** if both are enabled.
    - Product name  
    - License period  
    - Customer information (to whom the license file was issued)  
- Review limits applied by the license file on various licensed modules.  

![admin-license-manager-compliance-mode.png](../images/admin-license-manager-compliance-mode.png)

!!! note ""
    Compliance Mode drives which features appear elsewhere in the app. **eIDAS** mode is required for the [Qualified Certificate Requests](qualified_cert_requests.md) screen, for the AES and QES qualifiers and the eIDAS Compliance Mode toggle on a [Service Plan](../finance_settings/service_plans.md), for **Enable Digital Signatures in the Application** on the [Signature](signature_settings.md) tab, and for the [User Onboarding](authentication_settings.md#user-onboarding) settings to be available. The license also decides whether a service plan may offer a [Custom Email Connector](../finance_settings/service_plans.md#custom-email-connector).
- Download the license file signing certificate and the license file itself (**Download Certificate** / **Download License**) for verifying the signature on the license file.
- Configure a new license file — drag and drop the license XML file onto the page, or click **Choose File** — if:
    - The current license is about to expire.
    - A new license is required to enable additional application modules.
- A new license file can be requested by contacting the respective sales representative or by writing to [info@dictalabs.com](mailto:info@dictalabs.com).   
