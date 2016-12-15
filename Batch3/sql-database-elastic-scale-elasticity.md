<properties 
	pageTitle="分片弹性" 
	description="介绍了分片弹性（可轻松扩展 Azure SQL 数据库的能力）的概念并提供了相关示例。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>  


<tags 
	ms.service="sql-database" 
	ms.date="09/22/2015" 
	wacn.date=""/>

# 分片弹性 

**分片弹性**使应用程序开发人员能够根据需求动态增加和缩减数据库资源，使开发人员能够优化其应用程序的性能，并在最大程度上降低成本。Azure SQL 数据库的**弹性数据库工具**与[基本、标准和高级服务层](/documentation/articles/sql-database-service-tiers)的组合提供了非常有说服力的弹性方案。弹性数据库工具支持水平缩放这一种设计模式，在该模式下，通过从分片集添加或删除数据库（也[称为“分片”](/documentation/articles/sql-database-elastic-scale-glossary)）来增加或缩减容量。同样，SQL 数据库服务层提供了**垂直缩放**功能，其中单一数据库的资源可以向上或向下缩放以相应地满足需求。单个分片的垂直缩放和多个分片的水平缩放共同为应用程序开发人员提供了非常灵活的环境，该环境可进行缩放以满足性能、容量和成本优化需求。

凭借新引入的**弹性数据库池**功能，可以更简单地实现垂直缩放。池允许各个数据库的资源消耗*自动*进行扩大或缩小，且不超出整个池共有的预算。对于选择不利用弹性数据库池的应用程序，本文将介绍其他一些技术，可使用这些技术实施基于策略的机制以管理垂直缩放，并介绍了用于自动化水平缩放操作的常见方案。

## 水平缩放示例：演唱会门票销量猛增

水平缩放的一个规范方案是处理演唱会门票交易的应用程序。在客户数量正常的情况下，该应用程序使用最少的数据库资源来处理购买交易。但是，在销售一场热门演唱会的门票时，单一数据库便无法处理急剧上升的客户需求。

因此为了处理急剧增加的交易，该应用程序将进行水平缩放。该应用程序现在可以在多个分片上分布交易负载。当不再需要额外的资源时，数据库层将缩减回正常的使用量。在这里，水平缩放使应用程序能够进行扩展以满足客户需求，并在不再需要资源时缩小。

## 垂直缩放示例：遥测

一个垂直缩放的规范方案是使用**分片集**存储操作遥测的应用程序。在此方案中，最好将一天的所有遥测数据一起放置在单个分片上。在此应用程序中，会将当天的数据引入到一个分片中，并为以后的日子预配一个新的分片。操作数据会逐渐老化，并且可进行适当地查询这些数据。

为了在高负载时引入遥测数据，最好使用较高的服务层。换言之，高级数据库要优于基本数据库。当数据库达到其容量上限后，它将从引入切换为分析和报告。为此，标准层的性能等同于该任务。这样，开发人员可以在分片（除了最新创建的分片）上向下扩展服务层，以符合旧数据较低的性能要求。

还可以将垂直缩放用于提高单一数据库的性能，以此使性能得到提升。例如，纳税申报应用程序。在进行申报时，最好将单个客户的数据全部保留在同一个数据库中，并提高该分片的性能。根据不同的应用程序，垂直向上和向下扩展资源有利于优化成本和满足性能要求。

数据库层的水平和垂直缩放都是应用程序可缩放性和分片弹性的核心。

本文档提供了分片弹性方案的上下文，以及垂直和水平缩放的示例实现的参考。

## 分片弹性的机制 

垂直和水平缩放是由三个基本部分组成的功能：

1. **遥测**
2. **规则**
3. **操作**   

## 遥测

**数据驱动的弹性**是弹性缩放应用程序的核心。根据性能要求，可使用遥测做出是进行垂直缩放还是水平缩放的数据驱动的决策。

#### 遥测数据源
在 Azure SQL DB 的上下文中，有少量关键源可用作分片弹性的数据源。

1. **性能遥测**在 **sys.resource\_stats** 视图中公开 5 分钟 
2. 每小时**数据库容量遥测**通过 **sys.resource\_usage** 视图公开。  

