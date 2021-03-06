---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cfe675ca269a69c7c2bfa67638acd0afbcd1c8ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371280"
---
每个终结点都拥有*公用端口*和*专用端口*：

* Azure 负载均衡器使用公用端口侦听从 Internet 传入的虚拟机流量。
* 虚拟机使用专用端口侦听传入流量（通常发送到虚拟机上运行的应用程序或服务）。

使用 Azure 门户创建终结点时，将为 IP 协议和众所周知的网络协议的 TCP 或 UDP 端口提供默认值。 对于自定义终结点，需要指定正确的 IP 协议（TCP 或 UDP），以及公用和专用端口。 要将传入流量随机分布到多个虚拟机，需要创建包含多个终结点的负载均衡集。

创建终结点后，可以使用访问控制列表 (ACL) 定义规则，根据传入流量的源 IP 地址允许或拒绝终结点的公用端口的传入流量。 但是，如果虚拟机位于 Azure 虚拟网络中，则应改为使用网络安全组。 有关详细信息，请参阅[关于网络安全组](../articles/virtual-network/security-overview.md)。

> [!NOTE]
> 将对与 Azure 自动设置的远程连接终结点关联的端口自动完成 Azure 虚拟机的防火墙配置。 对于为所有其他终结点指定的端口，不会自动对虚拟机防火墙进行任何配置。 为虚拟机创建终结点时，需要确保虚拟机的防火墙也允许与终结点配置对应的协议和专用端口的流量。 若要配置防火墙，请参阅有关在虚拟机上运行的操作系统的文档或联机帮助。
>
>

## <a name="create-an-endpoint"></a>创建终结点
1. 如果尚未登录 [Azure 门户](https://portal.azure.com)，请先登录。
2. 单击“虚拟机”，并单击要配置的虚拟机的名称。
3. 在“设置”组中，单击“终结点”。 “终结点”页面列出虚拟机的所有当前终结点。 （此示例中的是 Windows VM。 如果是 Linux VM，则默认显示一个 SSH 终结点。）

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![终结点](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. 在终结点条目上方的命令栏中，单击“添加”。
5. 在“添加终结点”页面的“名称”中，键入终结点的名称。
6. 在“协议”中，选择“TCP”或“UDP”。
7. 在“公用端口”中，键入来自 Internet 的传入流量的端口号。 在“专用端口”中，键入虚拟机正在侦听的端口号。 这些端口号可以不同。 确保已将虚拟机的防火墙配置为允许与协议（在步骤 6 中）和专用端口对应的流量。
10. 单击“确定” 。

新终结点会在“终结点”页面上列出。

![成功创建终结点](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>管理终结点上的 ACL
若要定义一组可以发送流量的计算机，终结点上的 ACL 可以基于源 IP 地址限制流量。 按照下列步骤，在终结点上添加、修改或删除 ACL。

> [!NOTE]
> 如果终结点是负载均衡集的一部分，则会将对终结点上 ACL 作出的任何更改应用到该集中的所有终结点。
>
>

如果虚拟机位于 Azure 虚拟网络中，则建议使用网络安全组（而不是 ACL）。 有关详细信息，请参阅[关于网络安全组](../articles/virtual-network/security-overview.md)。

1. 如果尚未登录 Azure 门户，请先登录。
2. 单击“虚拟机”，并单击要配置的虚拟机的名称。
3. 单击“终结点” 。 从列表中选择适当的终结点。 ACL 列表位于页面底部。

   ![指定 ACL 详细信息](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. 使用列表中的行为 ACL 添加、删除或编辑规则，并更改其顺序。 **远程子网**值是从 Internet 传入流量的 IP 地址范围，Azure 负载均衡器将使用该值根据流量的源 IP 地址允许或拒绝传入流量。 请务必以 CIDR 格式（也称为地址前缀格式）指定 IP 地址范围。 例如 `10.1.0.0/8`。

 ![新的 ACL 条目](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


可以使用规则只允许来自与 Internet 上计算机对应的特定计算机的流量，或拒绝来自特定已知地址范围的流量。

按照从第一个规则开始并以最后一个规则结束的顺序评估规则。 这意味着规则应按最少限制到最多限制排序。 有关示例和详细信息，请参阅[什么是网络访问控制列表](../articles/virtual-network/virtual-networks-acl.md)。
