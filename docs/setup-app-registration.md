This guide will show you how to setup the app registration for a Netpump Server cluster through Azure portal.

Each cluster of Netpump Servers uses a single app registration. All Netpump Servers in the cluster can communicate with each other. All users assigned to the app registration can use the Netpump Servers.

## Audience
The user of this guide should have basic or moderate knowledge of how to use Azure Portal, and Azure Active Directory.

## Steps

1. Goto `https://portal.azure.com` and login
2. Search for `Azure Active Directory` and click on it.
![Alt text][click-aad]

3. Click `Add` then select `App Registration`
![Alt text][click-add-app-registration]

4. Type in a name for the app e.g. `Netpump Server`

5. Select `Accounts in this organizational directory only`

6. Click `Register`
7. Click `Expose an Api`
![Alt text][click-expose-api]
8. Click `Add` next to the `Application ID URI`
![Alt text][expose-api-id-uri] 
9. Click `Save`
![Alt text][expose-api-id-uri-save]
> whether you use a default value or a specific value is based on individual company policy, it has no effort on Netpump.

11. Create a scopes `Transfers.All`
> The only value here is that is a requirement is the scope name, the text descriptions can be varied depending on your preference.

  1. Click `Add a Scope`
  ![Alt text][add-a-scope]
  2. Fill in the form in with the below values
![Alt text][transfer-all-form]

  |||
  | ------------- | ------------- |
  | **Scope name**\* | Transfers.All |
  | Who can consent?  | Admin and users |
  | Admin consent display name  | Allows all Transfer Operations |
  | Admin consent description | Allows a user to handle all operation in relations Transfers |
  | User consent display name  | Transfer Admin |
  | User consent description  | Allows a user to handle all operation in relations Transfers |

  3. Click `Add Scope` 
---
12. Create an App Role
>
  1. Click App Roles

![App role][300-approlemenu]

  2. Click Create App Role

  3. Enter the details as follows:

![Create app role](images/app-reg/400-approlecreate.png)

  |||
  | ------------- | ------------- |
  | Display name | Automation |
  | Allowed member types | Applications |
  | Value | Automation |
  | Description | Server to server and script access |

---
13. Adding authorized client applications
>
  1. Click `Add a client application`
![Alt text][add-client-application]

  2. Add the client ID `d99b6435-bf29-4655-a1a2-ed1dbad109b3`
 
  > This guid is for the global `Netpump Desktop` Application 

  3. Tick boxes for 
   * `Transfers.All`
   > The prefix will change depending on the Application ID URI 

  ![Alt text](images/app-reg/500-addclientapp.png)

---

14. Setup permissions
>
  1. Click `API permissions`

  ![Alt text](images/app-reg/550-apipermmenu.png)

  2. Click `Add permission`
  ![Alt text][click-add-permission]

  3. Click `APIs my Organization Uses`

  4. Search for `Netpump Server`
  > The name will depend what name you gave the application in the `App Registration`

  ![Alt text](images/app-reg/600-apipermapp.png)

  5. At the "What type of permission?" question, choose `Delegated`, and add the `Transfers.All` permission
  ![Alt text](images/app-reg/700-apipermscope.png)

  6. Click `Add permissions` to save this permission.

  7. Click `Add permission` a second time
  ![Alt text][click-add-permission]

  8. Search for `Netpump Server` again
  > The name will depend what name you gave the application in the `App Registration`

  9. At the "What type of permission?" question, this time choose `Application`, and add the `Automation` permission
  ![Alt text](images/app-reg/800-apipermauto.png)

  10. Click `Add permissions` to save this permission.

---
15. Create the Client Secret
>
  1. Click `Certificates & secrets`
  ![Alt text][create-certificates-secrets]

  2. Click `New client secret`
  ![Alt text][new-client-secret]

  3. Fill in the form and click `save`

  4. Copy the client secret and save it for later
  > You can not view this secret after you leave this page. You will need for when prevision the Netpump Server

  ![Alt text][copy-secret]

16. Edit the manifest
>
  1. Click `Manifest` in the menu

  ![Manifest](images/app-reg/900-manifestmenu.png)

  2. Edit the manifest to set `accessTokenAcceptedVersion` to the value `2`

  !["accessTokenAcceptedVersion": 2,](images/app-reg/1000-manifestdetails.png)

  3. Click `Save`

17. Assign users
>
  1. Click `Overview` in the menu

  2. Click on the link next to the `Managed application in local directory` label

  ![Overview and link](images/app-reg/1100-overview.png)

  3. Click Properties

  4. Set `Assignment Required` to `Yes`

  ![Assignment required](images/app-reg/1200-appregproperties.png)

  5. `Save`


## Ready to provision cluster
You are now ready to provision your Netpump Server cluster.



[add-a-scope]: images/add-a-scope.png
[transfer-all-form]: images/transfer-all-form.png
[file-transfer]: images/file-transfer.png
[click-aad]: images/app-reg/100-aad.png
[click-add-app-registration]: images/app-reg/200-appreg.png
[click-app-registration-supported]: images/app-registration-supported.png
[click-expose-api]: images/click-expose-api.png
[expose-api-id-uri]: images/expose-api-id-uri.png
[expose-api-id-uri-save]: images/expose-api-id-uri-save.png
[add-client-application]: images/add-client-application.png
[client-id]: images/client-id.png
[client-authorized-scopes]: images/client-authorized-scopes.png
[click-api-permissions]: images/click-api-permissions.png
[click-add-permission]: images/click-add-permission.png
[click-my-apis]: images/click-my-apis.png
[click-my-apis-netpump-server]: images/click-my-apis-netpump-server.png
[my-apis-add-file-transfer]: images/my-apis-add-file-transfer.png
[my-apis-add-transfers-all]: images/my-apis-add-transfers-all.png
[create-certificates-secrets]: images/create-certificates-secrets.png
[new-client-secret]: images/new-client-secret.png
[copy-secret]: images/copy-secret.png
[300-approlemenu]: images/app-reg/300-approlemenu.png
