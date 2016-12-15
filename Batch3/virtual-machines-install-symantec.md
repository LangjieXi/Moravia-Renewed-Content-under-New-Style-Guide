<properties
	pageTitle="在 VM 上安装 Symantec Endpoint Protection | Microsoft Azure"
	description="了解如何在使用经典部署模型创建的新的或现有的 Azure VM 上安装和配置 Symantec Endpoint Protection 安全扩展插件。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>  


<tags
	ms.service="virtual-machines"
	ms.date="10/14/2015"
	wacn.date=""/>

# 如何在 Windows VM 上安装和配置 Symantec Endpoint Protection

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)] Resource Manager 模型。


本文演示如何在运行 Windows Server 的新的或现有虚拟机 (VM) 上安装和配置 Symantec Endpoint Protection。这是完整的客户端，其中包括病毒和间谍软件防护、防火墙和入侵防御等服务。

该客户端通过 VM 代理作为安全扩展插件进行安装。在新虚拟机上，将随终结点客户端一起安装代理。在未安装代理的现有虚拟机上，将需要先下载并安装代理。本文介绍这两种情况。

如果你拥有针对本地解决方案的 Symantec 现有订阅，那么可以用它来保护你的 Azure 虚拟机。如果你还不是 Symantec 的客户，那么可以注册试用订阅。有关此解决方案的详细信息，请参阅 [Microsoft Azure 平台上的 Symantec Endpoint Protection][Symantec]。如果你已经是 Symantec 客户，则还可查看此页上指向有关安装客户端的许可信息和说明的链接。

## 在新虚拟机上安装 Symantec Endpoint Protection

使用“从库中”选项创建虚拟机时，可以从[Azure 管理门户][Portal]安装 VM 代理和 Symantec 安全扩展插件。如果要创建的是单个虚拟机，那么可以使用这种方法轻松地添加来自 Symantec 的保护。

此**“从库中”**选项会打开帮助你设置虚拟机的向导。该向导的最后一页用于安装 VM 代理和 Symantec 安全扩展插件。

有关常规说明，请参阅[创建运行 Windows Server 的虚拟机][Create]。进入向导的最后一页时：

1.	在“VM 代理”下，“安装 VM 代理”应该已经选中。

2.	在“安全扩展插件”下，选中“Symantec Endpoint Protection”。


	![安装 VM 代理和 Endpoint Protection 客户端](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	单击页面底部的复选标记可创建虚拟机。

## 在现有虚拟机上安装 Symantec Endpoint Protection

在开始之前，你需要具备以下项：

- 在工作计算机上安装 Azure PowerShell 模块 0.8.2 版或更高版本。可以使用 **Get-Module azure | format-table version** 命令查看已安装的 Azure PowerShell 的版本。有关说明以及指向最新版本的链接，请参阅[如何安装和配置 Azure PowerShell][PS]。确保登录你的 Azure 订阅。

- 在 Azure 虚拟机上运行的 VM 代理。

首先，请验证虚拟机上是否已安装 VM 代理。填写云服务名称和虚拟机名称，然后在管理员级别的 Azure PowerShell 命令提示符下运行以下命令。替换引号内的所有内容，包括 < and > 字符。

> [AZURE.TIP] 如果你不知道云服务名称和虚拟机名称，运行 **Get-AzureVM** 可列出当前订阅中所有虚拟机的名称。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

如果 **write-host** 命令显示 **True**，则已安装 VM 代理。如果该命令显示 **False**，则请参阅 Azure 博客文章 [VM 代理和扩展 - 第 2 部分][Agent]中的说明和下载链接。

如果已安装 VM 代理，则运行以下命令安装 Symantec Endpoint Protection 代理。

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

若要验证 Symantec 安全扩展插件是否已安装并处于最新状态：

1.	登录到虚拟机。有关说明，请参阅[如何登录到运行 Windows Server 的虚拟机][Logon]。
2.	对于 Windows Server 2008 R2，请单击“开始”>“Symantec Endpoint Protection”。对于 Windows Server 2012 或 Windows Server 2012 R2，在开始屏幕键入 **Symantec**，然后单击“Symantec Endpoint Protection”。
3.	在“状态 - Symantec Endpoint Protection”窗口的“状态”选项卡中，根据需要应用更新或重启。

## 其他资源

[如何登录到运行 Windows Server 的虚拟机][Logon]

[Azure VM 扩展和功能][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.cn

[Create]: virtual-machines-windows-tutorial-classic-portal.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=Mooncake_Quality_Review_1215_2016-->