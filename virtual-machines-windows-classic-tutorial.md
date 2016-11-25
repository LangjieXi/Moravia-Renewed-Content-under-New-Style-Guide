<properties
	pageTitle="在经典管理门户中创建运行 Windows 的 VM | Azure"
	description="在 Azure 经典管理门户中创建 Windows 虚拟机。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>  


<tags
	ms.service="virtual-machines-windows"
	ms.date="07/12/2016"
	wacn.date=""/>  


# 在 Azure 经典管理门户中创建运行 Windows 的虚拟机

> [AZURE.SELECTOR]
- [Azure 经典管理门户](/documentation/articles/virtual-machines-windows-classic-tutorial/)
- [PowerShell：经典部署](/documentation/articles/virtual-machines-windows-classic-create-powershell/)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)] 了解如何[使用资源管理器部署模型执行这些步骤](/documentation/articles/virtual-machines-windows-hero-tutorial/)。

本教程演示如何在 Azure 经典管理门户中轻松创建运行 Windows 的 Azure 虚拟机 (VM)。我们将使用 Windows Server 映像作为示例，但这只是 Azure 提供的众多映像的其中一个。请注意，映像的选择取决于订阅。例如，Windows 桌面映像适用于 MSDN 订户。

本部分演示如何使用 Azure 经典管理门户中的“从库中”选项创建虚拟机。此选项提供的配置选项多于“快速创建”选项。例如，如果你想要将虚拟机加入到虚拟网络，则需要使用“从库中”选项。

你也可以使用[自己的映像](/documentation/articles/virtual-machines-windows-classic-createupload-vhd/)创建 VM。若要了解此方法和其他方法，请参阅[创建 Windows 虚拟机的不同方式](/documentation/articles/virtual-machines-windows-creation-choices/)。
## <a id="createvirtualmachine"></a> 创建虚拟机

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-windowsvm.md)]

## 后续步骤

- 登录到虚拟机。有关说明，请参阅[登录到运行 Windows Server 的虚拟机](/documentation/articles/virtual-machines-windows-classic-connect-logon/)。

- 附加磁盘以存储数据。你可以附加空磁盘和包含数据的磁盘。有关说明，请参阅[将数据磁盘附加到使用经典部署模型创建的 Windows 虚拟机](/documentation/articles/virtual-machines-windows-classic-attach-disk/)。

<!---HONumber=Mooncake_Quality_Review_1118_2016-->