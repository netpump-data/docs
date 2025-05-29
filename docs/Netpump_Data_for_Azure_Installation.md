# Netpump Data for Azure — Installation

## Guide

This guide provides a foolproof, step-by-step process to deploy Netpump Data on Azure. It is written for junior cloud administrators with limited Azure experience, so each step is ultra-explicit.

Follow the steps in order and use a “build sheet” (e.g. a local text file or spreadsheet) to record important values (IDs, secrets, URLs) as you go. Browser-specific tips, common pitfalls, and validation checks are included to ensure a smooth setup.

### Contents ###

Before You Start – Quick Checklist	

1. Create Two Resource Groups	
2. Add Key Vaults to the Resource Groups	
3. Register the Server Application (Back-end AAD App)	
4. Register the Client Application (Front-end AAD App)	
5. Configure Authentication for the Client App	
6. Assign the Admin Role to Yourself (or a Group)	
7. Grant Key Vault Access to the Client App	
8. Generate the SSL Certificate in Key Vault	
9. Create a Storage Account and an SMB File Share	
10. Deploy Netpump Data from the Azure Marketplace	
11. Retrieve Deployment Outputs and Configure DNS Records	
12. Configure Each Netpump Server via the Admin UI	
13. (Optional) Install the Netpump Desktop GUI for End Users
14. Install Netpump Data - On Premises version for Internal Servers
15. Use of PowerShell for Scripting Functions
16. Post-Deployment Validation – Confirm Everything is Working
17. Where to Get Help	


### Before You Start – Quick Checklist

Make sure you have the following prerequisites ready before proceeding. You can tick each item off as you confirm it:

- **Global Administrator rights in Microsoft Entra ID (Azure AD)**: Needed to grant consent and assign app roles in Azure AD.
- **Owner role on the target Azure subscription**: Needed to create resource groups and the managed application.
- **Decided on an Azure region (e.g. West Europe)**: All resources must reside in the same region for this setup.
- **A naming prefix for resources (e.g. netpump-prod)**: Keeps every Azure resource easily identifiable.
- **Your public DNS zone (e.g. pump.example.com)**: Required for creating DNS CNAME records later.
- **A local “build sheet” (spreadsheet or text file) open**: Where you will paste IDs, secrets, and URLs as you obtain them.

Now, let’s begin the installation step-by-step.

### 1. Create Two Resource Groups

Create the core resource group: In the Azure Portal, go to Resource groups and click Create. In the Basics tab of the Create a resource group form, enter the following:

- **Subscription**: Select your target subscription (ensure you have Owner access on it).
- **Resource group name**: `rg-netpump-core` (using your chosen prefix).
- **Region**: Your chosen region (e.g. West Europe).

Click Review + Create, then Create. Wait for the notification that says “Deployment succeeded.” This confirms the resource group was created successfully.

![Create_a_resource_group][001]

Create the install resource group: Repeat the above process to create a second resource group named `rg-netpump-install` in the same region. This second group will be used to hold temporary installation assets separately from core resources. Again, wait for the Deployment succeeded message.

**Why two resource groups?** Separating core resources and installation assets helps with organization and cleanup. The `rg-netpump-core` group will contain the persistent Netpump service resources, while `rg-netpump-install` will contain short-lived setup resources that can be isolated or removed later.

### 2. Add Key Vaults to the Resource Groups

You will create two Azure Key Vaults – one in each resource group:

#### Core Key Vault

In the `rg-netpump-core` resource group, click + Create and search for Key Vault. Select Key Vault and click Create. In the Key Vault creation form:

- **Key Vault Name**: `kv-netpump-core` (must be globally unique within Azure, usually the prefix plus core is fine).
- **Region**: Same region as above.
- Leave other settings at defaults (for this setup, standard SKU and default access policy settings are fine).
- Click Review + Create, then Create. Wait for the Key Vault deployment to succeed.

#### Install Key Vault

Repeat the process in the `rg-netpump-install` group. Create a Key Vault named `kv-netpump-install` (same region). Wait for deployment success.

