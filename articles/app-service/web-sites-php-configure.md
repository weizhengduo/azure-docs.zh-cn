---
title: 在 Azure 应用服务 Web 应用中配置 PHP
description: 了解如何在 Azure 应用服务中为 Web 应用配置默认 PHP 安装或添加自定义 PHP 安装。
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 6e7de0a7b580c0028982895487117ab98d0cd612
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503445"
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>在 Azure 应用服务 Web 应用中配置 PHP

## <a name="introduction"></a>介绍

本指南演示如何执行以下操作：在 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)中配置 Web 应用的内置 PHP 运行时，提供自定义 PHP 运行时，并启用扩展。 若要使用应用服务，请注册[免费试用版]。 要充分利用本指南，应先在应用服务中创建一个 PHP Web 应用。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>如何：更改内置 PHP 版本

默认情况下，将安装 PHP 5.6 并且在创建应用服务 Web 应用时立即可用。 查看可用发行版、其默认配置以及已启用的扩展的最佳方式是部署一个调用 [phpinfo()] 函数的脚本。

PHP 7.0 和 PHP 7.2 也可用，但它们在默认情况下不启用。 若要更新 PHP 版本，请使用下列方法之一：

### <a name="azure-portal"></a>Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中浏览到相应的 Web 应用，并单击“设置”按钮。

    ![Web 应用设置][settings-button]
1. 在“设置”边栏选项卡中选择“应用程序设置”，并选择新的 PHP 版本。

    ![应用程序设置][application-settings]
