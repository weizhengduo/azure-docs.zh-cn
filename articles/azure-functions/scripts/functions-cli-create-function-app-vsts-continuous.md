---
title: 在 Azure 中创建从 Visual Studio Team Services 部署的函数 | Microsoft Docs
description: 创建 Function App 并从 Visual Studio Team Services 部署函数代码
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988946"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>创建函数应用，然后从 Visual Studio Team Services 部署函数代码

本主题演示如何使用 Azure Functions 创建使用[消耗计划](../functions-scale.md#consumption-plan)的[无服务器](https://azure.microsoft.com/overview/serverless-computing/)函数应用。 从 Visual Studio Team Services (VSTS) 存储库连续部署函数应用（即函数的容器）。 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

若要完成本主题，必须具备以下条件：

* 包含函数应用项目的 VSTS 存储库，并且你对其具有管理权限。
* [个人访问令牌 (PAT)](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)，用于访问 VSTS 存储库。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果想要在本地使用 Azure CLI，必须安装和使用版本 2.0 或更高版本。 若要确定 Azure CLI 版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app 并从 Visual Studio Team Services 部署函数代码。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、存储帐户、函数应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | 创建函数应用所需的存储帐户。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | 在无服务器[消耗计划](../functions-scale.md#consumption-plan)中创建函数应用。 |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | 将 Function App 与 Git 或 Mercurial 存储库相关联。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
