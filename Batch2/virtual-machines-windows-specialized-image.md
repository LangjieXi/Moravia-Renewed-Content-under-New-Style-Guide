<!-- ARM: tested -->

<properties
	pageTitle="创建 Windows VM 的副本 | Azure"
	description="了解如何通过创建一个 *专用映像*，在资源管理器部署模型中创建运行 Windows 的 Azure 虚拟机的副本。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.date="04/26/2016"
	wacn.date=""/>

# 在 Azure Resource Manager 部署模型中创建 Windows 虚拟机的副本


本文说明如何创建运行 Windows 的 Azure 虚拟机 (VM) 的副本。具体而言，它介绍如何使用 Azure PowerShell 和 Azure 门户预览在 Azure Resource Manager 部署模型中执行此操作。其中说明了如何创建 Azure VM 的*专用*映像，此映像可维护用户帐户和其他来自原始 VM 的状态数据。使用专用映像可将 Windows VM 从经典部署模型移植到资源管理器部署模型，或为创建于资源管理器部署模型中的 Windows VM 创建备份副本。你可以使用此方法来通过操作系统和数据磁盘进行复制，然后设置网络资源以创建新虚拟机。

如果需要创建相似 Windows VM 的批量部署，则应该使用*通用*映像。相关信息请参阅[如何捕获 Windows 虚拟机](/documentation/articles/virtual-machines-windows-capture-image/)。



## 开始之前

在开始执行相关步骤前，请先确保符合以下先决条件：

- 你有使用经典或资源管理器部署模型创建的**运行 Windows 的 Azure 虚拟机**。你已配置操作系统、附加数据磁盘和进行诸如安装所需应用程序之类的其他自定义设置。你将使用此 VM 创建副本。如需有关创建源 VM 的帮助，请参阅[如何使用资源管理器创建 Windows VM](/documentation/articles/virtual-machines-windows-ps-create/)。

- 已在计算机上安装 **Azure PowerShell 1.0（或更高版本）**并且你已登录到 Azure 订阅。有关详细信息，请参阅[如何安装和配置 PowerShell](/documentation/articles/powershell-install-configure/)。

- 你已下载并安装 **AzCopy 工具**。有关此工具的详细信息，请参阅[使用 AzCopy 命令行工具传输数据](/documentation/articles/storage-use-azcopy/)。

- 你有**资源组**、**存储帐户**以及在该资源组中创建的用于将 VHD 复制到的 **Blob 容器**。有关使用现有存储帐户或创建新帐户的步骤，请参阅[创建或查找 Azure 存储帐户](/documentation/articles/virtual-machines-windows-upload-image/#createstorage)。

> [AZURE.NOTE] 对于通过使用两种部署模型之一作为源映像创建的 VM，可以采用类似的步骤。在适用的情况下，本文会注明细微的差异。


## 将 VHD 复制到资源管理器存储帐户


1. 执行以下两个选项之一，以释放源 VM 使用的 VHD：

	- 如果你想要复制源虚拟机，请停止并解除分配该虚拟机。

		- 对于使用经典部署模型创建的 VM，可以使用[门户](https://portal.azure.cn)，然后单击“浏览”>“虚拟机(经典)”> *你的 VM* >“停止”，或使用 PowerShell 命令 `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`。

		- 对于使用资源管理器部署模型创建的 VM，可以登录门户，然后单击“浏览”>“虚拟机”> *你的 VM* >“停止”，或使用 PowerShell 命令 `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`。请注意，门户中的 VM 状态将从“正在运行”更改为“已停止(已解除分配)”。

	- 如果你想要迁移源虚拟机，请删除该 VM 并使用剩余的 VHD。在[门户](https://portal.azure.cn)中浏览到虚拟机，然后单击“删除”。

1. 查找存储帐户的访问密钥，其中包含源 VHD，以及要在其中复制 VHD 以创建新 VM 的存储帐户。要从中复制 VHD 的帐户密钥称为*源密钥*，将它复制到的帐户称为*目标密钥*。有关访问密钥的详细信息，请参阅[关于 Azure 存储帐户](/documentation/articles/storage-create-storage-account/)。

	- 如果源 VM 是使用经典部署模型创建的，请单击“浏览”>“存储帐户(经典)”> *你的存储帐户* >“配置”>“密钥”。复制标记为 **PRIMARY ACCESS KEY** 的密钥。

	- 对于使用资源管理器部署模型所创建的源 VM，或对于要用于新 VM 的存储帐户，请单击“浏览”>“存储帐户”> *你的存储帐户* >“配置”>“访问密钥”。复制标记为 **key1** 的密钥。

1. 获取用于访问源和目标存储帐户的 URL。在门户中，浏览到你的存储帐户并单击“Blob”。然后单击托管源 VHD 的容器（例如，经典部署模型的 *vhds*），或单击要将 VHD 复制到的容器。单击容器的“属性”并复制标记为 **URL** 的文本。你将需要用到源和目标容器的 URL。这些 URL 看起来类似于 `https://myaccount.blob.core.chinacloudapi.cn/mycontainer`。

1. 在本地计算机上打开命令窗口，然后浏览到安装 AzCopy 的文件夹。（路径类似于 *C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy*。） 从该处运行以下命令：</br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] 如上一步所述，你应该使用 AzCopy 分别复制操作系统和数据磁盘。


## 使用复制的 VHD 创建 VM

[AZURE.INCLUDE [arm-api-version-powershell](../includes/arm-api-version-powershell.md)]

通过使用上述步骤中复制的 VHD，现在可以使用 Azure PowerShell 在新虚拟网络中创建基于资源管理器的 Windows VM。VHD 应与要创建的新虚拟机位于同一存储帐户中。


使用类似于下面的脚本为新 VM 设置虚拟网络和 NIC。根据应用程序使用适当的变量（以 **$** 符号表示）值。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


现在，请设置 VM 配置，并使用复制的 VHD 创建新虚拟机。</br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk by using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

	#Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

数据磁盘和操作系统磁盘的 URL 类似于：`https://StorageAccountName.blob.core.chinacloudapi.cn/BlobContainerName/DiskName.vhd`。可通过以下方法在门户上找到此信息：浏览到目标存储容器，单击复制的操作系统或数据 VHD，然后复制 URL 的内容。

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

如果此命令成功，你将看到类似于下面的输出：

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


你应该在 [Azure 门户预览](https://portal.azure.cn)的“浏览”>“虚拟机”下查看新创建的 VM，或者使用以下 PowerShell 命令进行查看：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

若要登录到新虚拟机，请在[门户](https://portal.azure.cn)中浏览到该 VM，单击“连接”，然后打开远程桌面 RDP 文件。使用原始虚拟机的帐户凭据登录到新虚拟机。


## 后续步骤

若要使用 Azure PowerShell 管理新虚拟机，请参阅[使用 Azure Resource Manager 与 PowerShell 来管理虚拟机](/documentation/articles/virtual-machines-windows-ps-manage/)。

<!---HONumber=Mooncake_Quality_Review_1202_2016-->