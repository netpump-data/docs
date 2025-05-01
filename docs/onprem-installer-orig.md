# On Premise Netpump Services Installation Guide

This guide shows how to install the Netpump Service locally.

## Prerequisites

Download the current version of the Netpump Service Installer.

## Installing the Application

1. Open the downloaded installer file on your local machine.

2. Confirm the displayed End User License Agreement. This can also be viewed online at [Netpump EULA](Netpump-EULA.pdf).

    ![Accept End User License Agreement][accept-license]

3. Select components to install.

    ![Set Installation Components][install-option-components]

4. Select to Install as a service. 

    Select this option to have Netpump Service created as a Windows Service that starts automatically with machine boot.

    ![Install as a Service Option][install-option-service]

5. Select the installation directory.

    ![Set Installation Location][install-option-location]

6. Installation is now complete. 

    You may choose to start the service at this time and/or create a Desktop Shortcut.

    ![Installation Complete][install-complete]


## Starting the Application

If created as a service the Netpump Service will start automatically on machine boot. To start/stop manually load Service and search for NetpumpService.

![Netpump Service][app-service]

If not created as a service the Netpump Service can be started by clicking the Desktop Shortcut or executable 'netPump.Service.exe' file in the install directory.

## Accessing the Netpump Service Administrator Page

Once the service has started, double click the created Desktop Short or executable file 'netPump.Service,exe' in the install directory. This will activate the Netpump Service Administrator Page. See [Netpump Service Administrator Page](netpump-service-administrator-page.md) for more details.

[accept-license]: images/installer-onprem/onprem-100.png
[install-option-components]: images/installer-onprem/onprem-200.png
[install-option-service]: images/installer-onprem/onprem-300.png
[install-option-location]: images/installer-onprem/onprem-400.png
[install-complete]: images/installer-onprem/onprem-500.png
[app-loaded]: images/installer-onprem/onprem-500.png
[app-service]: images/installer-onprem/onprem-600.png