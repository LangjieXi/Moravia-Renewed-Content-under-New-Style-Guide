<properties 
   pageTitle="SQL 数据库的业务连续性设计" 
   description="选择指南 本部分提供有关如何选择要使用的 BCDR 功能以及何时使用这些功能的指南。这包括使用 SQL DB 自动获取的内容的说明。"
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>  


<tags
   ms.service="sql-database"
   ms.date="05/27/2016"
   wacn.date="05/23/2016"/>

#业务连续性设计

针对业务连续性设计应用程序时，需要回答以下问题：

1. 哪种业务连续性功能适合用于防止应用程序发生中断？
2. 需要使用哪个级别的冗余和复制拓扑？

有关使用弹性池时的详细恢复策略，请参阅[使用 SQL 数据库弹性池的应用程序的灾难恢复策略](/documentation/articles/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)。

## 何时使用异地还原

数据库因其所在的区域发生事故而不可用时，[异地还原](/documentation/articles/sql-database-geo-restore)会提供默认的恢复选项。默认情况下，SQL 数据库为每个数据库提供内置的基本保护。具体的保护方式是执行数据库备份并将[数据库备份](/documentation/articles/sql-database-automated-backups)存储在异地冗余 Azure 存储空间 (GRS) 中。如果选择此方法，则不需要进行特殊的配置或其他资源分配。借助这些备份，可以使用异地还原命令来恢复任何区域中的数据库。[在中断后恢复](/documentation/articles/sql-database-disaster-recovery)部分中介绍了使用异地还原恢复应用程序的详细信息。

如果应用程序符合以下条件，则应使用内置保护功能：

1. 它不是任务关键型应用程序。它没有约束性的 SLA，也就是说，停机 24 小时或更长时间不会导致财务责任。
2. 数据更改频率（例如，每小时事务数）较低。1 小时的 RPO 不会导致丢失大量数据。
3. 应用程序成本敏感，并且无法判断异地复制的额外成本 

> [AZURE.NOTE] 在任何特定的区域，中断期间异地还原不会预先分配计算容量从备份中还原活动的数据库。服务管理与异地还原请求关联的工作负荷的方式是，最大程度地降低对该区域中现有数据库的影响，而这些数据库的容量需求具有优先级。因此，数据库恢复时间将取决于同一区域中同一时间要恢复的其他数据库数量。

## 何时使用活动异地复制

[活动异地复制](/documentation/articles/sql-database-geo-replication-overview)支持从主要位置创建位于不同区域的可读（辅助）数据库。它可以保证数据库在恢复后，具有必要的数据和计算资源来支持应用程序的工作负荷。有关使用故障转移恢复应用程序的信息，请参阅[在中断后恢复](/documentation/articles/sql-database-disaster-recovery)部分。

如果应用程序符合以下条件，则应使用异地复制：

1. 它是任务关键型应用程序。它具有严格规定了 RPO 和 RTO 的约束性 SLA。丢失数据和可用性会导致财务责任。 
2. 数据更改频率（例如每分钟或每秒事务数）较高。使用默认保护时发生 1 小时的 RPO 很可能会导致不可接受的数据丢失。
3. 使用异地复制所涉及的成本明显低于潜在财务责任和相关业务损失所付出的代价。

若要启用活动异地复制，请参阅[为 Azure SQL 数据库配置异地复制](/documentation/articles/sql-database-geo-replication-portal)。

> [AZURE.NOTE] 活动异地复制还支持对辅助数据库进行只读访问，因此为只读工作负荷提供了额外的容量。

##如何选择故障转移配置 

在针对业务连续性设计应用程序时，应考虑多个配置选项。所做的选择取决于应用程序部署拓扑，以及应用程序的哪些组件最容易中断。有关指信息，请参阅[使用异地复制设计云解决方案以实现灾难恢复](/documentation/articles/sql-database-designing-cloud-solutions-for-disaster-recovery)。

## 后续步骤

- [使用异地复制设计灾难恢复的云解决方案](/documentation/articles/sql-database-designing-cloud-solutions-for-disaster-recovery)

## 其他资源

- [使用 SQL 数据库弹性池的应用程序的灾难恢复策略](/documentation/articles/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool) 

<!---HONumber=Mooncake_Quality_Review_1118_2016-->