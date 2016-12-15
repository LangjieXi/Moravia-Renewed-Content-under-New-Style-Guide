<properties
   pageTitle="适用于 Windows Server 的 Azure Hybrid Use Benefit | Azure"
   description="了解如何充分利用 Windows Server 软件保障权益，以将本地许可证带入 Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>  


<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   wacn.date=""
   ms.author="georgem"/>  


# 适用于 Windows Server 的 Azure Hybrid Use Benefit

对于使用含软件保障的 Windows Server 的客户，可将本地 Windows Server 许可证带到 Azure，并以较低的成本在 Azure 中运行 Windows Server VM。Azure Hybrid Use Benefit 可让用户在 Azure 中运行 Windows Server VM，且只需支付基本计算费用。有关详细信息，请参阅 [Azure Hybrid Use Benefit 许可页](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。本文说明如何在 Azure 中部署 Windows Server VM，以利用此许可权益。

> [AZURE.NOTE] 如果要利用 Azure Hybrid Use Benefit，就不能使用 Azure 应用商店映像来部署 Windows Server VM。必须使用 PowerShell 或 Resource Manager 模板部署 VM，从能将 VM 正确注册为符合基本计算费率折扣资格。

## 先决条件
若要让 Azure 中的 Windows Server VM 利用 Azure Hybrid Use Benefit，必须符合以下几项先决条件：

- 已安装 Azure PowerShell 模块
- 已将 Windows Server VHD 上载到 Azure 存储空间

### 安装 Azure PowerShell
确保[已安装并配置最新的 Azure PowerShell](/documentation/articles/powershell-install-configure/)。即使要使用 Resource Manager 模板部署 VM，仍需要安装 Azure PowerShell 才能上载 Windows Server VHD（请参阅以下步骤）。

### 上载 Windows Server VHD

若要在 Azure 中部署 Windows Server VM，必须先创建包含基本 Windows Server 版本的 VHD。必须先通过 Sysprep 妥善准备此 VHD，然后才能将其上载到 Azure。请[详细了解 VHD 要求和 Sysprep 进程](/documentation/articles/virtual-machines-windows-upload-image/)以及[针对服务器角色的 Sysprep 支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。在运行 Sysprep 之前备份 VM。准备好 VHD 之后，即可使用 `Add-AzureRmVhd` cmdlet 将 VHD 上载到 Azure 存储帐户，如下所示：

	Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'

> [AZURE.NOTE] Microsoft SQL Server、SharePoint Server 和 Dynamics 还可以利用软件保障许可。用户仍需要准备 Windows Server 映像，方法是安装应用程序组件，并相应地提供许可证密钥，然后将磁盘映像上载到 Azure。查看相应文档，了解如何针对应用程序运行 Sysprep，例如[使用 Sysprep 安装 SQL Server 的注意事项](https://msdn.microsoft.com/zh-cn/library/ee210754.aspx)或[生成 SharePoint Server 2016 引用映像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)。

还可以进一步了解[将 VHD 上载到 Azure 的过程](/documentation/articles/virtual-machines-windows-upload-image/#upload-the-vm-image-to-your-storage-account)。

> [AZURE.TIP] 本文重点介绍如何部署 Windows Server VM。你也可以使用相同方式部署 Windows 客户端 VM。在以下示例中，请将 `Server` 相应替换为 `Client`。

## 通过 PowerShell 快速入门部署 VM
通过 PowerShell 部署 Windows Server VM 时，可以使用 `-LicenseType` 的附加参数。将 VHD 上载到 Azure 之后，可以使用 `New-AzureRmVM` 创建新 VM 并指定许可类型，如下所示：

	New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "China North" -VM $vm -LicenseType Windows_Server

可以在下面进一步[阅读有关如何通过 PowerShell 在 Azure 中部署 VM 的详细演练](/documentation/articles/virtual-machines-windows-hybrid-use-benefit-licensing/#deploy-windows-server-vm-via-powershell-detailed-walkthrough)，或阅读有关[使用资源管理器和 PowerShell 创建 Windows VM](/documentation/articles/virtual-machines-windows-ps-create/) 的不同步骤的描述性说明。

## 通过资源管理器部署 VM
在 Resource Manager 模板中，可以为 `licenseType` 指定附加参数。请阅读有关[创作 Azure Resource Manager 模板](/documentation/articles/resource-group-authoring-templates/)的详细信息。将 VHD 上载到 Azure 之后，请编辑 Resource Manager 模板，在计算提供程序中加入许可证类型，然后照常部署模板：

	"properties": {  
	   "licenseType": "Windows_Server",
	   "hardwareProfile": {
	        "vmSize": "[variables('vmSize')]"
	   },
 
## 验证 VM 是否正在利用许可权益
通过 PowerShell 或资源管理器部署方法部署 VM 之后，请使用 `Get-AzureRmVM` 验证许可证类型，如下所示：

	Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM

输出与下面类似：

	Type                     : Microsoft.Compute/virtualMachines
	Location                 : chinanorth
	LicenseType              : Windows_Server

以下 VM 在部署时未提供 Azure Hybrid Use Benefit 许可（例如直接从 Azure 库部署 VM），可看出其中的差异：

	Type                     : Microsoft.Compute/virtualMachines
	Location                 : chinanorth
	LicenseType              : 

## <a name="deploy-windows-server-vm-via-powershell-detailed-walkthrough"></a> PowerShell 详细演练

以下的详细 PowerShell 步骤演示了 VM 的完整部署。可以在[使用 Resource Manager 和 PowerShell 创建 Windows VM](/documentation/articles/virtual-machines-windows-ps-create/) 中了解更多上下文，包括实际的 cmdlet 和所创建的不同组件。你可以逐步执行创建资源组、存储帐户和虚拟网络，然后定义 VM 并完成创建 VM 的整个过程。
 
首先请安全获取凭据，并设置位置和资源组名称：

	$cred = Get-Credential
	$location = "China North"
	$resourceGroupName = "TestLicensing"

创建一个公共 IP：

	$publicIPName = "testlicensingpublicip"
	$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic

定义子网、NIC 和 VNET：

	$subnetName = "testlicensingsubnet"
	$nicName = "testlicensingnic"
	$vnetName = "testlicensingvnet"
	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id

为 VM 命名并创建 VM 配置：

	$vmName = "testlicensing"
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"

定义 OS：

	$computerName = "testlicensing"
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

将 NIC 添加到 VM：

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

定义要使用的存储帐户：

	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing

上载准备妥当的 VHD，并附加到 VM 以供使用：

	$osDiskName = "licensing.vhd"
	$osDiskUri = '{0}vhds/{1}{2}' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
	$urlOfUploadedImageVhd = "https://testlicensing.blob.core.chinacloudapi.cn/vhd/licensing.vhd"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

最后，创建 VM 并定义许可类型，以利用 Azure Hybrid Use Benefit：

	New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server

## 后续步骤

了解有关[使用 Azure Resource Manager 模板](/documentation/articles/resource-group-overview/)的详细信息。

<!---HONumber=Mooncake_Quality_Review_1202_2016-->