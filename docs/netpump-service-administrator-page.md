# Netpump Service Administrator Page

This guide describes details of the Netpump Service Configuration page contained within the Netpump Service Virtual Machine and Local Instances.

## Accessing the Administrator Page

The Netpump Service Configuration page is available if the following requirements are met, based on the services hosting environment.

### Cloud Hosted Service

1. Public IP Address assigned to the Virtual Machine instance created during the Netpump Service Marketplace Offer Configuration Guide.

2. DNS A Record pointing to the Public IP Address. The public URI that will be used to access the Netpump Service Configuration page.

3. The public URI added to the App Registration Authentication Redirect URIs. See [Configure Redirect URLs and Token Authentication for Configuration Page](setup-app-registration.md#configure-redirect-urls-and-token-authentication-for-configuration-page) for details.

### Locally Hosted Service

1. Updated hosts file containing reference to the local URI that will be used to access the Netpump Service Configuration page.

```bash
127.0.0.1   path.to.my.domain
```

2. The local URI added to the App Registration Authentication Redirect URIs. See [Configure Redirect URLs and Token Authentication for Configuration Page](setup-app-registration.md#configure-redirect-urls-and-token-authentication-for-configuration-page) for details.

**NOTE**:
Ensure the attached Key Vault Certificate from [Netpump Service Marketplace Offer Configuration Guide](setup-offer.md#prerequisites) prerequisites includes the chosen public URI.

Once the above settings are configured the page can be accessed via the configured public URI. Once loaded you will see a page similar to the image below.

![Netpump Service Configuration page preview][admin-page-preview]


## Administrator Page Settings

The Netpump Service Configuration page consists of 6 sections.

### Service Endpoint/Port Number Configuration

![Service Endpoint/Port Number Configuration][admin-page-uri]

Enter a valid URI host for the Netpump Service. Update this value to change the default port number and/or URI the service runs under. Please note for Cloud Hosted instances is should be a wildcard URI `http://*:443`. Locally Hosted instances should be a fully qualified URI `https://pathto.my.domain:443`.

The port number can be any valid port number provided it is allowed access to the Virtual Machine Instance (Cloud Hosted Instances) and it not currently in use by any local services (Locally Hosted instances).

### Azure Authentication

![Azure Authentication][admin-page-auth]

Enter the Azure Authentication credentials from the `App Registration` of the Netpump server set up in [Netpump Service App Registration Configuration Guide](setup-app-registration.md).

### Key Vault SSL Certificate

![Key Vault SSL Certificate][admin-page-ssl]

A link to the SSL Certificate within Azure Key Vault of the custom domain you wish to host the Netpump Server Application on. Link to  peer certificate(s) in the Azure Key Vault. These certificates will be used to authenticate to the peer service(s). The certificate(s) must be in the same Azure AD tenant as the application and the service principal must have access to the certificate(s).

### Server Message Block File Share(s)

![Server Message Block File share(s)][admin-page-smb]

Add/Remove SMB File Shares the service has access to. At least one SMB File Share must be configured. Enter valid UNC Path, Username and Password linking to the network SMB locations. The Netpump Service accesses the configured SMB File Shares for file transfer between instances.

### Netpump Service Log File(s)

![Netpump Service Log File(s)][admin-page-logs]

Click on displayed links to download service logs recorded by the Netpump Service. New logs are created on a daily basis named `service_[YYYYmmdd].log` and are displayed oldest to newest.

### Netpump Service Usage Data

![Netpump Service Usage Data][admin-page-usage]

Administrators are required as per their agreed EULA to provide usage logs upon request. The `` button will download the current usage logs linked to the running service.

### Restart Netpump Service

![Restart Netpump Service][admin-page-restart]

Administrators may manually restart the service via the Netpump Service Configuration page. This option is available if Netpump Server is running as a service.


[admin-page-preview]: images/admin-page-preview.png
[admin-page-uri]: images/admin-page-uri.png
[admin-page-auth]: images/admin-page-auth.png
[admin-page-ssl]: images/admin-page-ssl.png
[admin-page-smb]: images/admin-page-smb.png
[admin-page-logs]: images/admin-page-logs.png
[admin-page-usage]: images/admin-page-usage.png
[admin-page-restart]: images/admin-page-restart.png
