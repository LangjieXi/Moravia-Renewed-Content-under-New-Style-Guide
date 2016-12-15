<properties
   pageTitle="如何标记 VM | Microsoft Azure"
   description="了解如何标记使用 Resource Manager 部署模型创建的 Azure 虚拟机。"
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>  


<tags
   ms.service="virtual-machines"
   ms.date="11/10/2015"
   wacn.date=""/>

# 如何在 Azure 中标记虚拟机

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-rm-include.md)] 经典部署模型。


本文介绍在 Azure 中通过 Azure Resource Manager 标记虚拟机的不同方式。标记是用户定义的键/值对，可直接放置在资源或资源组中。目前，Azure 支持每个资源和资源组最多 15 个标记。标记可以在创建时放置在资源中，也可以添加到现有资源中。请注意，只有通过 Azure Resource Manager 创建的资源才支持标记。

## 通过模板标记虚拟机

首先，让我们看一下如何通过模板进行标记。[此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm)将标记放置在以下资源中：计算（虚拟机）、存储（存储帐户）和网络（公共 IP 地址、虚拟网络和网络接口）。

单击[](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm)“模板链接”中的“部署至 Azure”按钮。此操作将导航到 [Azure 预览门户](https://manage.windowsazure.cn)，可在其中部署此模板。

![使用标记进行简单部署](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

此模板包括以下标记：*Department*、*Application* 和 *Created By*。如果想要不同的标记名称，则可以直接在模板中添加/编辑这些标记。

![模板中的 Azure 标记](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

如你所见，标记被定义为键/值对，用冒号 (:) 分隔。必须按以下格式定义标记：

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

完成编辑后，使用选择的标记保存模板文件。

接下来，在“编辑参数”部分中，可以填写标记的值。

![通过 Azure 门户编辑标记](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

单击“创建”，使用标记值部署此模板。


## 通过门户进行标记

使用标记创建资源后，可以在门户中查看、添加和删除该标记。

选择标记图标，以查看标记：

![Azure 门户中的标记图标](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

通过定义你自己的键/值对，使用门户添加新标记并进行保存。

![通过 Azure 门户添加新标记](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

新标记现在应在资源的标记列表中显示。

![Azure 门户中保存的新标记](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## 使用 PowerShell 进行标记

若要通过 PowerShell 创建、添加和删除标记，首先需要[使用 Azure Resource Manager 设置 PowerShell 环境][]。一旦完成设置后，就可以在创建计算、网络和存储资源时或创建之后通过 PowerShell 将标记放置在这些资源中。本文章将重点说明如何查看/编辑虚拟机上放置的标记。

首先，通过 `Get-AzureVM`cmdlet 导航到虚拟机。

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

如果虚拟机已包含标记，你将在资源中看到所有标记：

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

如果想要通过 PowerShell 添加标记，可以使用 `Set-AzureResource` 命令。请注意，通过 PowerShell 更新时，标记会作为整体进行更新。因此，如果要向已具有标记的资源添加标记，需要包括想要放置在资源中的所有标记。以下示例说明了如何通过 PowerShell Cmdlet 将其他标记添加到资源中。

第一个 cmdlet 可使用 `Get-AzureResource` 和 `Tags` 函数将放置在 *MyWindowsVM* 中的所有标记设置到 *tags* 变量中。请注意，参数 `ApiVersion` 是可选的；如果未指定，则使用资源提供程序的最新 API 版本。

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

第二个命令可显示给定变量的标记。

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

第三个命令可将其他标记添加到 *tags* 变量。请注意，使用 **+=** 可将新的键/值对追加到 *tags* 列表。

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

第四个命令可将 *tags* 变量中定义的所有标记放置到给定资源中。在这种情况下，它是 MyWindowsVM。

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

第五个命令可显示资源上的所有标记。如你所见，*Location* 现在定义为值为 *MyLocation* 的标记。

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

若要了解有关通过 PowerShell 进行标记的详细信息，请查看 [Azure 资源 Cmdlet][]。


## 使用 Azure CLI 进行标记

还支持通过 Azure CLI 对已创建的资源进行标记。若要开始，请设置 [Azure CLI 环境][]。通过 Azure CLI 登录到订阅，并切换到 ARM 模式。你可以使用以下命令查看给定虚拟机的所有属性，包括标记：

        azure vm show -g MyResourceGroup -n MyVM

与 PowerShell 不同，如果要将标记添加到已包含标记的资源中，不必在使用 `azure vm set` 命令之前指定所有标记（旧标记和新标记）。相反，使用此命令可以将标记追加到资源中。若要通过 Azure CLI 添加新的 VM 标记，可以使用 `azure vm set` 命令以及标记参数 **-t**：

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

若要删除所有标记，可以在 `azure vm set` 命令中使用 **–T** 参数。

        azure vm set – g MyResourceGroup –n MyVM -T


我们现已通过 PowerShell、Azure CLI 和门户将标记应用到资源中，接下来看一看计费门户中的使用情况详细信息，以查看其中的标记。


## 查看使用情况详细信息中的标记

通过 Azure Resource Manager 放置在计算、网络和存储资源中的标记将填充在[计费门户](https://account.windowsazure.cn/)中的使用情况详细信息中。

单击“下载使用情况详细信息”，以查看订阅的使用情况详细信息。

![Azure 门户中的使用情况详细信息](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

选择帐单和**版本 2** 使用情况详细信息：

![Azure 门户中的版本 2 预览使用情况详细信息](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

在使用情况详细信息中，可以在“标记”列中看到所有标记：

![Azure 门户中的“标记”列](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

通过分析这些标记以及使用情况，组织将能够获得对消耗数据的全新见解。


## 其他资源

* [Azure Resource Manager 概述][]
* [使用标记组织 Azure 资源][]
* [了解 Azure 帐单][]
* [深入了解 Microsoft Azure 资源消耗][]




[使用 Azure Resource Manager 设置 PowerShell 环境]: ../powershell-azure-resource-manager.md
[Azure 资源 Cmdlet]: https://msdn.microsoft.com/zh-cn/library/azure/dn757692.aspx
[Azure CLI 环境]: ./xplat-cli-azure-resource-manager.md
[Azure Resource Manager 概述]: ../resource-group-overview.md
[使用标记组织 Azure 资源]: ../resource-group-using-tags.md
[了解 Azure 帐单]: ../billing-understand-your-bill.md
[深入了解 Microsoft Azure 资源消耗]: ../billing-usage-rate-card-overview.md

<!---HONumber=Mooncake_Quality_Review_1215_2016-->