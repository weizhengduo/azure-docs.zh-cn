---
title: Azure Stack 中的客户计费和退款 | Microsoft Docs
description: 了解如何从 Azure Stack 中检索资源使用情况信息。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: f055837711b52fc32cb387fb86c623d3502f47ab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090133"
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack 中的使用情况和计费

此文介绍向 Azure Stack 用户计收资源使用费的方式。 您可以了解如何分析和费用回访问计费信息。

Azure Stack 收集，并使用的资源的使用情况数据进行分组。 然后 Azure Stack 将此数据转发到 Azure Commerce。 Azure Commerce 计收 Azure Stack 使用费的方式与计收 Azure 使用费相同。

此外可以获取使用情况数据和导出到您自己的计费或费用使用计费适配器备份系统或将其导出到 Microsoft Power BI 等的商业智能工具。


## <a name="usage-pipeline"></a>使用情况管道

在 Azure Stack 中的每个资源提供程序将发布每个资源使用情况的使用情况数据。 使用情况服务定期（每小时或每天）聚合使用情况数据并将其存储在使用情况数据库中。 Azure Stack 操作员和用户可以通过 Azure Stack 资源使用情况 API 来访问存储的使用情况数据。 

如果已[将 Azure Stack 实例注册到 Azure](azure-stack-register.md)，则 Azure Stack 会配置为将使用情况数据发送到 Azure Commerce。 将数据上传到 Azure 后，可以通过计费门户或使用 Azure 资源使用情况 API 访问该数据。 请参阅[用量数据报告](azure-stack-usage-reporting.md)文章，详细了解哪些使用情况数据报告到 Azure。  

下图显示了使用情况管道中的关键组件： 

![使用情况管道](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>可以找到哪些使用情况信息，如何查找？

Azure Stack 资源提供程序 （例如计算、 存储和网络） 生成使用情况数据以小时为间隔为每个订阅。 使用情况数据包含有关所用资源的信息，例如资源名称、所用订阅和所用数量。 若要了解计量 ID 资源，请参阅[使用情况 API 常见问题解答](azure-stack-usage-related-faq.md)一文。

在收集使用情况数据后，它将[报告给 Azure](azure-stack-usage-reporting.md)来生成帐单，可以通过 Azure 计费门户查看账单。 

> [!NOTE]  
> 对于 Azure Stack 开发工具包和在容量模型下许可的 Azure Stack 集成系统用户，不需要进行使用情况数据报告。 若要详细了解 Azure Stack 中的许可，请参阅[打包和定价](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)数据表。

Azure 计费门户显示应计费资源的使用情况数据。 除了应计费资源之外，Azure Stack 还会捕获更广范围内资源的使用情况数据，可以通过 REST API 或 PowerShell 在 Azure Stack 环境中访问这些数据。 Azure Stack 操作员可以获取所有用户订阅的使用情况数据。 单个用户只能获取自己的详细使用情况。 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>多租户云服务提供商的使用情况报告

有许多 Azure Stack 客户的多租户云服务提供商 (CSP) 可以单独报告每个客户的使用情况，以便向不同的 Azure 订阅收取使用费。 

每个客户将会获得一个按不同 Azure Active Directory (Azure AD) 租户表示的标识。 Azure Stack 支持向每个 Azure AD 租户分配一个 CSP 订阅。 可将租户及其订阅添加到基本 Azure Stack 注册。 将会针对所有 Azure Stack 订阅执行基本注册。 如果没有为租户注册订阅，用户仍可使用 Azure Stack，其使用情况数据将发送到用于基本注册的订阅。 


## <a name="next-steps"></a>后续步骤

[注册到 Azure Stack](azure-stack-registration.md)

[向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)

[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)

[有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)