---
title: Plan for application management
titleSuffix: Configuration Manager
description: Implement and configure the necessary dependencies for deploying applications in Configuration Manager.
ms.date: 11/29/2019
ms.prod: configuration-manager
ms.technology: configmgr-app
ms.topic: conceptual
ms.assetid: 2be84a1d-ebb9-47ae-8982-c66d5b92a52a
author: aczechowski
ms.author: aaroncz
manager: dougeby


---

# Plan for and configure application management in Configuration Manager

*Applies to: Configuration Manager (current branch)*

Use the information in this article to help you implement the necessary dependencies to deploy applications in Configuration Manager.  



## Dependencies external to Configuration Manager  


### Internet Information Services (IIS)

IIS is required on the servers that run the following site system roles:

- Management point  
- Distribution point  

For more information, see [Site and site system prerequisites](../../core/plan-design/configs/site-and-site-system-prerequisites.md).  

> [!Note]  
> The application catalog also requires IIS. However, its Silverlight user experience isn't supported as of current branch version 1806. Starting in version 1906, updated clients automatically use the management point for user-available application deployments. You also can't install new application catalog roles. Support ends for the application catalog roles with version 1910.  
>
> For more information, see the following articles:
>
> - [Configure Software Center](plan-for-software-center.md#bkmk_userex)
> - [Removed and deprecated features](../../core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures.md)  


### Certificates on code-signed applications for mobile devices

When you code-sign applications to deploy them to mobile devices, don't use a certificate that was generated by using a Version 3 template (**Windows Server 2008, Enterprise Edition**). This certificate template creates a certificate that's incompatible with Configuration Manager applications for mobile devices.

If you use Active Directory Certificate Services to code-sign applications for mobile device applications, don't use a Version 3 certificate template.


### Audit sign-in events for user device affinity  

If you want to automatically create user device affinities, configure clients to audit sign-in events.

To determine automatic user device affinities, the Configuration Manager client reads sign-in events of type **Success** from the Windows security event log. Enable these events with the following two audit policies:

- **Audit account logon events**
- **Audit logon events**

To automatically create relationships between users and devices, make sure that these two settings are enabled on client computers. You can use Windows Group Policy to configure these settings.

For more information on user device affinity, see [Link users and devices with user device affinity](../deploy-use/link-users-and-devices-with-user-device-affinity.md).  



## Configuration Manager dependencies


### Management point

Clients contact a management point to download client policy, to locate content.

Starting in version 1906, updated clients automatically use the management point for user-available application deployments.

In version 1902 and earlier, clients use the management point to connect to the application catalog. If clients can't access a management point, they can't use the application catalog.

> [!Note]  
> Starting in version 1806, application catalog roles are no longer required to display user-available applications in Software Center. For more information, see [Configure Software Center](plan-for-software-center.md#bkmk_userex).<!--1358309-->  
>
> Starting in version 1906, you can't install new application catalog roles. Support ends for the application catalog roles with version 1910.  
  

### Distribution point

Before you can deploy applications to clients, you need at least one distribution point in the hierarchy. By default, the site server has a distribution point site role enabled during a standard installation. The number and location of distribution points vary according to the specific requirements of your environment.

For more information about how to install distribution points and manage content, see [Manage content and content infrastructure](../../core/servers/deploy/configure/manage-content-and-content-infrastructure.md).  


### Reporting services point

To use the reports in Configuration Manager for application management, first install and configure a reporting services point.

For more information, see [Introduction to reporting](../../core/servers/manage/introduction-to-reporting.md).  


### Client settings

Many client settings control how the client installs applications and the user experience on the device. These client settings include the following groups:

- Computer agent  
- Computer restart  
- Software Center  
- Software deployment  
- User and device affinity  

For more information, see the following articles:

- [About client settings](../../core/clients/deploy/about-client-settings.md)  
- [How to configure client settings](../../core/clients/deploy/configure-client-settings.md)  


### Security permissions for application management

- The **Application Author** security role includes the required permissions to create, change, and retire applications.  

- The **Application Deployment Manager** security role includes required permissions to deploy applications.  

- The **Application Administrator** security role has all the permissions from both the **Application Author** and the **Application Deployment Manager** security roles.  

For more information, see [Configure role-based administration](../../core/servers/deploy/configure/configure-role-based-administration.md).  


### App-V 4.6 SP1 or later client to run virtual applications

To create virtual applications in Configuration Manager, install App-V 4.6 SP1 or later on devices.

Before you deploy virtual applications, also update the App-V client with the hotfix described in the [Microsoft Support article 2645225](https://support.microsoft.com/help/2645225).  


### Application catalog

> [!Important]  
> Support ends for the application catalog roles with version 1910.. For more information, see [Remove the application catalog](#bkmk_remove-appcat).  

#### Application catalog web service point

The application catalog web service point is a site system role that provides information about available software from your software library to the application catalog website that users access.

For more information about how to configure this site system role, see [Install and configure the Application Catalog](#bkmk_appcat).  

#### Application catalog website point

The application catalog website point is a site system role that provides users with a list of available software.

For more information about how to configure this site system role, see [Install and configure the Application Catalog](#bkmk_appcat).

#### Discovered user accounts for application catalog

Configuration Manager must first discover user accounts before users can view and request applications from the application catalog. For more information, see [Run discovery](../../core/servers/deploy/configure/run-discovery.md).  



## <a name="bkmk_userex"></a> Configure Software Center  

For more information on configuring and branding Software Center, see [Plan for Software Center](plan-for-software-center.md).


## <a name="bkmk_remove-appcat"></a> Remove the application catalog

<!-- SCCMDocs-pr issue 3051 -->

Support ends for the application catalog roles with version 1910. For more information, see [Removed and deprecated features](../../core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures.md). The following list summarizes the changes:

- Starting in version 1806, the **Silverlight user experience** for the application catalog website point is no longer supported.<!--1358309--> The application catalog web service point role is no longer *required*, but still *supported*.

- Starting in version 1906, updated clients automatically use the management point for user-available application deployments. You also can't install new application catalog roles.

- Support ends for the application catalog roles with version 1910.  

These iterative improvements to Software Center and the management point are to simplify your infrastructure and remove the need for the application catalog for user-available deployments. Software Center can deliver all app deployments without the application catalog. Also, if you enable TLS 1.2 and use HTTP with the application catalog, users can't see user-targeted, available deployments. Update Configuration Manager to version 1906 or later to benefit from these improvements.

1. Update all clients to version 1806 or later. Version 1906 is recommended.  

1. Set branding for Software Center, instead of in the properties of the application catalog web site role. For more information, see [Software Center client settings](../../core/clients/deploy/about-client-settings.md#software-center).  

1. Review the default and any custom client settings. In the **Computer Agent** group, make sure the **Default Application Catalog website point** is `(none)`.  

    In version 1902 and earlier, the client only switches to using the management point when there are no application catalog roles in the hierarchy. Otherwise, clients continue to use one of the application catalog instances in the hierarchy. This behavior applies across separate primary sites.  

1. Remove the **application catalog website** and **application catalog web service** site system roles from all primary sites.

After you remove the application catalog roles, Software Center starts using the management point for user-targeted, available deployments. In version 1902 and earlier, it can take up to 65 minutes for this change to happen. To verify this behavior on a specific client, review the `SCClient_<username>.log`, and look for an entry similar to the following line:

`Using endpoint Url: https://mp.contoso.com/CMUserService_WindowsAuth, Windows authentication`


## <a name="bkmk_appcat"></a> Install and configure the application catalog  

> [!Important]  
> Support ends for the application catalog roles with version 1910. For more information, see [Remove the application catalog](#bkmk_remove-appcat).  

### Step 1: Web server certificate for HTTPS

If you use HTTPS connections, deploy a web server certificate to the site system servers for the application catalog website point and the application catalog web service point.

If you want clients to use the application catalog from the internet, deploy a web server certificate to at least one management point. Configure it for client connections from the internet.

For more information about certificate requirements, see [PKI certificate requirements](../../core/plan-design/network/pki-certificate-requirements.md).  


### Step 2: Client authentication certificate for HTTPS

If you use a client PKI certificate for connections to management points, deploy a client authentication certificate to client computers. Although clients don't use a client PKI certificate to connect to the application catalog, they must connect to a management point before they can use the application catalog.

Deploy a client authentication certificate to client computers in the following scenarios:

- All management points on the intranet accept only HTTPS client connections.
- Clients connect to the application catalog from the internet.

For more information about certificate requirements, see [PKI certificate requirements](../../core/plan-design/network/pki-certificate-requirements.md).  


### Step 3: Install and configure the application catalog roles

Install both the application catalog web service point and the application catalog website roles in the same site. You don't have to install them on the same server or in the same Active Directory forest. However, the application catalog web service point must be in the same forest as the site database.

For more information about server placement, see [Plan for site system servers and site system roles](../../core/plan-design/hierarchy/plan-for-site-system-servers-and-site-system-roles.md).

> [!NOTE]  
> Install the application catalog at a primary site. You can't install it at a secondary site or the central administration site.  

Install the application catalog on a new site system server or an existing server in the site. For more information on the general procedure, see [Install site system roles](../../core/servers/deploy/configure/install-site-system-roles.md). In the wizard to add a site system role or create a site system server, select the following roles from the list:  

- **Application catalog web service point**  
- **Application catalog website point**  

> [!TIP]  
> If you want client computers to use the application catalog over the internet, specify the internet fully qualified domain name (FQDN).  

#### Verify the installation of these site system roles  

- Status messages: Use the components **SMS_PORTALWEB_CONTROL_MANAGER** and **SMS_AWEBSVC_CONTROL_MANAGER**.  

    For example, status ID **1015** for **SMS_PORTALWEB_CONTROL_MANAGER** confirms that Site Component Manager successfully installed the application catalog website point.  

- Log files: Search for **SMSAWEBSVCSetup.log** and **SMSPORTALWEBSetup.log**.  

    For more information, search for the **awebsvcMSI.log** and **portlwebMSI.log** log files.  


### Step 4: Configure client settings

If you want all users to have the same settings, configure the default client settings. Otherwise, configure custom client settings for specific collections.

For more information, see the following articles:

- [About client settings](../../core/clients/deploy/about-client-settings.md)  
    - Computer agent  
    - Computer restart  
    - Software Center  
    - Software deployment  
    - User and device affinity  
- [How to configure client settings](../../core/clients/deploy/configure-client-settings.md)  

The Configuration Manager client configures devices with these settings when it next downloads client policy. To trigger policy retrieval for a single client, see [How to manage clients](../../core/clients/manage/manage-clients.md).


### Step 5: Verify that the application catalog is operational

Use the following procedures to verify that the application catalog is operational.

> [!NOTE]  
> The application catalog user experience requires Microsoft Silverlight. If you use the application catalog directly from a browser, first verify that Microsoft Silverlight is installed on the computer.  

> [!TIP]  
> Missing prerequisites are among the most typical reasons for the application catalog to operate incorrectly after installation. Confirm the role prerequisites for the application catalog site system roles. For more information, see [Site and site system prerequisites](../../core/plan-design/configs/site-and-site-system-prerequisites.md).  

In a browser, enter the address of the application catalog website. Confirm that the web page shows the three tabs: **Application Catalog**, **My Application Requests**, and **My Devices**.  

Use the appropriate address for the application catalog from the following list, where `<server>` is the computer name, intranet FQDN, or internet FQDN:  

- HTTPS client connections and default site system role settings: `https://<server>/CMApplicationCatalog`  

- HTTP client connections and default site system role settings: `http://<server>/CMApplicationCatalog`  

- HTTPS client connections and custom site system role settings: `https://<server>:<port>/<web application name>`  

- HTTP client connections and custom site system role settings: `http://<server>:<port>/<web application name>`  

> [!NOTE]  
> If you signed in to the device with a Domain Administrator account, the Configuration Manager client doesn't display notification messages. For example, messages indicating that new software is available.  