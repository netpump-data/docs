# Netpump Data for Azure — Installation

## Guide

This guide provides a foolproof, step-by-step process to deploy Netpump Data on Azure. It is written for junior cloud administrators with limited Azure experience, so each step is ultra-explicit.

Follow the steps in order and use a “build sheet” (e.g. a local text file or spreadsheet) to record important values (IDs, secrets, URLs) as you go. Browser-specific tips, common pitfalls, and validation checks are included to ensure a smooth setup.

## Contents

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

```json
{
  "allowedMemberTypes": ["User", "Application"],
  "description": "Administers Netpump servers",
  "displayName": "ServerAdmin",
  "id": "",
  "isEnabled": true,
  "value": "ServerAdmin"
}

