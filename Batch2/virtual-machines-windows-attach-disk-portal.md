<properties
	pageTitle="将数据磁盘附加到 Windows VM |Azure"
	description="如何使用资源管理器部署模型在 Azure 门户中将新磁盘或现有数据磁盘附加到 Windows VM。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.date="03/25/2016"
	wacn.date=""/>

# 如何在 Azure 门户中将数据磁盘附加到 Windows VM

本文介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到 Windows 虚拟机。你也可以[在 Azure 门户中将磁盘数据附加到 Linux VM](/documentation/articles/virtual-machines-linux-attach-disk-portal)。在开始之前，请查看以下提示：

- 虚拟机的大小决定了可以附加多少个磁盘。有关详细信息，请参阅[虚拟机大小](/documentation/articles/virtual-machines-windows-sizes)。
- 要使用高级存储，需要使用 DS 系列或 GS 系列虚拟机。可以用高级存储帐户和标准存储帐户将磁盘用于这些虚拟机。高级存储只在某些区域可用。有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](/documentation/articles/storage-premium-storage)。
- 附加到虚拟机的磁盘实际上是 Azure 存储帐户中的 .vhd 文件。有关详细信息，请参阅[关于虚拟机的磁盘和 VHD](/documentation/articles/virtual-machines-windows-about-disks-vhds)。
- 对于新磁盘，不需要首先进行创建，因为 Azure 将在附加磁盘时创建该磁盘。
- 对于现有磁盘，Azure 存储帐户中必须要有可用的 .vhd 文件。你可以使用已经存在的 .vhd（如果该磁盘没有附加到另一虚拟机），也可以将自己的 .vhd 文件上载到存储帐户。

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>如何：在 Windows Server 中初始化新的数据磁盘

1. 连接到虚拟机。有关说明，请参阅[如何连接并登录到运行 Windows Server 的 Azure 虚拟机](/documentation/articles/virtual-machines-windows-connect-logon)。

2. 登录虚拟机后，打开“服务器管理器”。在左窗格中，选择“文件和存储服务”。

	![打开服务器管理器](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. 展开菜单并选择“磁盘”。

4. “磁盘”部分会列出磁盘。在大多数情况下，会有磁盘 0、磁盘 1 和磁盘 2。磁盘 0 是操作系统磁盘，磁盘 1 是临时磁盘，磁盘 2 是刚附加到 VM 的数据磁盘。新的数据磁盘会将分区列为“未知”。右键单击磁盘，然后选择“初始化”。

5.	在初始化磁盘时，系统会告知你将要擦除所有数据。单击“是”确认警告并初始化磁盘。完成后，即会将分区列为“GPT”。再次右键单击磁盘，然后选择“新建卷”。

6.	使用默认值完成向导操作。完成向导后，“卷”部分将列出新卷。现在，磁盘处于联机状态并已准备好存储数据。


	![已成功初始化卷](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] VM 的大小决定了可以在其上附加多少个磁盘。有关详细信息，请参阅[虚拟机大小](/documentation/articles/virtual-machines-linux-sizes)。


## 后续步骤

如果应用程序需要使用 D: 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](/documentation/articles/virtual-machines-windows-classic-change-drive-letter)。

<!---HONumber=Mooncake_Quality_Review_1202_2016-->