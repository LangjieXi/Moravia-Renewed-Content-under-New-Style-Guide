<!-- Ibiza portal: not suitable for Mooncake -->

<properties
   pageTitle="使用 ARM 模板创建虚拟网络 | Azure"
   description="了解如何使用 ARM 模板|资源管理器创建虚拟网络。"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
	ms.service="virtual-network"
	ms.date="03/15/2016"
	wacn.date=""/>

# 使用 ARM 模板创建虚拟网络

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

>[AZURE.IMPORTANT]在使用 Azure 资源之前，请务必了解 Azure 当前使用两种部署模型：资源管理器部署模型和经典部署模型。在使用任何 Azure 资源之前，请确保你了解[部署模型和工具](/documentation/articles/azure-classic-rm/)。可以通过单击本文顶部的选项卡来查看不同工具的文档。本文档介绍如何使用资源管理器部署模型创建 VNet。你还可以[使用经典部署模型创建虚拟网络](/documentation/articles/virtual-networks-create-vnet-classic-pportal/)。

你将了解如何从 GitHub 下载并修改现有 ARM 模板，以及如何通过 GitHub、PowerShell 和 Azure CLI 部署该模板。

如果你只是直接从 GitHub 部署 ARM 模板，而不进行任何更改，请跳到[从 github 部署模板](#deploy-the-arm-template-by-using-click-to-deploy)。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!---HONumber=69-->