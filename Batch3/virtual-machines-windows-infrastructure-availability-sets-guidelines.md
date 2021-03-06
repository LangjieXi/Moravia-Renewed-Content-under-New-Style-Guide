<properties
	pageTitle="可用性集准则 |Azure"
	description="了解用于在 Azure 基础结构服务中部署可用性集的关键设计和实施准则。"
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>  


<tags
	ms.service="virtual-machines-windows"
	ms.date="06/30/2016"
	wacn.date=""/>

# 可用性集准则

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

本文重点介绍可用性集的必要规划步骤，按照这些步骤可确保应用程序在计划内或计划外事件期间保持可访问性。

## 可用性集的实施准则

决策：

- 应用程序基础结构中的各种角色和层需要多少可用性集？

任务：

- 定义每个应用程序层中所需的的 VM 数量。
- 确定是否需要调整将用于应用程序的容错域或更新域数量。
- 使用命名约定和其中将驻留的 VM 定义所需可用性集。一台 VM 只能驻留在一个可用性集内。

## 可用性集

在 Azure 中，可将虚拟机 (VM) 置于名为可用性集的逻辑分组中。在可用性集内创建 VM 时，Azure 平台将在底层基础结构内分布放置这些 VM。如果 Azure 平台发生计划内维护事件或者底层硬件/基础结构发生故障，使用可用性集将确保至少一台 VM 保持运行。

最佳做法是，不要将应用程序驻留在单个 VM 上。当 Azure 平台发生计划内或计划外事件时，包含单个 VM 的可用性集得不到任何保护。[Azure SLA](/support/sla/virtual-machines) 要求一个可用性集内有两台或更多 VM，以便在底层基础结构内分布 VM。

Azure 中的底层基础结构分为更新域和容错域。这些域是按将共享公用更新周期或共享相似物理基础结构（如电源和网络）的主机定义的。Azure 将自动跨域分布可用性集中的 VM，以维持可用性和容错能力。根据应用程序的大小和可用性集内的 VM 数，可以调整要使用的域的数目。你可以详细阅读[管理更新域和容错域的可用性和使用](/documentation/articles/virtual-machines-windows-manage-availability/)。

设计应用程序基础结构时，还应规划好将使用的应用程序层。将服务于同一目的的 VM 分为一组，放到可用性集中，例如用于容纳运行 IIS 的前端 VM 的可用性集。为运行 SQL Server 的后端 VM 创建单独的可用性集。这么做的目的是确保应用程序的每个组件都受到可用性集的保护，且至少一个实例将始终保持运行。

可对每个应用程序层利用负载均衡器，使其与可用性集一起工作，并确保始终可将流量路由到正在运行的实例。若不使用负载均衡器，VM 可能会在计划内和计划外维护事件中继续运行，但如果主 VM 不可用，最终用户可能无法解决这些问题。


## 后续步骤
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=Mooncake_Quality_Review_1215_2016-->