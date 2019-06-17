---
# required metadata

title: Manage transferring data between iOS apps
titleSuffix: Microsoft Intune
description: Understand how to use mobile app management policies in Microsoft Intune to manage data transfers between apps.
keywords:
author: Erikre
ms.author: erikre
manager: dougeby
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: microsoft-intune
ms.localizationpriority: high
ms.technology:
ms.assetid: d10b2d64-8c72-4e9b-bd06-ab9d9486ba5e

# optional metadata

#ROBOTS:
#audience:
#ms.devlang:
ms.reviewer: aanavath
ms.suite: ems
search.appverid: MET150
#ms.tgt_pltfrm:
ms.custom: intune-azure

ms.collection: M365-identity-device-management
---

# How to manage data transfer between iOS apps in Microsoft Intune

To help protect company data, restrict file transfers to only the apps that you manage. You can manage iOS apps in the following ways:

-   Prevent company data loss by configuring an app protection policy for the apps, which we call **policy-managed** apps. See [all the Intune-managed apps you can manage with app protection policy](https://www.microsoft.com/cloud-platform/microsoft-intune-apps)

-   Deploy and manage apps through the **MDM channel**, which requires devices to enroll in a Mobile Device Management (MDM) solution. The apps you deploy can be **policy-managed** apps or other managed apps.

The **Open in management** feature for iOS devices can limit file transfers between apps that are deployed through the **MDM channel**. Set *Open in management* restrictions in configuration settings and then deploy them using your MDM solution.  When a user installs the deployed app, the restrictions you set are applied.

##  Use app protection with iOS apps
Use App protection policies with the iOS **Open in management** feature to protect company data in the following ways:

-   **Employee owned devices not managed by any MDM solution:** You can set the app protection policy settings to **Allow app to transfer data to only Policy Managed apps**. The *Open-In* behavior in a Policy Managed app presents only other Policy Managed apps as options for sharing. If a user tries to send a policy protected file as an attachment from OneDrive in the native mail app, that file is unreadable.

-   **Devices managed by Intune:** For devices enrolled in Intune, data transfer between apps with app protection policies and other managed iOS apps deployed through Intune is allowed automatically. To specify how you want to allow data transfer to other apps, enable **Allow app to transfer data to other apps** and then choose your preferred level of sharing. To specify how you want to allow an app to receive data from other apps, enable **Allow app to receive data from other apps** and then choose your preferred level of receiving data. You can use the **Open in management** feature to control data transfer between apps that are deployed through Intune. For more information about receiving and sharing app data, see [Data relocation settings](app-protection-policy-settings-ios.md#data-protection).   

-   **Devices managed by a third-party MDM solution:** You can restrict data transfer to only managed apps by using the iOS **Open in management** feature.
To make sure that apps you deploy using a third-party MDM solution are also associated with your Intune app protection policies, configure the user UPN setting as described in the following section, [Configure user UPN setting](#configure-user-upn-setting-for-microsoft-intune-or-third-party-emm). When apps deploy with the user UPN setting, the app protection policies apply to the app when the user signs-in using their work account.

## Configure user UPN setting for Microsoft Intune or third-party EMM
Configuring the user UPN setting is **required** for devices that are managed by Intune or a third-party EMM solution. The UPN configuration works with the app-protection policies you deploy from Intune. The following procedure is a general flow on how to configure the UPN setting and the resulting user experience:

1.  In the [Azure portal](https://portal.azure.com), [create and assign an app protection policy](app-protection-policies.md) for iOS. Configure policy settings per your company requirements and select the iOS apps that should have this policy.

2.  Deploy the apps and the email profile that you want managed through Intune or your third-party MDM solution using the following generalized steps. This experience is also covered by *Example 1*.

3.  Deploy the app with the following app configuration settings to the managed device:

      **key** = IntuneMAMUPN, **value** = <username@company.com>

      Example: [‘IntuneMAMUPN’, ‘jondoe@microsoft.com’]
      
       > [!NOTE]
       > In Intune, the App Configuration policy has to be for enrollment type "Managed Devices".
       > Additionally, the App needs to be either installed from the Intune Company Portal if set as available or pushed as required to the device. 

4.  Deploy the **Open in management** policy using Intune or your third-party MDM provider to enrolled devices.


### Example 1: Admin experience in Intune or third-party MDM console

1. Go to the admin console of Intune or your third-party MDM provider. Go to the section of the console in which you deploy application configuration settings to enrolled iOS devices.

2. In the Application Configuration section, enter the following setting:

   **key** = IntuneMAMUPN, **value** = <username@company.com>

   The exact syntax of the key/value pair may differ based on your third-party MDM provider. The following table shows examples of third-party MDM providers and the exact values you should enter for the key/value pair.

   |Third-party MDM provider| Configuration Key | Value Type | Configuration Value|
   | ------- | ---- | ---- | ---- |
   |Microsoft Intune| IntuneMAMUPN | String | {{UserPrincipalName}}|
   |VMware AirWatch| IntuneMAMUPN | String | {UserPrincipalName}|
   |MobileIron | IntuneMAMUPN | String | ${userUPN} **or** ${userEmailAddress} |
   |Citrix Endpoint Management | IntuneMAMUPN | String | ${user.userprincipalname} |
   |ManageEngine Mobile Device Manager | IntuneMAMUPN | String | %upn% |


### Example 2: End-user experience

1.  A user installs the Microsoft Word app on a device.

2.  The user launches the managed native email app to access their email.

3.  The user tries to open a document from native mail in Microsoft Word.

4.  When the Word app launches, the user is prompted to sign in with their work account. The account the user enters must match the account you specified in the app configuration settings for the Microsoft Word app.

    > [!NOTE]
    > The user can add and use their personal accounts with Word. App protection policies don't apply when the user uses Word outside of a work-context. 

5.  After sign-in, app protection policy settings apply to the Word app.

6.  Now the data transfer succeeds and the document is tagged with a corporate identity in the app.  The data is treated in a work context and  policy settings apply. 

### Validate user UPN setting for third-party EMM

After configuring the user UPN setting, validate the iOS app's ability to receive and comply to Intune app protection policy.

For example, the **Require app PIN** policy setting is easy to test. When the policy setting equals **Yes**, the user should see a prompt to set or enter a PIN before they can access company data.

First,  [create and assign an app protection policy](app-protection-policies.md) to the iOS app. For more information on how to test app protection policy, See [Validate app protection policies](app-protection-policies-validate.md).


### See also
[What is Intune app protection policy](app-protection-policy.md)