**Why two Key Vaults?** The core vault (`kv-netpump-core`) is intended to store long-lived secrets like cluster certificates, while the install vault (`kv-netpump-install`) will store short-lived secrets used during the installation process. This separation adds security and clarity.

**Validation**: After creation, verify in the portal that each resource group contains one Key Vault with the expected name. For example, in `rg-netpump-core` you should see `kv-netpump-core`, and in `rg-netpump-install` you should see `kv-netpump-install`.

### 3. Register the Server Application (Back-end AAD App)

Next, set up an Azure AD app registration to represent the Netpump server cluster (the back-end API):

In the Azure Portal, open Microsoft Entra ID (Azure Active Directory). From the left menu, select App registrations, then click + New registration.

Fill out the Register an application form:

- **Name**: `NetpumpServerCluster` (or a similar name identifying the Netpump server cluster).
- **Supported account types**: Choose Accounts in this organizational directory only (Single tenant).
- **Redirect URI**: Leave this blank for the server app (no redirect URI needed for a backend service).
- Click Register to create the application.

After registration, you will be taken to the `NetpumpServerCluster` app overview. Copy the following IDs from the overview and paste them into your build sheet for later use:

- **Directory (tenant) ID** – (GUID identifying your Azure AD tenant).
- **Application (client) ID** – (GUID for this `NetpumpServerCluster` app).

Still on the `NetpumpServerCluster` app, configure it to expose an API:

- In the app's left-hand menu, go to Expose an API.
- Click Set (or Add an Application ID URI). Accept the suggested default Application ID URI (it will look like `api://` or similar) or adjust it if needed (e.g., `api://netpump-prod-`). Then click Save.
- Now click Add a scope. In the Add an API scope form, set:
  - **Scope name**: `Data.Transfer.All`
  - **Admin consent display name**: `Transfer data via Netpump`
  - **Admin consent description**: `Allows transferring data through Netpump servers.`
  - **State**: Enabled (Yes).
- Click Add scope to create this scope.

**Validation**: Back on the Expose an API page, you should now see the new scope listed, and its Enabled status should show “Yes”. This confirms your API scope was created successfully.

### 4. Register the Client Application (Front-end AAD App)

Now create a second Azure AD app registration to serve as the client/front-end application (for the Netpump UI or user interactions):

In App registrations, click + New registration again. Create another app with:

- **Name**: `NetpumpClient` (to denote this is the client-facing app).
- **Supported account types**: Accounts in this organizational directory only (Single tenant).
- **Redirect URI**: Leave blank for now (we will configure authentication later).
- Click Register. Once created, note the Application (client) ID for `NetpumpClient` (copy it to your build sheet; you already have the tenant ID from before, which is the same for both apps).

We need to define an app role in this client app, so that certain users (or the Netpump server itself) can be assigned as administrators:

In the `NetpumpClient` app, open the Manifest (you’ll find "Manifest" in the left menu of the app registration blade).

The manifest is a JSON file. Find the `"appRoles": []` section. Insert a new object inside the array to define a role. For example, if the appRoles section is empty (`"appRoles": []`), you can replace it with the following (make sure it’s inside the square brackets and comma-separated if there are other roles):









You will also need to verify “requestedAccessTokenversion”: 2. If it appears with a NULL value, change the value to “2” as shown.
Important: Replace `` with a new unique GUID. (You can generate a GUID using a tool or online GUID generator. Every app role id must be a unique GUID.)
Save the manifest changes. (The portal will validate the JSON – ensure the syntax is correct.)
Next, grant this client app permission to call the server app’s API:

In the NetpumpClient app, go to API Permissions.
Click + Add a permission → select My APIs → you should see NetpumpServerCluster in the list. Select it.
Under the Delegated permissions section, find and check the Data.Transfer.All scope (the one you created in the server app) and then click Add permissions.
The permission will be added with a warning that admin consent is required. Click the Grant admin consent for [Your Tenant] button. Confirm the consent when prompted. This grants tenant-wide consent for your client app to call the server app’s API (so users won’t need to consent individually).

