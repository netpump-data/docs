This guide will show you how to setup the app registration for a Netpump Server cluster through Azure portal.

Each cluster of Netpump Servers uses a single app registration. All Netpump Servers in the cluster can communicate with each other. All users assigned to the app registration can use the Netpump Servers.

## Audience
The user of this guide should have basic or moderate knowledge of how to use Azure Portal, and Microsoft Entra ID.

## Steps

### Create new App Registration
1. Goto `https://portal.azure.com` and login
2. Search for `Microsoft Entra ID` and click on it.

   ![Search for Microsoft Entra ID][click-entra-id]

3. Click `Add` then select `App Registration`

   ![Add App Registration][click-add-app-registration]

4. Type in a name for the app e.g. `Netpump Server`

5. Select `Accounts in this organizational directory only`

6. Click `Register`

### Set up API requirements for the App Registration

#### Setup the Application ID URI

1. Click `Expose an Api`

   ![Click on Expose an Api][click-expose-api]

2. Click `Add` next to the `Application ID URI`

   ![Click to add Application ID URI][expose-api-id-uri] 

3. Click `Save`

   ![Save the Application ID URI][expose-api-id-uri-save]
   > ℹ️ **Note:** Whether you use a default value or a specific value is based on individual company policy, it has no impact on Netpump.

#### Create scope `Transfers.All`

1. Click `Add a Scope`

   ![Click to add a scope][add-a-scope]

2. Fill in the form in with the below values
   > ℹ️ **Note:** Only `Scope Name` is required to match the below value, the consent display names and descriptions can be adjusted to suit company requirements and policy.

   |||
   | ------------- | ------------- |
   | **Scope name**\* | Transfers.All |
   | Who can consent?  | Admin and users |
   | Admin consent display name  | Allows all Transfer Operations |
   | Admin consent description | Allows a user to handle all operation in relations Transfers |
   | User consent display name  | Transfer Admin |
   | User consent description  | Allows a user to handle all operation in relations Transfers |

   ![Fill in scope settings][transfer-all-form]

3. Click `Add Scope`

#### Add authorized client applications

1. Click `Add a client application`

   ![Click to add a client application][add-client-application]

2. Add the client ID `d99b6435-bf29-4655-a1a2-ed1dbad109b3`

   > ℹ️ **Note:** This guid is for the global Netpump Desktop Application 

3. Tick boxes for
   * `Transfers.All`
      > ℹ️ **Note:** The prefix will change depending on the Application ID URI 

   ![Alt text](images/app-reg/500-addclientapp.png)

4. Click `Add application`

### Create an App Role

1. Click `App roles`

   ![App role][300-approlemenu]

2. Click `Create App Role`

3. Enter the details as follows:

   |||
   | ------------- | ------------- |
   | Display name | Automation |
   | Allowed member types | Applications |
   | Value | Automation |
   | Description | Server to server and script access |

   ![Create app role](images/app-reg/400-approlecreate.png)

4. Click `Apply`

### Setup permissions

1. Click `API permissions`

   ![Alt text](images/app-reg/550-apipermmenu.png)

2. Click `Add permission`

   ![Alt text][click-add-permission]

3. Click `APIs my Organization Uses`

4. Search for `Netpump Server`
   > ℹ️ **Note:** The name will depend what name you gave the application in the `App Registration`

   ![Alt text](images/app-reg/600-apipermapp.png)

5. At the "What type of permission?" question, choose `Delegated`, and add the `Transfers.All` permission

   ![Alt text](images/app-reg/700-apipermscope.png)

6. Click `Add permissions` to save this permission.

7. Click `Add permission` a second time

   ![Alt text][click-add-permission]

8. Search for `Netpump Server` again
   > ℹ️ **Note:** The name will depend what name you gave the application in the `App Registration`

9. At the "What type of permission?" question, this time choose `Application`, and add the `Automation` permission

   ![Alt text](images/app-reg/800-apipermauto.png)

10. Click `Add permissions` to save this permission.

### Create the Client Secret

1. Click `Certificates & secrets`

   ![Alt text][create-certificates-secrets]

2. Click `New client secret`

   ![Alt text][new-client-secret]

3. Enter a description for this secret, select the desired expiry (per company requirements) and click `Add`
   > ℹ️ **Note:** This secret will be used for the authentication settings when provisioning Netpump servers.

4. Copy the client secret and save it for Netpump server provisioning later
   > ℹ️ **Note:** You can not view this secret after you leave this page.

   ![Alt text][copy-secret]

### Edit the manifest

1. Click `Manifest` in the menu

   ![Manifest](images/app-reg/900-manifestmenu.png)

2. Edit the manifest to set `accessTokenAcceptedVersion` to the value `2`

   !["accessTokenAcceptedVersion": 2,](images/app-reg/1000-manifestdetails.png)

3. Click `Save`

### Configure Redirect URLs and Token Authentication for Configuration Page

For an administrator to access the Configuration Page, allowed URLs must be added to the application. The URLs should match the DNS record that will be used to access the Netpump Service Configuration Page.

1. Click `Authentication` in the menu

   ![Select Authentication from menu][click-authentiction]

2. Click `+ Add a Platform`.

   ![Add Platform][click-add-a-platform]

3. Select `Web` from the Platform listing

   ![Select Web Platform][select-web-platform]

3. Enter a valid `Redirect URIs` that corresponds to the public DNS. Multiple URIs can be added after saving the initial entry. Select `ID tokens (used for implicit and hybrid flows)` from the options.
   
   ![Enter Redirect URIs and ID Tokens][enter-redirect-uris]

4. Click `Configure`

### Assign users

1. Click `Overview` in the menu

2. Click on the link next to the `Managed application in local directory` label

   ![Overview and link](images/app-reg/1100-overview.png)

3. Click `Properties`

4. Set `Assignment Required` to `Yes`

   ![Assignment required](images/app-reg/1200-appregproperties.png)

5. `Save`

6. Click `Users and groups`

7. Add all users who require access to configure or use the Netpump service

   ![Add users](images/app-reg/1300-add-users.png)

### Key Vault - Service Principal permissions

> ℹ️ **Note:** As a prerequisite, you need a Key Vault with a valid SSL certificate for the domain you want to host your Netpump server on. The steps below cover giving your app registration (service principal) access to that Key Vault.

1. Open your Key Vault resource in Azure Portal and click on `Access control (IAM)`

2. Click on `Add` > `Add role assignment`

   ![Add role assignment](images/app-reg/1400-key-vault-add-role-assignment.png)

3. Select the `Key Vault Secrets User` role then click `Next`

   ![Pick Key Vault Secrets User role](images/app-reg/1500-key-vault-pick-role.png)

4. Click `Select members` then search for `Netpump Server`
   > ℹ️ **Note:** The name will depend what name you gave the application in the `App Registration`

   ![Add the Netpump Server service principal](images/app-reg/1600-key-vault-select-netpump-server.png)

5. Click `Select`

6. Click `Next`

7. Confirm the details and click `Review + assign`

   ![Review and assign](images/app-reg/1700-key-vault-review-role-assignment.png)

## Ready to provision cluster
You are now ready to provision your Netpump server cluster.



[add-a-scope]: images/add-a-scope.png
[transfer-all-form]: images/transfer-all-form.png
[file-transfer]: images/file-transfer.png
[click-entra-id]: images/app-reg/100-entra-id.png
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
[click-authentiction]: images/auth_01_select_manifest.png
[click-add-a-platform]: images/auth_02_choose_platform.png
[select-web-platform]: images/auth_03_choose_web_platform.png
[enter-redirect-uris]: images/auth_04_web_platform_configuration.png