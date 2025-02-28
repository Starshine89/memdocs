---
title: Windows Autopilot device preparation known issues
description: Information regarding known issues that might occur during a Windows Autopilot device preparation deployment. # RSS subscription is based on this description so don't change. If the description needs to change, update RSS URL in the Tip in the article.
ms.service: windows-client
ms.subservice: itpro-deploy
ms.localizationpriority: medium
author: frankroj
ms.author: frankroj
ms.reviewer: jubaptis
manager: aaroncz
ms.date: 07/08/2024
ms.collection:
  - M365-modern-desktop
  - highpri
  - tier2
ms.topic: troubleshooting
appliesto:
  - ✅ <a href="https://learn.microsoft.com/windows/release-health/supported-versions-windows-client" target="_blank">Windows 11</a>
---

# Windows Autopilot device preparation - known issues

This article describes known issues that can often be resolved with:

- Configuration changes.
- Cumulative updates.
- Might be resolved automatically in a future release.

> [!TIP]
>
> RSS can be used to notify when new known issues are added to this page. For example, the following RSS link includes this article:
>
> ``` url
> https://learn.microsoft.com/api/search/rss?search=%22Information+regarding+known+issues+that+might+occur+during+a+Windows+Autopilot+device+preparation%22&locale=en-us&%24filter=
> ```
>
> This example includes the `&locale=en-us` variable. The `locale` variable is required, but it can be changed to another supported locale. For example, `&locale=es-es`.
>
> For more information on using RSS for notifications, see [How to use the docs](/mem/use-docs#notifications) in the Intune documentation.

## Known issues

## Deployment fails for devices not in the Coordinated Universal Time (UTC) time zone

Date added: *July 8, 2024*

Autopilot device preparation deployments fail when devices aren't in the UTC time zone. The issue is being investigated.

As a workaround, customers can manually set the time zone in OOBE via Windows PowerShell until the issue is resolved:

```powershell
Set-TimeZone -Id "UTC"
```

## BitLocker encryption defaults to 128-bit when 256-bit encryption is configured

Date added: *July 8, 2024*

In some Windows Autopilot device preparation deployments, BitLocker encryption may default to 128-bit even though the admin configured 256-bit encryption due to a known race condition. The issue is being investigated. Microsoft recommends that customers who need 256-bit BitLocker encryption wait for the issue to be resolved before trying to use Windows Autopilot device preparation.

## Windows Autopilot device preparation policy shows 0 groups assigned

Date added: *June 18, 2024*

There's a known issue that the Windows Autopilot device preparation policy shows **0 groups assigned** even when:

- An assigned device security group was properly added to the policy.
- The **Intune Provisioning Client** service principal with AppID of **f1346770-5b25-470b-88bd-d5744ab7952c** is the owner of the device security group specified in the policy.

The issue is being investigated. As a workaround, create a new assigned device security group with the **Intune Provisioning Client** service principal with AppID of **f1346770-5b25-470b-88bd-d5744ab7952c** as the owner, and then assign the new device group to the Windows Autopilot device preparation policy. For more information on creating the assigned device group, see [Create a device group](tutorial/user-driven/entra-join-device-group.md#create-a-device-group).

## Unable to assign Windows Autopilot device preparation policy to user group

Date added: *June 18, 2024*

There's a known issue where an administrator might not be able to assign the Windows Autopilot device preparation policy to a user group. When the issue occurs, the following error might occur:

> **Unable to save group assignment for <policy_name>. You do not have permission to save these assignments.**

The issue is being investigated. As a workaround, add the following additional role-based access control (RBAC) permission for the Windows Autopilot device preparation administrator role:

- **Device configurations**
  - Assign

For more information, see [Required RBAC permissions](requirements.md?tabs=rbac#required-rbac-permissions).

> [!NOTE]
>
> The [Required RBAC permissions](requirements.md?tabs=rbac#required-rbac-permissions) article doesn't list the **Device configurations** - **Assign** permission. The requirement of this permission is only temporary until the issue is resolved. However, the article can be used as a guide on how to properly add this permission.

### Device is stuck at 100% during the out-of-box experience (OOBE)

Date added: *June 3, 2024*

If during Windows Autopilot device preparation deployment a device gets stuck at 100% during the out-of-box experience (OOBE), the end-user needs to manually restart the device for the deployment to continue. This issue is a known issue and a fix is being worked on.

### Object with AppID of f1346770-5b25-470b-88bd-d5744ab7952c displays as Intune Autopilot ConfidentialClient

Date added: *June 3, 2024*

In some tenants, when trying to set the owner of the device group used in the Windows Autopilot device preparation policy, the service principal with AppID of **f1346770-5b25-470b-88bd-d5744ab7952c** displays as **Intune Autopilot ConfidentialClient** instead of **Intune Provisioning Client**. As long as the service principal has an AppID of **f1346770-5b25-470b-88bd-d5744ab7952c**, it's the correct service principal and can be selected.

### Conflict between Microsoft Entra ID and Windows Autopilot device preparation local administrator setting

Date added: *June 3, 2024*

There's a compatibility problem between the Windows Autopilot device preparation policy **User account type** setting and the Microsoft Entra ID **Local administrator settings**. Specifically, when the Windows Autopilot device preparation policy **User account type** setting is set to **Standard user** and the Microsoft Entra ID setting **Registering user is added as local administrator on the device during Microsoft Entra join (Preview)** under **Local administrator settings** is set to either **Selected** or **None**, provisioning gets skipped during a Windows Autopilot device preparation deployment. This settings conflict leads to a scenario where users could reach the desktop without having the expected applications installed. The Microsoft Entra ID **Local administrator settings** can be found by signing into the [Azure portal](https://portal.azure.com/) and navigating to **Microsoft Entra ID** > **Manage | Devices** > **Manage | Devices settings**.

Until the issue is fixed, for users to be standard non-administrators on their device, make sure that the settings are set to one of the following three setting combinations:

- The Microsoft Entra ID **Local administrator settings** is set to **None**.
- The Windows Autopilot device preparation policy **User account type** setting is set to **Administrator**.

- The Microsoft Entra ID **Local administrator settings** is set to **Selected** and the standard non-administrator users aren't selected.
- The Windows Autopilot device preparation policy **User account type** setting is set to **Administrator**.

- The Microsoft Entra ID **Local administrator settings** is set to **All**.
- The Windows Autopilot device preparation policy **User account type** is set to **Standard user**.

In all three cases, the end result is that the user is a standard non-administrative user on the device.

If the intention is for the user to be a local administrator user on the device, make sure that the settings are set to one of the following two setting combinations:

- The Microsoft Entra ID **Local administrator settings** is set to **All**.
- The Windows Autopilot device preparation policy **User account type** setting is set to **Administrator**.

- The Microsoft Entra ID **Local administrator settings** is set to **Selected** and the administrator users are selected.
- The Windows Autopilot device preparation policy **User account type** setting is set to **Administrator**.

### Initial release of Windows Autopilot device preparation

Date added: *June 3, 2024*

The initial release of Windows Autopilot device preparation has the following known issues and limitations:

- Dependency and supersedence relationships are marked in reports as **Dependent**.
- Application uninstall intent is marked in reports as **Installed** if completed successfully.
- Managed Installer policy during the out-of-box experience (OOBE) isn't supported due to the possibility of incorrect reporting.
- Custom compliance isn't supported during Windows Autopilot device preparation deployments.
- The device health script isn't supported during Windows Autopilot device preparation deployments.
