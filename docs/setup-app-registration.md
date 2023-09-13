This guide will show you how to setup the Netpump Server through Azure's marketplace offering

## Prerequisite
The user of this guide should have basic or moderate knowledge of how to use Azure Portal, and Azure Active Directory.

## Steps

1. Goto `https://portal.azure.com` and login
2. Goto `Azure Active Directory`
![Alt text][click-aad]

3. Click `Add` then select `App Registration`
![Alt text][click-add-app-registration]

4. Type in a name for the app e.g. `Netpump Server`

5. Select `Accounts in any organizational directory (Any Microsoft Entra ID tenant - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)`
![Alt text][click-app-registration-supported]

6. Click `Register`
7. Click `Expose an Api`
![Alt text][click-expose-api]
8. Click `Add` next to the `Application ID URI`
![Alt text][expose-api-id-uri] 
9. Click `Save`
![Alt text][expose-api-id-uri-save]
> whether you use a default value or a specific value is based on individual company policy, it has no effort on Netpump.

11. Create two scopes, the first scope is for `Transfers.All`
> The only value here is that is a hard requirement is the scope name, all the other views can set based on individual company policy 

  1. Click `Add a Scope`
  ![Alt text][add-a-scope]
  2. Fill in the form in with the below values
![Alt text][transfer-all-form]

  |||
  | ------------- | ------------- |
  | **Scope name**\* | Transfers.All |
  | Who can consent?  | Admin and users |
  | Admin consent description  | Allows all Transfer Operations |
  | User consent display name  | Allows a user to handle all operation in relations Transfers |
  | User consent description  | Transfer Admin |
  | Content Cell  | Allows a user to handle all operation in relations Transfers |

  3. Click `Add Scope` 
---

12. create a second scope for `File.Transfer`
> 
  1. Click `Add a Scope`
  ![Alt text][add-a-scope]
  2. Fill in the form in with the below values
  ![Alt text][file-transfer]

  |||
  | ------------- | ------------- |
  | **Scope name**\*  | File.Transfer |
  | Who can consent?  | Admin and users |
  | Admin consent description  | Start file transfers |
  | User consent display name  | Allows users to start file transfers |
  | User consent description  | Start file transfers |
  | Content Cell  | Allows you to start file transfers |

  3. Click `Add Scope`
---

13. Adding authorized client applications
>
  1. Click `Add a client application`
![Alt text][add-client-application]

  2. Add the client ID `d99b6435-bf29-4655-a1a2-ed1dbad109b3`
  ![Alt text][client-id]
  > This guid is for the global `Netpump Desktop` Application 

  3. Tick boxes for 
   * `File.Transfer`
   * `Transfers.all`
   > The prefixes will change depending on the Application ID URI 

  ![Alt text][client-authorized-scopes]

---

14. Setup permissions
>
  1. Click `API permissions`
  ![Alt text][click-api-permissions]

  2. Click `Add permission`
  ![Alt text][click-add-permission]

  3. Click `My APIs`
  ![Alt text][click-my-apis]

  4. Click `Netpump Server`
  > The name will depend what name you gave the application in the `App Registration`

  ![Alt text][click-my-apis-netpump-server]

  5. Add the `File.Transfer` permission
  ![Alt text][my-apis-add-file-transfer]

  6. Add the `Transfers.All` permission
  ![Alt text][my-apis-add-transfers-all]

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

You are now ready to prevision your Netpump Server

[add-a-scope]: images/add-a-scope.png
[transfer-all-form]: images/transfer-all-form.png
[file-transfer]: images/file-transfer.png
[click-aad]: images/click-aad.png
[click-add-app-registration]: images/click-add-app-registration.png
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
