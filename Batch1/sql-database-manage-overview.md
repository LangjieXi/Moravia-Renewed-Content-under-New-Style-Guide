<properties
	pageTitle="概述：SQL 数据库的管理工具"
	description="比较管理 Azure SQL 数据库的工具和选项"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>  


<tags
	ms.service="sql-database"
	ms.date="07/19/2016"
	wacn.date="04/22/2016"/>

# 概述：SQL 数据库的管理工具

本主题探讨并比较用于管理 Azure SQL 数据库的工具和选项，便于用户挑选适合作业、业务以及自身需求的工具。选择合适的工具取决于所管理的数据库数量、任务以及执行任务的频率。

## Azure 门户

[Azure 门户](https://manage.windowsazure.cn)是一个基于 Web 的应用程序，可以在其中创建、更新和删除数据库及逻辑服务器并监视数据库活动。如果刚开始使用 Azure（管理少量的数据库）或需要快速执行某些操作，该工具是理想之选。

有关使用该门户的更详细信息，请参阅[使用 Azure 管理门户管理 SQL 数据库](/documentation/articles/sql-database-manage-portal/)。

## Visual Studio 中的 SQL Server Management Studio 和 SQL Server Data Tools

Visual Studio 中的 SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 是在计算机上运行的客户端工具，可用于连接到、管理和开发云中的数据库。如果你是熟悉 Visual Studio 或其他集成开发环境 (IDE) 的应用程序开发人员，请[尝试使用 Visual Studio 中的 SSDT](https://msdn.microsoft.com/zh-cn/library/mt204009.aspx)。许多数据库管理员都熟悉 SSMS（可用于 Azure SQL 数据库）。[下载最新版本的 SSMS](https://msdn.microsoft.com/zh-cn/library/mt238290)，在使用 Azure SQL 数据库时始终使用该最新版本。有关使用 SSMS 管理 Azure SQL 数据库的详细信息，请参阅[使用 SSMS 管理 SQL 数据库](/documentation/articles/sql-database-manage-azure-ssms/)。

> [AZURE.IMPORTANT] 必须使用最新版本的 SQL Server Management Studio 和 SQL Server Data Tools for Visual Studio 来保持与 Microsoft Azure 和 SQL 数据库的更新同步。这些早期版本的工具无法正常用于 SQL 数据库。

## 命令行工具

你可以使用诸如 PowerShell 之类的命令行工具管理数据库和弹性数据库池，并自动执行 Azure 资源部署。Azure 建议在生产环境中使用此工具来管理大量的数据库并自动进行部署和资源更改。

有关使用命令行工具管理 Azure SQL 数据库的详细信息，请参阅[使用 PowerShell 管理 SQL 数据库](/documentation/articles/sql-database-command-line-tools/)

## 弹性数据库工具
使用弹性数据库工具执行如下操作：

* 使用[弹性作业](/documentation/articles/sql-database-elastic-jobs-overview/)针对一组数据库执行 T-SQL 脚本
* 使用[拆分-合并工具](/documentation/articles/sql-database-elastic-scale-overview-split-and-merge/)将多租户模型数据库移至单租户模型
* 使用[弹性扩展客户端库](/documentation/articles/sql-database-elastic-database-client-library/)管理单租户模型或多租户模型中的数据库。
 

<!---HONumber=Mooncake_Quality_Review_1118_2016-->