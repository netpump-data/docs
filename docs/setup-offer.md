This guide will show you how to setup the Netpump Server through Azure's marketplace offering

## Prerequisite
The user of this guide should have basic or moderate knowledge of how to use Azure Portal, and Azure Active Directory.

1. A valid deployment environment
 > The service is intended to be used in an existing network with valid SMB share setup and network routing.

2. Provision of the Enterprise Application.
 > A guide can be find provision the enterprise application can be found [here](setup-app-registration.md)

3. Download and install the Netpump Desktop application [here][download-link]

4. A domain name and DNS entries for each server you will create.
> Managing DNS is outside the scope of this documentation.

5. A keyVault with a provisioned SSL Certificate of the custom domain you wish to host the Netpump Server Application on.
> Acquiring a certificate is outside the scope of this documentation. Azure Key Vault provides ways to acquire certificates from third party certification authorities, or import your own.

## Provisioning Netpump server

1. Goto Azure Portal https://portal.azure.com and login 
2. Create a new resource
![Alt text][create-new-resource]
3. search for `Netpump` 
![Alt text][search-netpump]

> If the Netpump subscription has been shared with you as a private plan, you will need to click `view private plans` to see the plan

4. Select the Netpump offer
![Alt text][select-offer]

5. Click `create`
![Alt text][click-create]

6. Follow the prompts in the deployments steps until you get to Authentication Settings

7. Once you are on the Additional Settings tab, you will need to fill out the Authentication settings
![Alt text][auth-settings]
Use the information from the `App Registration` of the Netpump server that you have already created in the prerequisite steps
![Alt text][app-registration-example]

8. Add the user assigned managed identity that you created in the prerequisite step `4`
![Alt text][add-user-assigned-managed-identity]

9. Select the desired certificate for the custom domain

10.  The `User assigned identity name` question should be the name of the User Assigned Managed Identity from step `8.`
![Alt text][identity-name-example]

11. Click `Review & create`

After the deployments complete you edit the NIC of the Netpump Server so that two scenarios  
* Netpump Desktop application needs access to the Netpump Destination Server
* The Netpump Destination server and the Netpump origin server 

12. Repeat the above process for each server in your Netpump cluster. At a minimum you will need to provision two servers to see accelerated file transfers between them.

13. Create a DNS entry for the newly created server in your DNS system. The domain name you choose must match the SSL certificate configured in your Key Vault.

Once that is done, you should be able to connect to the Netpump server using the Netpump Desktop application.

[search-netpump]: images/search-netpump.png
[create-new-resource]: images/create-new-resource.png
[select-offer]: images/select-offer.png
[click-create]: images/click-create.png
[auth-settings]: images/auth-settings.png
[app-registration-example]: images/app-registration-example.png
[add-user-assigned-managed-identity]: images/add-user-assigned-managed-identity.png
[identity-name-example]: images/identity-name-example.png
[setup-app-registration]: setup-app-registration.md
[example-network]: google.com
[download-link]: http://netpump.com.au/