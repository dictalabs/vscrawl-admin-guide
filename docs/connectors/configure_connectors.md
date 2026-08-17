# Configure Connectors in Default Settings

In the previous section, we discussed adding external TSPs and other external components like the email server. From the list of configured connectors, the administrator can choose the default email connector and the default Signing Service Connector using the screen shown below:

![Configure Connectors in Default Settings](../images/configure-connectors.png)

In this screen, the administrator can select the default connectors for:

- **Default Email Connector**: Choose the default email connector to manage email notifications.

- **Default OnBoarding Connector**: Select the default onboarding connector for remote signatures. Only Sign connectors using the **eTugra Middleware** or **Crypto Engine** providers appear in this dropdown.
- **Default CA Connector**: Select the default Certification Authority connector, from any configured **EJBCA**, **DictaLabs CA** or **Microsoft CA** connector.
- **Power Survey Recipient Limit**: Set the maximum number of recipients allowed in a single Power Survey (numbers only). Leave blank to use the application default.

![Power Survey Recipient Limit](../images/admin-default-settings-power-survey-limit.png)
