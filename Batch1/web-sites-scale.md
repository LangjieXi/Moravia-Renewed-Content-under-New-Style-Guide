<properties
	pageTitle="增加 Azure 中的应用 | Azure"
	description="了解如何增加 Azure App Service 中的应用以增加容量和功能。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>  


<tags
	ms.service="app-service"
	ms.date="07/05/2016"
	wacn.date=""/>

# 增加 Azure 中的应用 #

本文介绍如何在 Azure App Service 中缩放应用。缩放的工作流有两种：增加和向外缩放；本文介绍增加工作流。

- [增加](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多 CPU、内存、磁盘空间、专用虚拟机 (VM) 等附加功能、自定义域和证书、过渡槽、自动缩放等。通过更改应用所属的应用服务计划的定价层可以增加。
- [向外缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加运行应用的 VM 实例数。根据定价层，最多可以向外缩放到 20 个实例。**高级**层中的[应用服务环境](/documentation/articles/app-service-app-service-environments-readme/)会进一步将向外缩放计数增加到 50 个实例。有关向外缩放的详细信息，请参阅[手动或自动缩放实例计数](/documentation/articles/insights-how-to-scale/)。本文介绍了如何使用自动缩放，即根据预定义的规则和计划自动缩放实例计数。

缩放设置只需几秒钟即会应用，且影响范围遍及[应用服务计划](/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)中的所有应用。在此过程中，不需要更改代码或重新部署应用程序。

有关各个应用服务计划的定价和功能的信息，请参阅[应用服务定价详细信息](/pricing/details/web-sites/)。

> [AZURE.NOTE] 从**免费**层切换应用服务计划之前，必须先删除 Azure 订阅的现有[支出限制](/pricing/spending-limits/)。若要查看或更改 Azure App Service 订阅的选项，请参阅 [Azure Subscriptions][azuresubscriptions]（Azure 订阅）。

<a name="scalingsharedorbasic">
## <a name="scalingstandard"></a>增加定价层

1. 在浏览器中，打开 [Azure 门户][portal]。

2. 在应用的边栏选项卡中，单击“所有设置”，然后单击“增加”。

	![导航到增加 Azure 应用。][ChooseWHP]

4. 选择层，然后单击“选择”。

	操作完成后，“通知”选项卡将闪烁绿色的“成功”字样。

## <a name="ScalingSQLServer"></a>缩放相关资源
如果应用依赖于其他服务，如 Azure SQL 数据库或 Azure 存储，则还可以按需增加这些资源。不应将这些资源与应用服务计划一起缩放，而必须单独进行缩放。

1. 在“概要”中，单击“资源组”链接。

	![增加 Azure 应用的相关资源](./media/web-sites-scale/RGEssentialsLink.png)

2. 在“资源组”边栏选项卡的“摘要”部分中，单击要进行缩放的一个资源。以下屏幕截图显示 SQL 数据库资源和 Azure 存储资源。

	![导航到资源组边栏选项卡以增加 Azure 应用](./media/web-sites-scale/ResourceGroup.png)

3. 对于 SQL 数据库资源，请单击“设置”>“定价层”以缩放定价层。

	![增加 Azure 应用的 SQL 数据库后端](./media/web-sites-scale/ScaleDatabase.png)

	还可以为 SQL 数据库实例打开[异地复制](/documentation/articles/sql-database-geo-replication-overview/)。

    对于 Azure 存储资源，请单击“设置”>“配置”以增加存储选项。

    ![增加 Azure 应用使用的 Azure 存储帐户](./media/web-sites-scale/ScaleStorage.png)

## <a name="devfeatures"></a>了解开发人员功能
我们根据定价层提供以下面向开发人员的功能：

### 位数 ###

- “基本”、“标准”和“高级”层支持 64 位和 32 位应用程序。
- “免费”和“共享”计划层仅支持 32 位应用程序。

### 调试器支持 ###

- “免费”、“共享”和“基本”模式提供每个应用服务计划 1 个连接的调试器支持。
- “标准”和“高级”模式提供每个应用服务计划 5 个并发连接的调试器支持。

## <a name="OtherFeatures"></a>了解其他功能

- 有关应用服务计划中所有其他功能（包括定价和所有用户（包括开发人员）感兴趣的功能）的详细信息，请参阅[应用服务定价详细信息](/pricing/details/web-sites/)。

>[AZURE.NOTE] 如果没有注册 Azure 帐户，但是想要使用 Azure App Service，请转到[试用应用服务](https://tryappservice.azure.com/)，可以在应用服务中立即创建一个临时入门 Web 应用。无需付费，也不需要做出任何承诺。

## <a name="Next Steps"></a>后续步骤

- 若要开始使用 Azure，请参阅 [Azure 试用版](/pricing/1rmb-trial/)。
- 有关定价、支持和 SLA 的信息，请访问以下链接。

	[数据传输定价详细信息](/pricing/details/data-transfer/)

	[Azure 支持计划](/support/plans/)

	[服务级别协议](/support/legal/sla/)

	[SQL 数据库定价详细信息](/pricing/details/sql-database/)

	[Azure 的虚拟机和云服务大小][vmsizes]

	[应用服务定价详细信息](/pricing/details/app-service/)

	[应用服务定价详细信息 - SSL 连接](/pricing/details/web-sites/)

- 有关 Azure App Service 最佳实践（包括构建可缩放、有弹性的体系结构）的信息，请参阅 [Best Practices: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)（最佳实践：Azure App Service Web 应用）。

- 有关缩放应用服务应用的视频，请参阅以下资源：

	- [何时扩展 Azure 网站 - 使用 Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [自动缩放 Azure 网站、CPU 或按计划 - 使用 Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Azure 网站如何伸缩 - 使用 Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)


<!-- LINKS -->
[vmsizes]: /pricing/details/app-service/
[SQLaccountsbilling]: /pricing/details/sql-database/
[azuresubscriptions]: https://manage.windowsazure.cn
[portal]: https://portal.azure.cn/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!---HONumber=Mooncake_Quality_Review_1118_2016-->