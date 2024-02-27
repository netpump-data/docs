# Netpump Service Administrator Page

This guide describes details of the Netpump Service Configuration page contained within the Netpump Service Virtual Machine and Local Instances.

The Netpump Service Configuration page is available if the following requirements are met, based on services hosting method.

## Cloud Hosted Service

1. Public IP Address assigned to the Virtual Machine instance created during the Netpump Service Marketplace Offer Configuration Guide.

2. DNS A Record pointing to the Public IP Address. The public URI that will be used to access the Netpump Service Configuration page.

3. The public URI added to the App Registration Authentication Redirect URIs. See Configure Redirect URLs and Token Authentication for Configuration Page for details.

## Locally Hosted Service

1. Updated hosts file containing reference to the local URI that will be used to access the Netpump Service Configuration page.

127.0.0.1   pathto.my.domain

2. The local URI added to the App Registration Authentication Redirect URIs. See Configure Redirect URLs and Token Authentication for Configuration Page for details.

Note: Ensure the attached Key Vault Certificate from linklinked includes the chosen public URI.