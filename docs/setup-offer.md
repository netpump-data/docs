This guide will show you how to setup the Netpump Server through Azure's marketplace offering

## Prerequisites
The user of this guide should have basic or moderate knowledge of how to use Azure Portal, and Microsoft Entra ID.

1. A valid deployment environment
   > ℹ️ **Note:** The service is intended to be used in an existing network with valid SMB share setup and network routing.

2. A domain name and DNS entries for each server you will create.
   > ℹ️ **Note:** Managing DNS is outside the scope of this documentation.

3. An Azure Key Vault with a provisioned SSL Certificate of the custom domain you wish to host the Netpump Server Application on.
   > ℹ️ **Note:** Acquiring a certificate is outside the scope of this documentation. Azure Key Vault provides ways to acquire certificates from third party certification authorities, or import your own.
   * You can [follow this quickstart from Microsoft](https://learn.microsoft.com/en-us/azure/key-vault/general/quick-create-portal) to set up a new Key Vault in Azure if you don't have one configured already.

4. The Netpump server cluster Enterprise Application
   * [Follow this guide](setup-app-registration.md) to provision the enterprise application.

5. Netpump Desktop application
   * You can download and install the Netpump Desktop application [here][download-link]

## Provisioning Netpump server

1. Go to Azure Portal https://portal.azure.com and login 
2. Create a new resource

   ![Create new resource][create-new-resource]

3. Search for `Netpump`

   ![Search for Netpump][search-netpump]

> ℹ️ **Note:** If the Netpump subscription has been shared with you as a private plan, you will need to click `View private plans` to see the plan.

4. Select the Netpump offer

   ![Select Netpump offer][select-offer]

5. Click `Create`

   ![Click Create][click-create]

6. Select your desired subscription, resource group, region and managed application details then click Next.

   ![Basic settings example][basic-settings]

7. Enter your file share settings then click Next.

   ![File share settings example][file-share-settings]

8. On the `Deployment Details` tab, setup the network settings for the Netpump server as required for your company network then click Next.

   ![Network settings example][network-settings]

9. On the `Additional Settings` tab, fill out the Authentication settings

   ![Enter authentication settings][auth-settings]

   Use the information (including client secret) from the `App Registration` of the Netpump server created in prerequisite step `4`.

   ![App registration example][app-registration-example]

   ![App registration client secret example][app-registration-client-secret-example]

11. Select the desired certificate for the custom domain from your Key Vault (prerequisite step `3`)

12. Enter a custom Endpoint URL the application will run under. The port must be a valid https protocol port number. Leave blank to use the default https://*.443 port number.

13. Click `Review & create`

After the deployments complete you can edit the NIC of the Netpump Server to enable:
* The Netpump Desktop application to be able to connect to the Netpump destination server
* The Netpump destination server and the Netpump origin server to be able to connect to eachother

14. Repeat the above process for each server in your Netpump cluster. At a minimum you will need to provision two servers to enable accelerated file transfers between them.

15. Create a DNS entry for the newly created server in your DNS system. The domain name you choose must match the SSL certificate configured in your Key Vault.

Once that is done, you should be able to connect to the Netpump server using the Netpump Desktop application.

[search-netpump]: images/search-netpump.png
[create-new-resource]: images/create-new-resource.png
[select-offer]: images/select-offer.png
[click-create]: images/click-create.png
[basic-settings]: images/basic-settings.png
[network-settings]: images/network-settings.png
[file-share-settings]: images/file-share-settings.png
[auth-settings]: images/auth-settings.png
[app-registration-example]: images/app-registration-example.png
[app-registration-client-secret-example]: images/app-registration-secret-example.png
[add-user-assigned-managed-identity]: images/add-user-assigned-managed-identity.png
[identity-name-example]: images/identity-name-example.png
[setup-app-registration]: setup-app-registration.md
[example-network]: google.com
[download-link]: http://netpump.com.au/