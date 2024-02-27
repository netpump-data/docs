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
127.0.0.1   pathto.my.domain
```

2. The local URI added to the App Registration Authentication Redirect URIs. See [Configure Redirect URLs and Token Authentication for Configuration Page](setup-app-registration.md#configure-redirect-urls-and-token-authentication-for-configuration-page) for details.

Note: Ensure the attached Key Vault Certificate from [Netpump Service Marketplace Offer Configuration Guide](setup-offer.md#prerequisites) prerequisites includes the chosen public URI.

## Administrator Page Settings

The Netpump Service Configuration page consists of 6 sections.

### Service Endpoint/Port Number Configuration

### Azure Authentication

### Key Vault SSL Certificate

### Server Message Block File share(s)

### Netpump Service Log File(s)

### Netpump Service Usage Data

### Restart Netpump Service
