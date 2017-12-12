---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/deploying-membership-databases-to-enterprise-environments
title: "将成员资格数据库部署到企业环境 |Microsoft 文档"
author: jrjlee
description: "本主题介绍的关键注意事项以及你将需要设置 ASP.NET 应用程序服务数据库 （多个通用...时需要解决的挑战"
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 3cf765df-d311-4f68-a295-c9685ceea830
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/deploying-membership-databases-to-enterprise-environments
msc.type: authoredcontent
ms.openlocfilehash: f4d898b6e09b5b9df44b62f9cb4b9d367f288efb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
<a name="deploying-membership-databases-to-enterprise-environments"></a><span data-ttu-id="9087d-103">将成员资格数据库部署到企业环境</span><span class="sxs-lookup"><span data-stu-id="9087d-103">Deploying Membership Databases to Enterprise Environments</span></span>
====================
<span data-ttu-id="9087d-104">通过[Jason Lee](https://github.com/jrjlee)</span><span class="sxs-lookup"><span data-stu-id="9087d-104">by [Jason Lee](https://github.com/jrjlee)</span></span>

[<span data-ttu-id="9087d-105">下载 PDF</span><span class="sxs-lookup"><span data-stu-id="9087d-105">Download PDF</span></span>](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> <span data-ttu-id="9087d-106">本主题介绍的关键注意事项和你将需要设置 ASP.NET 应用程序服务在测试、 临时或生产环境中 （通常称为成员资格数据库） 的数据库时需要解决的挑战。</span><span class="sxs-lookup"><span data-stu-id="9087d-106">This topic explains the key considerations and challenges you'll need to overcome when you provision ASP.NET application services databases (more commonly referred to as membership databases) in test, staging, or production environments.</span></span> <span data-ttu-id="9087d-107">它还介绍了可用来应对这些挑战的方法。</span><span class="sxs-lookup"><span data-stu-id="9087d-107">It also describes approaches you can use to meet these challenges.</span></span>


<span data-ttu-id="9087d-108">本主题窗体的基于名为 Fabrikam，Inc.的虚构公司的企业部署要求的教程系列中的一部分本系列教程使用的示例解决方案 （&） #x 2014;[联系人管理器解决方案](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014; 来表示具有现实级别的复杂性，包括 ASP.NET MVC 3 应用程序，Windows 的 web 应用程序Communication Foundation (WCF) 服务和数据库项目。</span><span class="sxs-lookup"><span data-stu-id="9087d-108">This topic forms part of a series of tutorials based around the enterprise deployment requirements of a fictional company named Fabrikam, Inc. This tutorial series uses a sample solution&#x2014;the [Contact Manager solution](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)&#x2014;to represent a web application with a realistic level of complexity, including an ASP.NET MVC 3 application, a Windows Communication Foundation (WCF) service, and a database project.</span></span>

<span data-ttu-id="9087d-109">这些教程的核心的部署方法取决于中介绍的拆分项目文件方法[了解项目文件](../web-deployment-in-the-enterprise/understanding-the-project-file.md)，在其中生成过程控制由两个项目文件 （&） #x 2014; 一个包含生成适用于每种目标环境和一个包含特定于环境的生成和部署设置的说明。</span><span class="sxs-lookup"><span data-stu-id="9087d-109">The deployment method at the heart of these tutorials is based on the split project file approach described in [Understanding the Project File](../web-deployment-in-the-enterprise/understanding-the-project-file.md), in which the build process is controlled by two project files&#x2014;one containing build instructions that apply to every destination environment, and one containing environment-specific build and deployment settings.</span></span> <span data-ttu-id="9087d-110">在生成期间，特定于环境的项目文件合并到环境无关的项目文件中以形成一组完整的生成说明。</span><span class="sxs-lookup"><span data-stu-id="9087d-110">At build time, the environment-specific project file is merged into the environment-agnostic project file to form a complete set of build instructions.</span></span>

## <a name="what-are-the-issues-when-you-deploy-a-membership-database"></a><span data-ttu-id="9087d-111">部署成员资格数据库时，问题是什么？</span><span class="sxs-lookup"><span data-stu-id="9087d-111">What Are the Issues When You Deploy a Membership Database?</span></span>

<span data-ttu-id="9087d-112">在大多数情况下，制定部署策略对于数据库，你需要考虑的第一个操作是你想要部署哪些的数据。</span><span class="sxs-lookup"><span data-stu-id="9087d-112">In most cases, when you devise a deployment strategy for a database, the first thing you need to consider is what data you want to deploy.</span></span> <span data-ttu-id="9087d-113">在开发或测试环境中，你可能想要将用户帐户数据，以便于快速且轻松地测试部署。</span><span class="sxs-lookup"><span data-stu-id="9087d-113">In a development or test environment, you might want to deploy user account data to facilitate quick and easy testing.</span></span> <span data-ttu-id="9087d-114">在过渡或生产环境中，是不太可能想要将用户帐户数据部署。</span><span class="sxs-lookup"><span data-stu-id="9087d-114">In a staging or production environment, it's very unlikely that you'd want to deploy user account data.</span></span>

<span data-ttu-id="9087d-115">遗憾的是，ASP.NET 成员资格数据库引入作出此决定大量更复杂一些特定难题：</span><span class="sxs-lookup"><span data-stu-id="9087d-115">Unfortunately, ASP.NET membership databases introduce some specific challenges that make this decision a lot more complex:</span></span>

- <span data-ttu-id="9087d-116">仅限架构的部署将使处于不可操作状态的状态的成员资格数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-116">A schema-only deployment will leave the membership database in a non-operational state.</span></span> <span data-ttu-id="9087d-117">这是因为成员资格数据库包含某些配置数据 (在**aspnet\_SchemaVersions**表)，在数据库要求才能正常。</span><span class="sxs-lookup"><span data-stu-id="9087d-117">This is because the membership database includes some configuration data (in the **aspnet\_SchemaVersions** table) that the database requires in order to function.</span></span> <span data-ttu-id="9087d-118">在这种情况下，如果以排除用户帐户数据执行成员资格数据库的仅有架构的部署，你将需要运行一个后期部署脚本来添加基本配置数据。</span><span class="sxs-lookup"><span data-stu-id="9087d-118">As such, if you perform a schema-only deployment of your membership database in order to exclude user account data, you'll need to run a post-deployment script to add the essential configuration data.</span></span>
- <span data-ttu-id="9087d-119">根据成员资格数据库的配置方式，成员资格提供程序可能使用计算机密钥对密码进行加密，并将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="9087d-119">Depending on how your membership database is configured, the membership provider may use the machine key to encrypt passwords and store them in the database.</span></span> <span data-ttu-id="9087d-120">在这种情况下，与数据库一起部署的所有用户帐户数据将都成为目标服务器上不可用。</span><span class="sxs-lookup"><span data-stu-id="9087d-120">In this case, any user account data you deploy with the database will become unusable on the destination server.</span></span> <span data-ttu-id="9087d-121">为此，将用户帐户数据部署不受支持的方案。</span><span class="sxs-lookup"><span data-stu-id="9087d-121">For this reason, deploying user account data is not a supported scenario.</span></span>

## <a name="choosing-a-membership-database-strategy"></a><span data-ttu-id="9087d-122">选择成员资格数据库策略</span><span class="sxs-lookup"><span data-stu-id="9087d-122">Choosing a Membership Database Strategy</span></span>

<span data-ttu-id="9087d-123">当你选择如何来设置企业服务器环境中的成员资格数据库，请使用以下准则：</span><span class="sxs-lookup"><span data-stu-id="9087d-123">Use these guidelines when you choose how to provision a membership database in an enterprise server environment:</span></span>

- <span data-ttu-id="9087d-124">只要有可能，则不要部署成员资格数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-124">Wherever possible, do not deploy membership databases.</span></span> <span data-ttu-id="9087d-125">相反，在目标数据库服务器上手动创建成员资格数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-125">Instead, create the membership database manually on the target database server.</span></span> <span data-ttu-id="9087d-126">如果你尚未自定义成员资格数据库架构，你可以只需创建一个新就地在目标中使用[ASP.NET SQL 服务器注册工具 (aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-126">If you haven't customized your membership database schema, you can simply create a new one in situ at the destination using the [ASP.NET SQL Server Registration Tool (aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx).</span></span>
- <span data-ttu-id="9087d-127">如果你别无选择，但若要部署的成员资格数据库 （&） #x 2014; 例如，如果你已大量修改的数据库架构 （&） #x 2014; 你应执行的成员资格数据库，若要排除用户帐户数据，仅限架构的部署和然后运行一个后期部署脚本来添加任何所需的配置数据。</span><span class="sxs-lookup"><span data-stu-id="9087d-127">If you have no option but to deploy a membership database&#x2014;for example, if you've made extensive modifications to the database schema&#x2014;you should perform a schema-only deployment of the membership database, to exclude user account data, and then run a post-deployment script to add any required configuration data.</span></span> <span data-ttu-id="9087d-128">您可以在这些方法中找到全面的指南[如何： 部署 ASP.NET 成员资格数据库而不包括用户帐户](https://msdn.microsoft.com/en-us/library/ff361972(v=vs.100).aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-128">You can find broad guidance on these approaches in [How to: Deploy the ASP.NET Membership Database Without Including User Accounts](https://msdn.microsoft.com/en-us/library/ff361972(v=vs.100).aspx).</span></span>

<span data-ttu-id="9087d-129">务必记住*成员资格数据库的架构是，可能需要相当静态*。</span><span class="sxs-lookup"><span data-stu-id="9087d-129">It's important to remember that *the schema of your membership database is likely to be fairly static*.</span></span> <span data-ttu-id="9087d-130">即使你已自定义成员资格数据库，它不太，你将需要更新的定期 （&） #x 2014年上的架构; 它不要更改 web 应用程序或数据库项目中的代码的频率相同。</span><span class="sxs-lookup"><span data-stu-id="9087d-130">Even if you've customized the membership database, it's unlikely that you'll need to update the schema on a regular basis&#x2014;it's not going to change with the same frequency as the code in a web application or a database project.</span></span> <span data-ttu-id="9087d-131">在这种情况下，则不需要任何自动进行的或单步执行部署过程中包括成员资格数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-131">As such, you shouldn't need to include the membership database in any automated or single-step deployment processes.</span></span>

## <a name="using-vsdbcmd-to-update-a-membership-database-schema"></a><span data-ttu-id="9087d-132">使用 VSDBCMD 更新成员资格数据库架构</span><span class="sxs-lookup"><span data-stu-id="9087d-132">Using VSDBCMD to Update a Membership Database Schema</span></span>

<span data-ttu-id="9087d-133">如果在第一个部署之后修改了成员资格数据库的结构，你可能不想要使用 Internet 信息服务 (IIS) Web 部署工具 （Web 部署） 来部署该数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-133">If you modify the structure of your membership database after your first deployment, you may not want to use the Internet Information Services (IIS) Web Deployment Tool (Web Deploy) to redeploy the database.</span></span> <span data-ttu-id="9087d-134">Web 部署中的数据库部署功能不包括的功能，以向的目标数据库 （&） #x 2014年差异更新，而是，Web 部署必须删除并重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-134">The database deployment functionality in Web Deploy doesn't include the capability to make differential updates to a destination database&#x2014;instead, Web Deploy must drop and re-create the database.</span></span> <span data-ttu-id="9087d-135">这意味着，你会失去在过渡环境或生产环境中通常不需要任何现有用户帐户数据。</span><span class="sxs-lookup"><span data-stu-id="9087d-135">This means that you lose any existing user account data, which is typically undesirable in staging or production environments.</span></span>

<span data-ttu-id="9087d-136">替代方法是使用 VSDBCMD 实用程序来更新目标数据库的架构。</span><span class="sxs-lookup"><span data-stu-id="9087d-136">The alternative is to use the VSDBCMD utility to update the schema of your destination database.</span></span> <span data-ttu-id="9087d-137">VSDBCMD 包括两个重要功能。</span><span class="sxs-lookup"><span data-stu-id="9087d-137">VSDBCMD includes two important capabilities.</span></span> <span data-ttu-id="9087d-138">首先，它可以将现有数据库架构导入.dbschema 文件。</span><span class="sxs-lookup"><span data-stu-id="9087d-138">First, it can import the schema of an existing database into a .dbschema file.</span></span> <span data-ttu-id="9087d-139">其次，它可以为差异更新，这意味着它只是使将最新的目标数据库所需的更改，并且不会丢失任何数据，对现有数据库中部署.dbschema 文件。</span><span class="sxs-lookup"><span data-stu-id="9087d-139">Second, it can deploy a .dbschema file to an existing database as a differential update, which means that it only makes the changes required to bring the target database up to date and you don't lose any data.</span></span>

<span data-ttu-id="9087d-140">你可以使用以下高级步骤更新成员资格数据库架构：</span><span class="sxs-lookup"><span data-stu-id="9087d-140">You can use these high-level steps to update a membership database schema:</span></span>

1. <span data-ttu-id="9087d-141">使用 VSDBCMD**导入**操作来生成你的源成员资格数据库的.dbschema 文件。</span><span class="sxs-lookup"><span data-stu-id="9087d-141">Use the VSDBCMD **Import** action to generate a .dbschema file for your source membership database.</span></span> <span data-ttu-id="9087d-142">中介绍了此过程[如何： 在命令提示符下导入架构](https://msdn.microsoft.com/en-us/library/dd172135.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-142">This procedure is described in [How to: Import a Schema from a Command Prompt](https://msdn.microsoft.com/en-us/library/dd172135.aspx).</span></span>
2. <span data-ttu-id="9087d-143">使用 VSDBCMD**部署**操作将.dbschema 文件部署到你的目标成员资格数据库。</span><span class="sxs-lookup"><span data-stu-id="9087d-143">Use the VSDBCMD **Deploy** action to deploy the .dbschema file to your destination membership database.</span></span> <span data-ttu-id="9087d-144">中介绍了此过程[VSDBCMD 的命令行参考。EXE （部署和架构导入）](https://msdn.microsoft.com/en-us/library/dd193283.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-144">This procedure is described in [Command-Line Reference for VSDBCMD.EXE (Deployment and Schema Import)](https://msdn.microsoft.com/en-us/library/dd193283.aspx).</span></span>

## <a name="conclusion"></a><span data-ttu-id="9087d-145">结束语</span><span class="sxs-lookup"><span data-stu-id="9087d-145">Conclusion</span></span>

<span data-ttu-id="9087d-146">本主题介绍一些你可能需要进行设置各种目标环境中的 ASP.NET 成员资格数据库时所面临的挑战。</span><span class="sxs-lookup"><span data-stu-id="9087d-146">This topic described some of the challenges you may face when you need to provision ASP.NET membership databases in various target environments.</span></span> <span data-ttu-id="9087d-147">具体而言，它解释为什么仅限架构的部署将使成员资格数据库处于不可操作状态的状态和原因将用户帐户数据部署不支持。</span><span class="sxs-lookup"><span data-stu-id="9087d-147">In particular, it explained why schema-only deployments will leave the membership database in a non-operational state and why deploying user account data is not supported.</span></span> <span data-ttu-id="9087d-148">本主题还提供有关如何设置、 部署和更新成员资格数据库中不同的方案的指南。</span><span class="sxs-lookup"><span data-stu-id="9087d-148">The topic also presented guidance on how to provision, deploy, and update membership databases in different scenarios.</span></span>

## <a name="further-reading"></a><span data-ttu-id="9087d-149">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="9087d-149">Further Reading</span></span>

<span data-ttu-id="9087d-150">有关更多指导和如何使用 VSDBCMD 的示例，请参阅[VSDBCMD 的命令行参考。EXE （部署和架构导入）](https://msdn.microsoft.com/en-us/library/dd193283.aspx)和[如何： 在命令提示符下导入架构](https://msdn.microsoft.com/en-us/library/dd172135.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-150">For more guidance and examples of how to use VSDBCMD, see [Command-Line Reference for VSDBCMD.EXE (Deployment and Schema Import)](https://msdn.microsoft.com/en-us/library/dd193283.aspx) and [How to: Import a Schema from a Command Prompt](https://msdn.microsoft.com/en-us/library/dd172135.aspx).</span></span> <span data-ttu-id="9087d-151">有关详细信息使用 aspnet\_regsql.exe 创建成员资格数据库，请参阅[ASP.NET SQL 服务器注册工具 (aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-151">For more information on using aspnet\_regsql.exe to create membership databases, see [ASP.NET SQL Server Registration Tool (aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx).</span></span> <span data-ttu-id="9087d-152">有关部署成员资格数据库的更多常规指南，请参阅[如何： 部署 ASP.NET 成员资格数据库而不包括用户帐户](https://msdn.microsoft.com/en-us/library/ff361972(v=vs.100).aspx)。</span><span class="sxs-lookup"><span data-stu-id="9087d-152">For more general guidance on deploying membership databases, see [How to: Deploy the ASP.NET Membership Database Without Including User Accounts](https://msdn.microsoft.com/en-us/library/ff361972(v=vs.100).aspx).</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="9087d-153">[上一页](deploying-database-role-memberships-to-test-environments.md)
[下一页](excluding-files-and-folders-from-deployment.md)</span><span class="sxs-lookup"><span data-stu-id="9087d-153">[Previous](deploying-database-role-memberships-to-test-environments.md)
[Next](excluding-files-and-folders-from-deployment.md)</span></span>