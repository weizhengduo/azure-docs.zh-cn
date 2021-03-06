---
title: 设置已注册 Azure Active Directory 的设备 | Microsoft Docs
description: 了解如何设置已注册 Azure Active Directory 的设备。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 0c38c0160cea51940ac5b04ee64095c6a6f25b5d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414668"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>设置已注册 Azure Active Directory 的 Windows 10 设备

使用 Azure Active Directory (Azure AD) 中的设备管理，可以确保用户从满足安全性和符合性标准的设备访问资源。 有关详细信息，请参阅 [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)。

若要启用“自带设备 (BYOD)”方案，可通过配置已注册 Azure AD 的设备实现此方案。 在 Azure AD 中，可配置适用于 Windows 10、iOS、Android 和 macOS 的已注册 Azure AD 的设备。 本文提供了适用于 Windows 10 设备的相关步骤。 


## <a name="before-you-begin"></a>开始之前

若要注册 Windows 10 设备，必须配置设备注册服务以允许注册设备。 此外，还必须使已注册的设备数少于已配置的最大设备数。 有关详细信息，请参阅[配置设备设置](../devices/device-management-azure-portal.md#configure-device-settings)。

## <a name="what-you-should-know"></a>要点

注册设备时，应记住以下要点：

- Windows 将组织目录中的设备在 Azure AD 中注册

- 可能需要完成多重身份验证质询。 该质询可由 IT 管理员配置。

- Azure AD 会检查设备是否需要移动设备管理注册，并且如果适用，则会注册设备。

- 如果是托管用户，Windows 会通过自动登录你将转到桌面。

- 如果是联合用户，则会转到 Windows 登录屏幕以输入凭据。


## <a name="registering-a-device"></a>注册设备

本部分介绍了在 Azure AD 中注册 Windows 10 设备的步骤。 将显示成功注册的设备并包含“工作或学校帐户”条目。

![注册](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**注册 Windows 10 设备：**

1. 在“开始”菜单中，单击“设置”。

    ![设置](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. 单击“帐户”。

    ![帐户](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. 单击“访问工作单位或学校”。

    ![访问工作单位或学校](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. 在“访问工作单位或学校”对话框中，单击“连接”。

    ![连接](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. 在“设置工作或学校帐户”对话框中，输入帐户名称（例如，someone@example.com），再单击“下一步”。

    ![连接](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. 在“输入密码”对话框中输入密码，然后单击“下一步”。

    ![连接](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. 在“已经完成全部设置”对话框中，单击“完成”。

    ![连接](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>验证

若要验证设备是否已加入 Azure AD，可查看设备上的“访问工作单位或学校”。

![注册](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

或者还可在 Azure AD 门户上查看设备设置。

![注册](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅 [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)

- 有关在 Azure AD 门户中管理设备的详细信息，请参阅[使用 Azure 门户管理设备](../device-management-azure-portal.md)。