开发人员可通过使用以下查询来查询主数据，以分析性能资源使用情况，其中“Shard\_20140623”是目标数据库的名称。

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

可汇总一段时间内的**性能遥测**（在以下查询中为七天）以删除暂时性行为。

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

可使用针对 **sys.resource\_usage** 视图的类似查询来测量**数据库容量**。**storage\_in\_megabytes** 列的最大值将生成数据库的当前大小。对于在具体的分片达到其容量上限时水平缩放应用程序，这样的遥测非常有用。

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

由于数据已引入到具体的分片中，因此提前一天进行计划并确定该分片是否有足够的容量处理未来的工作负载是十分有用的。虽然不是线性回归的真正实现，但是以下查询将返回连续两天内数据库容量中的最大增量。然后可将此类遥测应用于规则，之后该规则将导致执行某个操作（或不执行操作）。

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## 规则  

规则是确定是否执行某个操作的决策引擎。有些规则非常简单，而有些则非常复杂。如下方代码片段中所示，可以配置基于容量的规则，以便在分片达到其最大容量的 $SafetyMargin（例如，80%）时预配新的分片。

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

通过上面提供的数据源，可以制定许多规则以完成大量的分片弹性方案。

## 操作  

应用规则的结果将导致执行操作（或不执行操作）。两种最常见的操作是：

* 增加或减少分片的服务层或性能级别 
* 从分片集添加或删除分片。

请注意：在水平和垂直缩放解决方案中，操作的结果不是立即完成的。例如，在进行垂直缩放时，发布将基本数据库的服务层增加到高级数据库的 ALTER DATABASE 命令所花费的时间是不确定的。该持续时间在很大程度上取决于数据库的大小（有关详细信息，请参阅[更改服务层和性能级别](http://msdn.microsoft.com/zh-cn/library/azure/dn369872.aspx)。同样，新分片的分配或预配也不是立即完成的。因此，对于垂直和水平缩放，应用程序应考虑更改或预配新数据库所花的非零时间。

## 示例分片弹性方案 

下图所示的示例重点介绍了两种弹性缩放方案：1. 分片映射的水平缩放；2. 单个分片的垂直缩放。

![操作数据引入][1]

为了进行水平缩放，将使用规则（基于日期或数据库大小）来预配新分片并将它注册到分片映射，从而水平增加数据库层。其次，为了进行垂直缩放，将实施第二个规则：在该规则中，任何超过一天的分片都将从高级版降级到标准或基本版。

再次考虑该遥测方案：按日期列出应用程序分片。它会持续收集遥测数据，因此在加载时需要使用高性能版本，但在数据老化时则需要较低的性能。当天的数据 [Tnow] 将写入高性能数据库（高级）。过了零点后，前一天的分片（现在为 [T-1]）将不再用于引入。当前数据由当前的 [Tnow] 引入。在第二天到来之前，必须预配新的分片 ([T+1]) 并注册到分片映射。

这可通过在每次新的一天到来之前预配新的分片，或在当前分片 ([Tnow]) 接近其最大容量时预配新的分片来完成。使用这些方法中的任何一种，都将保留某一天写入的所有遥测的数据局部性。还可以应用每小时分片，以获得更精细的粒度。在预配新分片后，由于 [T-1] 用于查询和报告，因此最好减少数据库的性能级别以降低成本。当数据库中的内容老化时，可进一步减少性能层，并且/或者可将数据库的内容存档到 Azure 存储或将其删除，具体操作取决于应用程序。

## 执行分片弹性方案  

为了实际实现水平和垂直方案，已在脚本中心上创建并发布了许多[分片弹性示例脚本](http://go.microsoft.com/?linkid=9862617)。已将这些 PowerShell Runbook 编写为在 Azure 自动化服务中运行，它们提供了许多与弹性数据库客户端库和 Azure SQL 数据库交互的方法。通过基于这些代码示例进行构建，或从其中提取部分代码，开发人员可以生成必要的脚本，以使其应用程序的水平缩放、垂直缩放或这两种缩放方案同时实现自动化。

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->


<!--anchors-->
[Telemetry]: #telemetry
[Rule]: #rule
[Action]: #action
 

<!---HONumber=Mooncake_Quality_Review_1215_2016-->