Validation: After granting consent, the API permission should show the status Granted for [Your Tenant]. This indicates the permission is active. If you refresh the API Permissions page, you shouldn’t see a “Not granted” warning anymore.
Lastly for the client app, create a client secret that the Netpump service will use to authenticate as this app:

In the NetpumpClient app, go to Certificates & secrets.
Click + New client secret. For Description, enter something like svc-auth (to indicate this secret is for the service authentication). Set an appropriate expiration (e.g., 24 months).
Click Add. Azure will generate a new secret. Copy the secret’s Value immediately and paste it into your build sheet (e.g., under “Client Secret”). You will not be able to view this value again after you leave the page.

Common Pitfall: Not copying the secret value right away. If you navigate away, the value will be hidden forever and you’d have to create a new secret. Always store it securely as soon as it’s created.
5. Configure Authentication for the Client App
Now that the two app registrations are created, configure the authentication settings for the client app (NetpumpClient) so that it can be used for user sign-ins (including from the desktop GUI, if used):

In the NetpumpClient app registration, go to Authentication in the menu.
Under Platform configurations, click + Add a platform and choose Web (since the Netpump server will interact with this as a web client).
Add a Redirect URI of type Web: enter https://localhost (we use localhost as a placeholder redirect URI for testing/desktop app scenarios).
Under Implicit grant and Hybrid flows, check the boxes for Access tokens and ID tokens. (This enables OAuth2 implicit flow if needed and ensures an access token and ID token are provided in the response.)
Click Configure to save this platform configuration.

