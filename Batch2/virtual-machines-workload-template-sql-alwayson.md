<properties
	pageTitle="使用 Azure Resource Manager 模板部署 SQL Server AlwaysOn | Microsoft Azure"
	description="使用 Resource Manager 模板和 Azure 门户、Azure PowerShell 或 Azure CLI，轻松部署 5 个支持 SQL Server AlwaysOn 的服务器。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="10/08/2015"
	wacn.date=""/>

# 使用 Azure Resource Manager 模板部署 SQL Server AlwaysOn

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-rm-include.md)]经典部署模型。你无法使用经典部署模型创建此资源。

根据本文中的说明，使用 Azure Resource Manager 模板部署 SQL Server AlwaysOn。此模板将在 2 个不同子网上的新虚拟网络中创建 5 个虚拟机。

![](./media/virtual-machines-workload-template-sql-alwayson/five-server-sqlao.png)

可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 运行模板。

## Azure 门户

要使用 Azure Resource Manager 模板和 Azure 门户部署此工作负荷，请单击[此处](https://manage.windowsazure.cn#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsql-server-2014-alwayson-dsc%2Fazuredeploy.json)。

![](./media/virtual-machines-workload-template-sql-alwayson/azure-portal-template.png)

1.	在“模板”窗格中，单击“保存”。
2.	单击“参数”。在“参数”窗格上，输入新值、从允许的值中选择，或者接受默认值，然后单击“确定”。
3.	如有需要，可单击“订阅”并选择正确的 Azure 订阅。
4.	单击“资源组”并选择现有的资源组。或者，单击“或新建”为此工作负荷创建新的资源组。
5.	如有需要，可单击“资源组位置”并选择正确的 Azure 位置。
6.	如有需要，可单击“法律条款”以查看使用模板的条款和协议。
7.	单击“创建”。

根据具体模板，Azure 可能需花费一些时间生成工作负荷。当模板执行完成时，你的现有或新资源组中将出现一个包含 5 个服务器的新 SQL Server AlwaysOn 配置。

## Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../includes/powershell-preview-inline-include.md)]

在以下命令集中填写 Azure 部署名称、新的资源组名称以及 Azure 数据中心位置。删除引号内的所有内容，包括 < and > 字符。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

下面是一个示例。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

接下来，在 Azure PowerShell 提示符下运行你的命令块。

当你运行 **New-AzureRmResourceGroupDeployment** 命令时，系统会提示你提供一系列参数的值。指定所有参数值后，**New-AzureRmResourceGroupDeployment** 将创建和配置虚拟机。

当模板执行完成时，你的新资源组中将出现一个包含 5 个服务器的新 SQL Server AlwaysOn 配置。

## Azure CLI

在开始之前，请确保安装了正确版本的 Azure CLI 且已登录，并切换到新的“资源管理器”模式。有关详细信息，请单击[此处](/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli#getting-ready)。

首先，创建新的资源组。使用以下命令并指定组名称，以及要向其中部署的 Azure 数据中心位置。

	azure group create <group name> <location>

接下来，使用以下命令并指定新资源组的名称以及 Azure 部署的名称。

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json <group name> <deployment name>

下面是一个示例。

	azure group create sqlao eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json sqlao sqldevtest

当你运行 **azure group deployment create** 命令时，系统会提示你提供一系列参数的值。指定所有参数值后，Azure 将创建和配置虚拟机。

当模板执行完成时，你的新资源组中将出现一个包含 5 个服务器的新 SQL Server AlwaysOn 配置。


## 其他资源

[使用 Azure Resource Manager 模板和 Azure PowerShell 部署和管理虚拟机](/documentation/articles/virtual-machines-deploy-rmtemplates-powershell)

[Azure Resource Manager 中的 Azure 计算、网络和存储提供程序](/documentation/articles/virtual-machines-azurerm-versus-azuresm)

[Azure Resource Manager 概述](/documentation/articles/resource-group-overview)

[使用 Azure Resource Manager 模板和 Azure CLI 部署和管理虚拟机](/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli)

[虚拟机文档](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure)

<!---HONumber=Mooncake_Quality_Review_1202_2016-->