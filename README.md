# Azure Service Principal expiry notification

Automatic notification of expiry of secrets and certificates on SPNs is still not built into the platform. It is possible to develop a custom solution using Azure services to perform these notifications, using one of the following options:

1. Logic App: Workflow based configuration, and my recommended approach. Uses built-in connectors and operations to simplify the work of fetching the data.
* [Steps - Use Azure Logic Apps to Notify of Pending AAD Application Client Secrets and Certificate Expirations - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/use-azure-logic-apps-to-notify-of-pending-aad-application-client/ba-p/3014603)
* Instead of storing and fetching the identity in the Key Vault and then getting the access token (as shown in the example above), I recommend using the Logic App’s MSI and assigning it the required permissions. This article was written before MSIs were available for Logic Apps. It will be easier to manage the credentials and will simplify the workflow.
* For the Send Email step, there are a few options:
  * The example uses an Office 365 connection. You can use the credentials of anyone who can login and has the appropriate license.
  * Alternatively, you can use the SMTP connector and provide explicit credentials to connect to a non-Office 365 server.
* This example sends the email to a fixed list of users. With some changes, you could also send a similar email to the owners of the SPNs as well (if set).

2.	Azure Automation: PowerShell (or Python) based workbooks. A more traditional approach that will require authoring of a script to read the data out of Azure AD, process the data and send out an email. Can use the MSI of the service, credentials stored in the automation account or credentials from a Key Vault.
* [Steps - App Registration Expiration Monitoring and Notifications - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/app-registration-expiration-monitoring-and-notifications/ba-p/2043805)
* Like above, this example uses an app registration to obtain an access token. Azure Automation now supports MSIs which can be given permissions into Azure AD. This will simplify the code and process.
* For sending email notification, this example uses a different approach. It leverages Log Analytics and Azure Monitor Alerts. The workbook writes the status of expiration to a Log Analytics workspace, then leverages built in Azure Monitor Alerts to send out the notification.
  * On one hand, this removes the need to manage email notifications, it leverages Azure native services to do so. Additionally, it makes the data available to others to consume in its raw form.
  * On the other hand, it limits the customization of notifications that can be sent out to what Azure Monitor Alerts offers out of the box.

