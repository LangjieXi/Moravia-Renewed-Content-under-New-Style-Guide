<properties
	pageTitle="创建 Linux VM 的不同方式 | Microsoft Azure"
	description="列出在 Azure 上创建 Linux 虚拟机的不同方法，以及指向每种方法的工具和教程的链接。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# 使用资源管理器创建 Linux 虚拟机的不同方式

Azure 提供不同的方法来使用资源管理器部署模型创建 VM，以满足不同的用户和目的。本文汇总了这些方法的差异，以及在创建 Linux 虚拟机 (VM) 时可使用的选项。

## Azure CLI 

Azure CLI 可通过 npm 包、提供发行版的程序包或 Docker 容器跨平台使用。有关详细信息，请参阅[如何安装和配置 Azure CLI](../xplat-cli-install.md)。以下教程提供了有关使用 Azure CLI 的示例。阅读下面每篇文章，了解更多有关所示的 CLI 快速启动命令的更多详细信息：

* [从 Azure CLI 创建用于开发和测试的 Linux VM](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [使用 Azure 模板创建受保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [使用 Azure CLI 从头开始创建 Linux VM](virtual-machines-linux-create-cli-complete.md)

* [将磁盘添加到 Linux VM](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Azure 门户

[Azure 门户](https://portal.azure.com)的图形用户界面是一种试用 VM 的简便方式，尤其是在刚开始摸索 Azure 时，因为无需在你的系统上安装任何内容。使用 Azure 门户创建 VM：

* [使用 Azure 门户创建 Linux VM](virtual-machines-linux-quick-create-portal.md)
* [使用 Azure 门户附加磁盘](virtual-machines-linux-attach-disk-portal.md)

## 操作系统和映像选项
创建 VM 时，可根据要运行的操作系统选择映像。Azure 及其合作伙伴提供了许多映像，其中一些映像包括预安装的应用程序和工具。或者，你也可以上载自己的映像之一（参见下文）。

### Azure 映像
可以使用 `azure vm image` CLI 命令，按发布者、发行版本和内部版本查看可用的内容。

列出可用的发布者：

```bash
azure vm image list-publishers --location WestUS
```

列出给定发布者的可用产品（服务）：

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

列出给定产品/服务的可用 SKU（发行版本）：

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

列出给定发行版的所有可用映像：

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

请参阅[使用 Azure CLI 导航并选择 Azure 虚拟机映像](virtual-machines-linux-cli-ps-findimage.md)，获取有关浏览和使用可用映像的更多示例。

`azure vm quick-create` 和 `azure vm create` 命令还提供一些别名，可用于快速访问较常见的发行版及其最新版本。这比每次创建 VM 时都需要指定发布者、产品/服务、SKU 和版本要方便：

| 别名 | 发布者 | 产品/服务 | SKU | 版本 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 最新 |
| CoreOS | CoreOS | CoreOS | Stable | 最新 |
| Debian | credativ | Debian | 8 | 最新 |
| openSUSE | SUSE | openSUSE | 13\.2 | 最新 |
| RHEL | Redhat | RHEL | 7\.2 | 最新 |
| SLES | SLES | SLES | 12-SP1 | 最新 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 最新 |

### 使用你自己的映像

如果需要特定自定义，可以通过*捕获*现有的 Azure VM 使用基于该 VM 的映像，或者上载你自己的存储在虚拟硬盘 (VHD) 中的映像。有关受支持的分发以及如何使用你自己的映像的详细信息，请参阅以下文章：

* [Azure 认可的分发](virtual-machines-linux-endorsed-distros.md)

* [有关未认可分发的信息](virtual-machines-linux-create-upload-generic.md)

* [如何捕获用作 Resource Manager 模板的 Linux 虚拟机](virtual-machines-linux-capture-image.md)。快速启动命令：

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## 后续步骤

* 尝试参阅以下教程之一，了解如何通过[门户](virtual-machines-linux-quick-create-portal.md)、[CLI](virtual-machines-linux-quick-create-cli.md) 或 Azure Resource Manager [模板](virtual-machines-linux-cli-deploy-templates.md)创建 Linux VM。

* 创建 Linux VM 后，可以轻松[添加数据磁盘](virtual-machines-linux-add-disk.md)。

* [重置密码或 SSH 密钥和管理用户](virtual-machines-linux-using-vmaccess-extension.md)的简易步骤

<!---HONumber=Mooncake_Quality_Review_1202_2016-->