(Optional) If you anticipate building a single-page application or JavaScript front-end running on a different port (for example, a development React app on http://localhost:3000), you can also add a SPA platform with that redirect URI. This step is not necessary for the basic installation, but can be done similarly by selecting SPA and adding http://localhost:3000 as a redirect URI (and enabling tokens).
Click Save on the Authentication page if it didn't auto-save. The client app is now configured to allow interactive user sign-ins.
Validation: After saving, you should see the new platform (Web) listed with the URI, and the checkboxes for Access tokens/ID tokens should remain checked. This confirms the client app is ready for user authentication flows.
6. Assign the Admin Role to Yourself (or a Group)
Recall we created an app role ServerAdmin in the NetpumpClient app’s manifest. Now we must assign this role to the appropriate user(s) who will administer the Netpump servers (likely you, and/or a group of admins):

In the Azure Portal, go to Microsoft Entra ID (Azure AD) and navigate to Enterprise applications (this is the list of service principals for your apps). Locate the application NetpumpClient in the list (you can use the search bar if needed) and click it.
In the NetpumpClient enterprise application blade, go to Users and groups in the left menu.
Click + Add user/group at the top. In the Add Assignment pane:

Click Users > None Selected to open the user picker. Find and select your own user account (or an Azure AD group containing the relevant administrators). Click Select.
Now click Roles > None Selected. You should see the ServerAdmin role we created. Select ServerAdmin and click Select.
Click Assign to assign the selected user (you) to the ServerAdmin role for the NetpumpClient app.



After assignment, you can verify it’s in place: the user or group should now be listed under Users and groups with Role showing as ServerAdmin.
Validation: Refresh the Users and groups page for NetpumpClient. You should see your account (or chosen group) listed with ServerAdmin under the Role column. This means you (or your admins group) now have the elevated role needed to manage Netpump servers via the application.
Why do this? Assigning the ServerAdmin role ensures that only authorized users can administer the Netpump Data service. In later steps, when you log into the Netpump interface, the system will check that your account has this role before granting admin access.
7. Grant Key Vault Access to the Client App
The Netpump deployment (running under the context of the NetpumpClient app) will need to retrieve secrets (like certificates) from Azure Key Vault. We must grant it the necessary permissions on the Key Vaults:

Go to the Key Vaults blade and open the kv-netpump-install vault (the one in the install resource group).
In the key vault, under Settings, click Access policies (ensure you are using Vault access policy model, which is default).
Click + Create (or Add Access Policy). In the Add access policy form:

For Configure from template (optional), you can leave it as “None” (we’ll select permissions manually).
Under Key permissions you can leave unselected (no key needed for now).
Under Secret permissions, select Get and List.
Under Certificate permissions, select Get and List as well. (This will allow the app to retrieve certificate secrets from the vault.)
In the Principal field, click None selected to open the principal picker. Search for NetpumpClient (the name of the app registration we created). You should see it appear (it will have a guid ID associated). Select it and click Select.
Click Add to add this access policy, then Save on the Access Policies page to apply it.



(Optional) If you anticipate that the Netpump service will need to access the core vault (kv-netpump-core) at runtime (for example, to fetch cluster certificates stored there), you can repeat the above steps for kv-netpump-core as well. Grant the same Get and List for secrets and certificates to the NetpumpClient principal on that vault. (This step can be done later or as needed; it’s not strictly required for the initial deployment, since we will put our certificate in the install vault.)
Validation: After saving the access policy, in the Access policies list for kv-netpump-install, you should see an entry for Principal: NetpumpClient with the selected Secret Permissions and Certificate Permissions listed (Get, List). This indicates the client app (and thus our Netpump service) can read secrets from that vault. If you open the kv-netpump-install Overview and click Managed identities, you will not see the app here because we added an access policy directly for the service principal; that's expected.
Common Pitfall: Selecting the wrong principal or vault. Ensure you picked the NetpumpClient app (not the server app) for the access policy, and that you applied it on the kv-netpump-install vault. A simple mistake here could cause permission issues when the application tries to retrieve secrets later.
8. Generate the SSL Certificate in Key Vault
Netpump requires an SSL certificate for secure communication. We will generate a self-signed certificate in the Key Vault to use for this purpose:

Open the kv-netpump-install Key Vault (the install vault). In the vault, go to the Certificates section and click + Generate/Import.
In the Create a certificate screen:

Method of Certificate Creation: Choose Generate (to create a new certificate).
Certificate Name: netpump-tls (you can use this name for clarity; it will be the friendly name of the cert).
Type of Certificate Authority (CA): Select Self-signed (i.e., generate a self-signed certificate). (In some Azure Portal versions, you might directly have an option like “Self-signed certificate” to choose.)
You can leave other fields at defaults (Key Type RSA, Key Size 2048, etc., which are fine for a self-signed TLS cert).
Click Create to generate the certificate. The vault will create a new certificate and also generate an associated secret (with the same name) containing the certificate’s private key.



It may take a minute for the certificate to be created. Once the Certificate Operation shows as completed (Status: Issued), click on the new certificate (netpump-tls) in the Certificates list.
In the certificate’s detail page, find the Secret Identifier (a URL). This is typically labelled as Secret Identifier and looks like https://kv-netpump-install.vault.azure.net/secrets/netpump-tls/. Copy this Secret Identifier URL and paste it into your build sheet (label it “Cert URL”).
Validation: The Secret Identifier URL should start with your key vault’s URI and include netpump-tls and a version GUID. If you accidentally copy the Certificate Identifier or something else, the deployment will fail. Double-check you have the Secret Identifier (which typically differs by the segment /secrets/ in the URL). Common Pitfall: Copying the wrong URL. Ensure it’s the secret URL (for the private key), not just the certificate (public key) URL.
9. Create a Storage Account and an SMB File Share

[admin-page-smb]: images/admin-page-smb.png
Netpump uses an Azure Storage File Share (SMB share) as part of its data transfer pipeline (for staging data, etc.). We will create a storage account and a file share:

In Azure Portal, click + Create a resource and search for Storage account. Select Storage account and click Create.
In the Create a storage account form (Basics tab):

Subscription: Your subscription.
Resource group: Select rg-netpump-core (the core resource group).
Storage account name: stnetpumpdata (as an example - storage account names must be all lowercase, 3-24 characters, and globally unique. You can use your prefix, e.g., stdata. If stnetpumpdata is taken, choose a different variation).
Region: (should auto-fill with the resource group’s region, ensure it’s the same region as everything else).
Performance/Redundancy:

[001]: images/installer-gui/001.png
