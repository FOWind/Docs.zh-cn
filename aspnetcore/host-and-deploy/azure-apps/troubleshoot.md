---
title: "解决在 Azure App Service 上的 ASP.NET 核心"
author: guardrex
description: "了解如何诊断 ASP.NET Core Azure 应用服务部署问题。"
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 144af8e93bb935d07fd064d5f45b40faea4a2664
ms.sourcegitcommit: 7a87d66cf1d01febe6635c7306f2f679434901d1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>解决在 Azure App Service 上的 ASP.NET 核心

作者：[Luke Latham](https://github.com/guardrex)

本文说明了如何诊断 ASP.NET Core 应用使用 Azure App Service 的诊断工具的启动问题。 有关其他故障排除建议，请参阅[Azure App Service 诊断概述](/azure/app-service/app-service-diagnostics)和[如何： 在 Azure App Service 中监视应用](/azure/app-service/web-sites-monitor)Azure 文档中。

## <a name="app-startup-errors"></a>应用程序启动错误

**502.5 进程故障**  
工作进程将失败。 不启动应用程序。

[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)尝试启动工作进程，但它无法启动。 经常检查应用程序事件日志可帮助解决这种类型的问题。 访问日志中进行了说明[应用程序事件日志](#application-event-log)部分。

*502.5 进程失败*配置错误的应用程序会导致工作进程失败，返回错误页：

![显示 502.5 进程失败页的浏览器窗口](troubleshoot/_static/process-failure-page.png)

**500 内部服务器错误**  
启动该应用程序，但某个错误阻止在完成请求的服务器。

启动过程中或创建响应时，应用程序的代码中出现此错误。 响应可能包含任何内容，或响应可能显示为*500 内部服务器错误*浏览器中。 应用程序事件日志通常表明应用程序正常启动。 从服务器的角度来看，这是正确的。 应用程序未启动，但它无法生成有效的响应。 [Kudu 控制台中运行此应用](#run-the-app-in-the-kudu-console)或[启用 ASP.NET 核心模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。

## <a name="troubleshoot-app-startup-errors"></a>应用程序启动错误疑难解答

### <a name="application-event-log"></a>应用程序事件日志

若要访问应用程序事件日志，请使用**诊断并解决问题**在 Azure 门户中的边栏选项卡：

1. 在 Azure 门户中，打开应用的边栏选项卡中**应用程序服务**边栏选项卡。
1. 选择**诊断并解决问题**边栏选项卡。
1. 下**选择问题类别**，选择**Web 应用程序向下**按钮。
1. 下**建议解决方案**，打开的窗格**打开应用程序事件日志**。 选择**打开应用程序事件日志**按钮。
1. 检查提供的最新错误*IIS AspNetCoreModule*中**源**列。

除了使用**诊断并解决问题**边栏选项卡是检查应用程序事件日志文件直接使用[Kudu](https://github.com/projectkudu/kudu/wiki):

1. 选择**高级工具**中的边栏选项卡**开发工具**区域。 选择**转&rarr;**按钮。 Kudu 控制台打开新浏览器选项卡或窗口中。
1. 使用的页上，顶部导航栏打开**调试控制台**和选择**CMD**。
1. 打开**LogFiles**文件夹。
1. 选择铅笔图标旁边*eventlog.xml*文件。
1. 检查日志。 滚动到底部的日志，以查看最新的事件。

### <a name="run-the-app-in-the-kudu-console"></a>Kudu 控制台中运行此应用

许多启动错误未生成应用程序事件日志中的有用信息。 你可以在运行应用程序[Kudu](https://github.com/projectkudu/kudu/wiki)远程执行控制台，以发现错误：

1. 选择**高级工具**中的边栏选项卡**开发工具**区域。 选择**转&rarr;**按钮。 Kudu 控制台打开新浏览器选项卡或窗口中。
1. 使用的页上，顶部导航栏打开**调试控制台**和选择**CMD**。
1. 打开的文件夹的路径**站点** > **wwwroot**。
1. 在控制台中，通过执行应用程序的程序集与运行应用*dotnet.exe*。 在下面的命令中，应用程序的程序集的名称替换`<assembly_name>`:
   ```console
   dotnet .\<assembly_name>.dll
   ```
1. 控制台应用程序中，显示任何错误，从输出传送到 Kudu 控制台。

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET 核心模块 stdout 日志

ASP.NET 核心模块 stdout 日志通常记录找不到应用程序事件日志中的有用的错误消息。 用于启用和查看 stdout 日志：

1. 导航到**诊断并解决问题**在 Azure 门户中的边栏选项卡。
1. 下**选择问题类别**，选择**Web 应用程序向下**按钮。
1. 下**建议解决方案** > **启用 Stdout 日志重定向**，选择该按钮**打开 Kudu 控制台编辑 Web.Config**。
1. 在 Kudu **Diagnostic 控制台**，打开的文件夹的路径**站点** > **wwwroot**。 向下滚动到显示*web.config*列表底部的文件。
1. 单击铅笔图标旁边*web.config*文件。
1. 设置**stdoutLogEnabled**到`true`和更改**stdoutLogFile**路径： `\\?\%home%\LogFiles\stdout`。
1. 选择**保存**以保存已更新*web.config*文件。
1. 向应用程序发出的请求。
1. 返回到 Azure 门户。 选择**高级工具**中的边栏选项卡**开发工具**区域。 选择**转&rarr;**按钮。 Kudu 控制台打开新浏览器选项卡或窗口中。
1. 使用的页上，顶部导航栏打开**调试控制台**和选择**CMD**。
1. 选择**LogFiles**文件夹。
1. 检查**已修改**列并选择铅笔图标以编辑 stdout 日志的最新的修改日期。
1. 在打开日志文件后，将显示错误。

**重要 ！** 禁用完成故障排除时，日志记录 stdout。

1. 在 Kudu **Diagnostic 控制台**，则返回到路径**站点** > **wwwroot**以显示*web.config*文件。 打开**web.config**再次通过选择铅笔图标的文件。
1. 设置**stdoutLogEnabled**到`false`。
1. 选择**保存**保存文件。

> [!WARNING]
> 若要禁用 stdout 日志可能会导致应用程序或服务器失败。 没有对日志文件大小没有限制或创建的日志文件数。
>
> 对于例程日志记录在 ASP.NET Core 应用程序，使用限制日志文件大小和旋转日志的日志记录库。 有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。

## <a name="common-startup-errors"></a>常见的启动错误 

请参阅[ASP.NET 核心常见错误参考](xref:host-and-deploy/azure-iis-errors-reference)。 中的参考主题介绍了大部分常见防止应用程序启动的问题。

## <a name="process-dump-for-a-slow-or-hanging-app"></a>慢速或悬挂应用程序的进程转储

当应用程序响应速度很慢或挂起的请求时，请参阅[在 Azure App Service 中进行故障排除慢速的 web 应用程序性能问题](/azure/app-service/app-service-web-troubleshoot-performance-degradation)用于调试的指导。

## <a name="remote-debugging"></a>远程调试

请参阅[远程调试的故障排除 Azure App Service 中使用 Visual Studio 中的 web 应用的 web 应用部分](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)Azure 文档中。

## <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/)提供从托管在 Azure App Service，包括错误日志记录和报告功能的应用程序的遥测。 Application Insights 只能针对在应用程序启动时应用的日志记录功能变得可用之后发生的错误报告。 有关详细信息，请参阅[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)。

## <a name="monitoring-blades"></a>监视边栏选项卡

监视边栏选项卡提供疑难解答的体验到本主题前面所述的方法的替代方法。 这些边栏选项卡可以用于诊断 500 系列错误。

确认安装了 ASP.NET 核心扩展。 如果未安装的扩展，请手动安装它们：

1. 在**开发工具**边栏选项卡部分中，选择**扩展**边栏选项卡。
1. **ASP.NET 核心扩展**应出现在列表中。
1. 如果未安装的扩展，选择**添加**按钮。
1. 选择**ASP.NET 核心扩展**从列表中。
1. 选择**确定**以接受法律条款。
1. 选择**确定**上**将扩展添加**边栏选项卡。
1. 弹出一条信息性消息表示成功安装的扩展。

如果未启用 stdout 日志记录，请按照下列步骤：

1. 在 Azure 门户中，选择**高级工具**中的边栏选项卡**开发工具**区域。 选择**转&rarr;**按钮。 Kudu 控制台打开新浏览器选项卡或窗口中。
1. 使用的页上，顶部导航栏打开**调试控制台**和选择**CMD**。
1. 打开的文件夹的路径**站点** > **wwwroot**向下滚至揭示*web.config*列表底部的文件。
1. 单击铅笔图标旁边*web.config*文件。
1. 设置**stdoutLogEnabled**到`true`和更改**stdoutLogFile**路径： `\\?\%home%\LogFiles\stdout`。
1. 选择**保存**以保存已更新*web.config*文件。

继续激活诊断日志记录：

1. 在 Azure 门户中，选择**诊断日志**边栏选项卡。
1. 选择**上**切换**应用程序日志记录 （文件系统）**和**详细错误消息**。 选择**保存**边栏选项卡顶部的按钮。
1. 若要包含失败的请求跟踪，也称为失败请求事件缓冲 (FREB) 日志记录，选择**上**切换**失败请求跟踪**。 
1. 选择**日志流**边栏选项卡，其中立即下列出**诊断日志**在门户中的边栏选项卡。
1. 向应用程序发出的请求。
1. 在日志的流数据，指示错误的原因。

**重要 ！** 请务必禁用完成故障排除时，日志记录 stdout。 请参阅中的说明[ASP.NET 核心模块 stdout 日志](#aspnet-core-module-stdout-log)部分。

若要查看失败的请求跟踪日志 （FREB 日志）：

1. 导航到**诊断并解决问题**在 Azure 门户中的边栏选项卡。
1. 选择**失败请求跟踪日志**从**支持工具**侧栏区域。

请参阅[失败请求跟踪部分中的 Azure App Service 主题中的 web 应用启用诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和[Azure 中的 Web 应用的应用程序性能常见问题： 如何打开失败的请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)有关详细信息。

有关详细信息，请参阅[启用 Azure App Service 中的 web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。

> [!WARNING]
> 若要禁用 stdout 日志可能会导致应用程序或服务器失败。 没有对日志文件大小没有限制或创建的日志文件数。
>
> 对于例程日志记录在 ASP.NET Core 应用程序，使用限制日志文件大小和旋转日志的日志记录库。 有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。

## <a name="additional-resources"></a>其他资源

* [ASP.NET 核心中的错误处理简介](xref:fundamentals/error-handling)
* [Azure 应用服务和 IIS 上 ASP.NET Core 的常见错误参考](xref:host-and-deploy/azure-iis-errors-reference)
* [对 Azure App Service 中使用 Visual Studio 中的 web 应用进行故障排除](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [排除"502 错误网关"和"503 服务不可用"Azure web 应用中的 HTTP 的错误](/app-service/app-service-web-troubleshoot-http-502-http-503)
* [解决在 Azure App Service 的慢速 web 应用程序性能问题](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [在 Azure 中的 Web 应用的应用程序性能常见问题](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Friday: Azure 应用程序服务诊断和故障排除体验 （12 分钟视频）](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
