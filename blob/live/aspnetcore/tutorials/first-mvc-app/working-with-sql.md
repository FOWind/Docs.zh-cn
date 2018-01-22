---
title: "使用 SQL Server LocalDB"
author: rick-anderson
description: "结合简单的 MVC 应用使用 SQL Server LocalDB"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 14b0f04ce904b34a03b3c5160e189e2e6d045734
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="working-with-sql-server-localdb"></a><span data-ttu-id="a11d0-103">使用 SQL Server LocalDB</span><span class="sxs-lookup"><span data-stu-id="a11d0-103">Working with SQL Server LocalDB</span></span>

<span data-ttu-id="a11d0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a11d0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a11d0-105">`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="a11d0-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="a11d0-106">在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="a11d0-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

<span data-ttu-id="a11d0-107">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="a11d0-107">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="a11d0-108">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串：</span><span class="sxs-lookup"><span data-stu-id="a11d0-108">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[Main](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

<span data-ttu-id="a11d0-109">将应用部署到测试或生产服务器时，可使用环境变量或另一种方法将连接字符串设置为实际的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="a11d0-109">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="a11d0-110">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="a11d0-110">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="a11d0-111">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="a11d0-111">SQL Server Express LocalDB</span></span>

<span data-ttu-id="a11d0-112">LocalDB 是 SQL Server Express 数据库引擎的轻型版本，专门针对程序开发。</span><span class="sxs-lookup"><span data-stu-id="a11d0-112">LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.</span></span> <span data-ttu-id="a11d0-113">LocalDB 按需启动并在用户模式下运行，因此没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="a11d0-113">LocalDB starts on demand and runs in user mode, so there is no complex configuration.</span></span> <span data-ttu-id="a11d0-114">默认情况下，LocalDB 数据库在 C:/Users/\<user\> 目录中创建“\*.mdf”文件。</span><span class="sxs-lookup"><span data-stu-id="a11d0-114">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

* <span data-ttu-id="a11d0-115">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX)。</span><span class="sxs-lookup"><span data-stu-id="a11d0-115">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* <span data-ttu-id="a11d0-117">右键单击 `Movie` 表，然后单击“视图设计器”</span><span class="sxs-lookup"><span data-stu-id="a11d0-117">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

<span data-ttu-id="a11d0-120">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="a11d0-120">Note the key icon next to `ID`.</span></span> <span data-ttu-id="a11d0-121">默认情况下，EF 将名为 `ID` 的属性设置为主键。</span><span class="sxs-lookup"><span data-stu-id="a11d0-121">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="a11d0-122">右键单击 `Movie` 表，然后单击“查看数据”</span><span class="sxs-lookup"><span data-stu-id="a11d0-122">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="a11d0-125">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="a11d0-125">Seed the database</span></span>

<span data-ttu-id="a11d0-126">在 Models 文件夹中创建一个名为 `SeedData` 的新类。</span><span class="sxs-lookup"><span data-stu-id="a11d0-126">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="a11d0-127">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="a11d0-127">Replace the generated code with the following:</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="a11d0-128">如果 DB 中没有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="a11d0-128">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="a11d0-129">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="a11d0-129">Add the seed initializer</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a11d0-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a11d0-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a11d0-131">将种子初始值设定项添加 Program.cs 文件中的 `Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="a11d0-131">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Program.cs?highlight=6,14-32)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a11d0-132">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a11d0-132">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a11d0-133">将种子初始值设定项添加到 Startup.cs 文件中的 `Configure` 方法末尾：</span><span class="sxs-lookup"><span data-stu-id="a11d0-133">Add the seed initializer to the end of the `Configure` method in the *Startup.cs* file.</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]

---

<span data-ttu-id="a11d0-134">测试应用</span><span class="sxs-lookup"><span data-stu-id="a11d0-134">Test the app</span></span>

* <span data-ttu-id="a11d0-135">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="a11d0-135">Delete all the records in the DB.</span></span> <span data-ttu-id="a11d0-136">可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="a11d0-136">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="a11d0-137">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="a11d0-137">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="a11d0-138">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="a11d0-138">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="a11d0-139">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="a11d0-139">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="a11d0-140">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点”</span><span class="sxs-lookup"><span data-stu-id="a11d0-140">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

   * <span data-ttu-id="a11d0-143">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行</span><span class="sxs-lookup"><span data-stu-id="a11d0-143">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
   * <span data-ttu-id="a11d0-144">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5</span><span class="sxs-lookup"><span data-stu-id="a11d0-144">If you were running VS in debug mode, stop the debugger and press F5</span></span>
   
<span data-ttu-id="a11d0-145">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="a11d0-145">The app shows the seeded data.</span></span>

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55.png)

>[!div class="step-by-step"]
<span data-ttu-id="a11d0-147">[上一页](adding-model.md)
[下一页](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="a11d0-147">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  