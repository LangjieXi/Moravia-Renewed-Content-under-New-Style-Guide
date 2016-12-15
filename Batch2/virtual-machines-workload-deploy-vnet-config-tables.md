<properties
	pageTitle="使用配置表的虚拟网络 | Microsoft Azure"
	description="了解如何使用预先确定的设置通过配置表中的设置配置跨界 Azure 虚拟网络。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.date="10/20/2015"
	wacn.date=""/>

# 使用配置表创建跨界虚拟网络

> [AZURE.IMPORTANT]Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器和经典](/documentation/articles/resource-manager-deployment-model/)。本文介绍使用经典部署模型。Azure 建议大多数新部署使用资源管理器部署模型。

本主题将指导你逐步完成使用以前在下列一组配置表中指定的设置创建跨界虚拟网络的过程：

- 表 V：跨界虚拟网络配置
- 表 S：虚拟网络中的子网
- 表 D：本地 DNS 服务器
- 表 L：本地网络的地址前缀

这些表通常在介绍 Azure 中 IT 工作负荷的配置，并涉及跨界虚拟网络的主题中填写。有关示例，请参阅[阶段 1：配置 Azure](/documentation/articles/virtual-machines-workload-intranet-sharepoint-phase1/)。

以下过程参考了这些表中的信息，以指导你完成虚拟网络配置过程。如果你尚未在另一个主题中指定这些表中的设置，但仍要配置跨界虚拟网络，请参阅[配置与 Azure 虚拟网络的跨界站点到站点连接](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

> [AZURE.NOTE]此过程将指导你逐步完成创建使用站点到站点 VPN 连接的虚拟网络的过程。有关将 Azure ExpressRoute 用于站点到站点连接的信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## 使用配置表设置创建新的跨界 Azure 虚拟网络

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。
2. 在任务栏中，单击“新建”>“网络服务”>“虚拟网络”>“自定义创建”。
3. 在“虚拟网络详细信息”页上：
	- 在“名称”中，键入表 V 的项目 1 中的名称。
	- 在“位置”中，选择表 V 的项目 2 中的区域。
4. 单击“下一步”箭头以继续。
5. 在“DNS 服务器和 VPN 连接”页上：
	- 在“DNS 服务器”中，针对表 D 中的每个条目，配置本地 DNS 服务器的友好名称和 IP 地址。
	- 在“站点到站点连接”中，选择“配置站点到站点 VPN”。
	- 如果已配置本地网络并想要使用它，请在“本地网络”中选择其名称。如果要创建新的本地网络，请在“本地网络”中选择“指定新的本地网络”。
	- 如果尚未为你的订阅配置本地网络，则不会看到“本地网络”字段。
6. 单击“下一步”箭头以继续。
7. 在“站点到站点连接”页上（如果在步骤 5 中选择了“指定新的本地网络”）：
	- 在“名称”中，键入表 V 的项目 3 中的名称（本地网络名称）。
	- 在“VPN 设备 IP 地址”中，键入表 V 的项目 4 中的地址。
	- 在“地址空间”中，对于表 L 中的每个条目，就前缀而言输入你的组织网络的 IP 地址空间（在“起始 IP”中）和前缀长度（在“CIDR (地址计数)”中）。
8. 单击“下一步”箭头以继续。
9. 在“虚拟网络地址空间”页上：
	- 在“地址空间”中，就前缀而言输入表 V 的项目 5 中虚拟网络的专用 IP 地址空间（在“起始 IP”中）和前缀长度（在“CIDR (地址计数)”中）。
	- 在“子网”中，对于表 S 中的每个条目：
		- 在“子网”列中键入子网的名称，以覆盖默认名称（如果需要）。
		- 就前缀而言键入子网的专用 IP 地址空间（在“起始 IP”中）和前缀长度（在“CIDR (地址计数)”中）。
	- 单击“添加网关子网”。
10. 单击复选标记以完成配置。

## 其他资源

[虚拟网络概述](../virtual-network/virtual-networks-overview.md)

[虚拟网络配置任务](../documentation/services/virtual-machines/)

[配置与 Azure 虚拟网络的跨界站点到站点连接](../vpn-gateway/vpn-gateway-site-to-site-create.md)

<!---HONumber=Mooncake_Quality_Review_1202_2016-->