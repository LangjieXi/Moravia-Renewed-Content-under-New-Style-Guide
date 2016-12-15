<!-- not suitable for Mooncake -->

<properties
   pageTitle="使用模板部署常用应用程序框架 | Azure"
   description="使用 Azure Resource Manager 模板创建常用应用程序框架，以便安装 Active Directory、Docker，等等。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines"
	ms.date="02/03/2016"
	wacn.date=""/>

# 使用 Azure Resource Manager 模板部署常用应用程序框架

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-rm-include.md)]经典部署模型。

工作负荷通常要求多项资源按设计运行。利用 Azure Resource Manager 模板，你不仅可以定义应用程序的配置方式，而且可以定义资源的部署方式，以便为配置的应用程序提供支持。本文介绍库中最常用的模板以及如何使用 Azure 门户、Azure PowerShell 或 Azure CLI 来部署它们。

## 应用程序

在此表中，你可以找到有关模板中所用参数的详细信息，可以在部署模板之前对其进行检查，也可以直接从 Azure 门户部署模板。

| 应用程序 | 了解详细信息 | 查看模板 | 立即部署 |
|:---|:---:|:---:|:---:|
| Active Directory | [库](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Apache | [库](https://azure.microsoft.com/documentation/templates/apache2-on-ubuntu-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/apache2-on-ubuntu-vm) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapache2-on-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
| Couchbase | [库](https://azure.microsoft.com/documentation/templates/couchbase-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/couchbase-on-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcouchbase-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| DataStax | [库](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/datastax-on-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdatastax-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Django | [库](https://azure.microsoft.com/documentation/templates/django-app/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/django-app) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdjango-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Docker | [库](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdocker-simple-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Elasticsearch | [库](https://azure.microsoft.com/documentation/templates/elasticsearch/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Felasticsearch%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Jenkins | [库](https://azure.microsoft.com/documentation/templates/jenkins-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/jenkins-on-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fjenkins-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Kafka | [库](https://azure.microsoft.com/documentation/templates/kafka-ubuntu-multidisks/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/kafka-on-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%kafka-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| LAMP | [库](https://azure.microsoft.com/documentation/templates/lamp-app/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Flamp-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| MongoDB | [库](https://azure.microsoft.com/documentation/templates/mongodb-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fmongodb-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Redis | [库](https://azure.microsoft.com/documentation/templates/redis-high-availability/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/redis-high-availability) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fredis-high-availability%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| SharePoint | [库](https://azure.microsoft.com/documentation/templates/sharepoint-three-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-three-vm) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Spark | [库](https://azure.microsoft.com/documentation/templates/spark-ubuntu-multidisks/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/spark-ubuntu-multidisks) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fspark-ubuntu-multidisks%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Tomcat | [库](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/openjdk-tomcat-ubuntu-vm) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fopenjdk-tomcat-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| WordPress | [库](https://azure.microsoft.com/documentation/templates/wordpress-single-vm-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/wordpress-single-vm-ubuntu) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwordpress-single-vm-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| ZooKeeper | [库](https://azure.microsoft.com/documentation/templates/zookeeper-cluster-ubuntu-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/zookeeper-cluster-ubuntu-vm) | <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fzookeeper-cluster-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |

除了这些模板，你也可以在[库模板](https://azure.microsoft.com/documentation/templates/)中搜索模板。

## Azure 门户

使用 Azure 门户部署模板很容易，只需向其发送 URL 即可。需要提供模板文件的名称才能进行部署。查找名称时，你可以在模板库中查找页面，也可以在 Github 存储库中查找。将此 URL 中的 {template name} 更改为要部署的模板的名称，然后将其输入浏览器中：

    https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F{template name}%2Fazuredeploy.json

此时会看到“自定义部署”边栏选项卡：

![](./media/virtual-machines-workload-template-ad-domain/azure-portal-template.png)

1.	在“模板”窗格中，单击“保存”。
2.	单击“参数”。在“参数”窗格上，输入新值、从允许的值中选择，或者接受默认值，然后单击“确定”。
3.	如有需要，可单击“订阅”并选择正确的 Azure 订阅。
4.	单击“资源组”并选择现有的资源组。或者，单击“或新建”为此部署创建新的资源组。
5.	如有需要，可单击“位置”并选择正确的 Azure 位置。
6.	如有需要，可单击“法律条款”以查看使用模板的条款条件和协议。
7.	单击“创建”。

根据具体模板，Azure 可能需花费一些时间部署资源。

## Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../includes/powershell-preview-inline-include.md)]

将括号中的文本替换为资源组名称、位置、部署名称和模板名称后，运行以下命令即可创建资源组和部署：

	New-AzureRmResourceGroup -Name {resource-group-name} -Location {location}
	New-AzureRmResourceGroupDeployment -Name {deployment-name} -ResourceGroupName {resource-group-name} -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/{template-name}/azuredeploy.json"

运行 **New-AzureRmResourceGroupDeployment** 命令时，系统会提示你输入模板中参数的值。根据具体模板，Azure 可能需花费一些时间部署资源。

## Azure CLI

[安装 Azure CLI](/documentation/articles/xplat-cli-install)，登录，并确保启用资源管理器命令。有关如何执行此操作的信息，请参阅[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure Resource Manager 配合使用](/documentation/articles/xplat-cli-azure-resource-manager)。

将括号中的文本替换为资源组名称、位置、部署名称和模板名称后，运行以下命令即可创建资源组和部署：

	azure group create {resource-group-name} {location}
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/{template-name}/azuredeploy.json {resource-group-name} {deployment-name}

运行 **azure group deployment create** 命令时，系统会提示你输入模板中参数的值。根据具体模板，Azure 可能需花费一些时间部署资源。

## 后续步骤

发现 [GitHub](https://github.com/Azure/azure-quickstart-templates) 上可自由应用的所有模板。

了解有关 [Azure Resource Manager](/documentation/articles/resource-group-template-deploy) 的详细信息。

<!---HONumber=Mooncake_Quality_Review_1202_2016-->