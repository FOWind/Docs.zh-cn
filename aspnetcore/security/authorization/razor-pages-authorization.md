---
title: "在 ASP.NET Core razor 页授权约定"
author: guardrex
description: "了解如何控制对在启动时的约定来为用户授权，并允许匿名用户访问各个页或文件夹中的页的页的访问。"
manager: wpickett
ms.author: riande
ms.date: 10/27/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 2bad6e1cc654b972206af03f99160628f81e026f
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2018
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>在 ASP.NET Core razor 页授权约定

作者：[Luke Latham](https://github.com/guardrex)

若要控制在 Razor 页面应用程序的访问的一个方法是在启动时使用授权约定。 这些约定，可以为用户授权，并允许匿名用户访问各个页的文件夹。 自动本主题中所述的约定应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="require-authorization-to-access-a-page"></a>需要授权可访问的页面

使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)通过约定[AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions)添加[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)到页中指定的路径：

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,4)]

指定的路径是视图引擎路径，这是无需扩展和包含仅正斜杠的 Razor 页根相对路径。

[AuthorizePage 重载](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_AuthorizePage_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_String_System_String_)当你需要指定一个授权策略才可用。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>需要的页的文件夹的访问权

使用[AuthorizeFolder](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder)通过约定[AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions)添加[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)到所有指定路径上的文件夹中的页：

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,5)]

指定的路径是视图引擎路径，这是 Razor 页根相对路径。

[AuthorizeFolder 重载](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_AuthorizeFolder_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_String_System_String_)当你需要指定一个授权策略才可用。

## <a name="allow-anonymous-access-to-a-page"></a>允许匿名访问的页

使用[AllowAnonymousToPage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage)通过约定[AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions)添加[AllowAnonymousFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)到指定的路径下的网页：

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,6)]

指定的路径是视图引擎路径，这是无需扩展和包含仅正斜杠的 Razor 页根相对路径。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>允许匿名访问的页面的文件夹

使用[AllowAnonymousToFolder](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder)通过约定[AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions)添加[AllowAnonymousFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)到所有指定路径上的文件夹中的页：

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,7)]

指定的路径是视图引擎路径，这是 Razor 页根相对路径。

## <a name="note-on-combining-authorized-and-anonymous-access"></a>请注意有关组合授权和匿名访问

这是完全合法，若要指定的页的文件夹需要授权，并指定该文件夹中的页允许匿名访问：

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

但是，反过来，不是如此。 不能声明用于匿名访问的页面的文件夹，并指定授权中的页：

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private") 
```

需要专用的页上的授权不起作用，因为当同时`AllowAnonymousFilter`和`AuthorizeFilter`筛选器应用到页上， `AllowAnonymousFilter` wins 和控制的访问。

## <a name="see-also"></a>请参阅

* [Razor 页面自定义路由和页面模型提供程序](xref:mvc/razor-pages/razor-pages-convention-features)
* [PageConventionCollection](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection)类