1. 单击“Web 应用设置”边栏选项卡顶部的“保存”按钮。

    ![保存配置设置][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

1. 打开 Azure PowerShell 并登录到帐户：

        PS C:\> Connect-AzureRmAccount
1. 设置 Web 应用的 PHP 版本。

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. 现已设置 PHP 版本。 可以确认以下设置：

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli-20-linux-mac-windows"></a>Azure CLI 2.0（Linux、Mac、Windows）

若要使用 Azure 命令行界面，必须已在计算机上[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

1. 打开终端，并登录到帐户。

        az login

1. 选中查看受支持运行时的列表。

        az webapp list-runtimes | grep php

1. 设置 Web 应用的 PHP 版本。

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. 现已设置 PHP 版本。 可以确认以下设置：

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>如何：更改内置 PHP 配置

对于任何内置 PHP 运行时，都可以通过执行以下步骤更改任何配置选项。 （有关 php.ini 指令的信息，请参阅 [php.ini 指令的列表]。）

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>更改 PHP\_INI\_USER、PHP\_INI\_PERDIR、PHP\_INI\_ALL 配置设置

1. 将 [.user.ini] 文件添加到根目录。
1. 使用会在 `php.ini` 文件中使用的语法，将配置设置添加到 `.user.ini` 文件。 例如，如果希望启用 `display_errors` 设置，并将 `upload_max_filesize` 设置设为 10 分钟，则 `.user.ini` 文件应包含以下文本：

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. 部署 Web 应用。
1. 重新启动 Web 应用。 （需要进行重新启动，因为 PHP 读取 `.user.ini` 文件的频率受 `user_ini.cache_ttl` 设置的约束，该设置是一个系统级别设置且默认值为 300 秒（5 分钟）。 重新启动 Web 应用会强制 PHP 读取 `.user.ini` 文件中的新设置。）

作为使用 `.user.ini` 文件的替代方法，可以在脚本中使用 [ini_set()] 函数来设置不是系统级别指令的配置选项。

### <a name="changing-phpinisystem-configuration-settings"></a>更改 PHP\_INI\_SYSTEM 配置设置

1. 向 Web 应用添加一个键为 `PHP_INI_SCAN_DIR` 且值为 `d:\home\site\ini` 的应用设置
1. 使用 Kudu 控制器 (http://&lt;site-name&gt;.scm.azurewebsite.net) 在 `d:\home\site\ini` 目录中创建一个 `settings.ini` 文件。
1. 使用会在 `php.ini` 文件中使用的语法，将配置设置添加到 `settings.ini` 文件。 例如，如果希望将 `curl.cainfo` 设置指向 `*.crt` 文件并将“wincache.maxfilesize”设置为 512K，则 `settings.ini` 文件应包含以下文本：

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. 重启 Web 应用以重载更改。

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>如何：在默认 PHP 运行时中启用扩展

如上一部分所述，查看默认 PHP 版本、其默认配置以及已启用的扩展的最佳方式是部署一个调用 [phpinfo()] 的脚本。 若要启用其他扩展，请执行下列步骤：

### <a name="configure-via-ini-settings"></a>通过 ini 设置进行配置

1. 将 `ext` 目录添加到 `d:\home\site` 目录。
1. 将 `.dll` 扩展文件置于 `ext` 目录中（例如 `php_xdebug.dll`）。 确保扩展与默认版本的 PHP兼容，并且是 VC9 版本且与非线程安全 (nts) 兼容。
1. 向 Web 应用添加一个键为 `PHP_INI_SCAN_DIR` 且值为 `d:\home\site\ini` 的应用设置
1. 在 `d:\home\site\ini` 中创建名为 `extensions.ini` 的 `ini` 文件。
1. 使用会在 `php.ini` 文件中使用的语法，将配置设置添加到 `extensions.ini` 文件。 例如，如果想要启用 MongoDB 和 XDebug 扩展，则 `extensions.ini` 文件应包含以下文本：

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. 重新启动 Web 应用以加载更改。

### <a name="configure-via-app-setting"></a>通过应用设置进行配置

1. 将 `bin` 目录添加到根目录。
1. 将 `.dll` 扩展文件置于 `bin` 目录中（例如 `php_xdebug.dll`）。 确保扩展与默认版本的 PHP兼容，并且是 VC9 版本且与非线程安全 (nts) 兼容。
1. 部署 Web 应用。
1. 在 Azure 门户中浏览到相应的 Web 应用，并单击“设置”按钮。

    ![Web 应用设置][settings-button]
1. 在“设置”边栏选项卡中选择“应用程序设置”，并滚动到“应用设置”部分。
1. 在“应用设置”部分中，创建 **PHP_EXTENSIONS** 键。 此键的值将是相对于网站根目录的一个路径：**bin\your-ext-file**。

    ![启用应用程序设置中的扩展][php-extensions]
1. 单击“Web 应用设置”边栏选项卡顶部的“保存”按钮。

    ![保存配置设置][save-button]

通过使用 **PHP_ZENDEXTENSIONS** 键，还可以支持 Zend 扩展。 若要启用多个扩展，请包括应用设置值的 `.dll` 文件的逗号分隔列表。

## <a name="how-to-use-a-custom-php-runtime"></a>如何：使用自定义 PHP 运行时

应用服务 Web 应用可以使用提供的 PHP 运行时（而非默认 PHP 运行时）来执行 PHP 脚本。 提供的运行时可由提供的 `php.ini` 文件配置。 若要在 Web 应用中使用自定义 PHP 运行时，请执行下列步骤。

1. 获取非线程安全、VC9 或 VC11 兼容版本的 PHP for Windows。 可在此处找到 PHP for Windows 最新版本：[http://windows.php.net/download/]。 可在此处的存档中找到旧版本：[http://windows.php.net/downloads/releases/archives/]。
1. 修改运行时的 `php.ini` 文件。 Web 应用将忽略作为任何仅在系统级别使用的指令的配置设置。 （有关仅在系统级别使用的指令的信息，请参阅 [php.ini 指令的列表]）。
1. （可选）将扩展添加到 PHP 运行时并在 `php.ini` 文件中启用这些扩展。
1. 将 `bin` 目录添加到根目录，并将包含 PHP 运行时的目录置于该目录中（例如 `bin\php`）。
1. 部署 Web 应用。
1. 在 Azure 门户中浏览到相应的 Web 应用，并单击“设置”按钮。

    ![Web 应用设置][settings-button]
1. 从“设置”边栏选项卡选择“应用程序设置”并滚动到“处理程序映射”部分。 将 `*.php` 添加到扩展字段，并将路径添加到 `php-cgi.exe` 可执行文件。 如果将 PHP 运行时放在应用程序根目录中的 `bin` 目录下，路径将为 `D:\home\site\wwwroot\bin\php\php-cgi.exe`。

    ![指定处理程序映射中的处理程序][handler-mappings]
1. 单击“Web 应用设置”边栏选项卡顶部的“保存”按钮。

    ![保存配置设置][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>如何：在 Azure 中启用编辑器自动化

默认情况下，如果 PHP 项目中有 composer.json，则应用服务与其不相关。 如果使用 [Git 部署](app-service-deploy-local-git.md)，则可以通过在 `git push` 期间启用编辑器扩展来启用 composer.json 处理。

> [!NOTE]
> 可以[在此处针对应用服务中的一流编辑器支持进行投票](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)！
>

1. 在 [Azure 门户](https://portal.azure.com)中，在 PHP Web 应用的边栏选项卡中，单击“工具” > “扩展”。

    ![Azure 门户设置边栏选项卡，用于在 Azure 中启用编辑器自动化](./media/web-sites-php-configure/composer-extension-settings.png)
2. 单击“添加”，并单击“编辑器”。

    ![添加编辑器扩展，以在 Azure 中启用编辑器自动化](./media/web-sites-php-configure/composer-extension-add.png)
3. 单击“确定”以接受法律条款。 再次单击“确定”以添加扩展。

    “已安装扩展”边栏选项卡将显示编辑器扩展。
    ![接受法律条款以在 Azure 中启用编辑器自动化](./media/web-sites-php-configure/composer-extension-view.png)
4. 现在，在本地计算机上的终端窗口中，对 Web 应用执行 `git add`、`git commit` 和 `git push`。 请注意，编辑器正在安装在 composer.json 中定义的依赖项。

    ![在 Azure 中使用编辑器自动化的 Git 部署](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [PHP 开发人员中心](/develop/php/)。

> [!NOTE]
> 如果要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
>

[免费试用版]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 指令的列表]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
