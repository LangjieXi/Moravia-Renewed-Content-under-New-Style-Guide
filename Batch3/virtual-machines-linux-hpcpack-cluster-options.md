<!-- not suitable for Mooncake -->

<properties
 pageTitle="云中的 Linux HPC Pack 群集选项 | Azure"
 description="了解使用 Microsoft HPC Pack 在 Azure 云中创建和管理 Linux 高性能计算 (HPC) 群集时可用的选项"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags 
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 wacn.date=""
 ms.author="danlep"/>

# 使用 HPC Pack 在 Azure 中为 Linux 工作负荷创建和管理 HPC 群集的选项

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文重点介绍使用 HPC Pack 运行 Linux 工作负荷的选项。还有用于[使用 HPC Pack 运行 Windows HPC 工作负荷](/documentation/articles/virtual-machines-windows-hpcpack-cluster-options/)的选项。

## 在 Azure VM 中运行 HPC Pack 群集

### Azure 模板


* （应用商店）[用于 Linux 工作负荷的 HPC Pack 群集](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* （快速入门）[使用 Linux 计算节点创建 HPC 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### PowerShell 部署脚本

* [使用 HPC Pack IaaS 部署脚本创建 Linux HPC 群集](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster-powershell-script/)

### 教程

* [教程：Azure 的 HPC Pack 群集中的 Linux 计算节点入门](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster/)

* [教程：在 Azure 中的 Linux 计算节点上使用 Microsoft HPC Pack 运行 NAMD](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster-namd/)

* [教程：在 Azure 中的 Linux RDMA 群集上运行 OpenFOAM 和 Microsoft HPC Pack](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster-openfoam/)

* [教程：在 Azure 中的 Linux RDMA 群集上运行 STAR-CCM+ 和 Microsoft HPC Pack](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster-starccm/)

### 群集管理

* [将作业提交到 Azure 中的 HPC Pack 群集](/documentation/articles/virtual-machines-windows-hpcpack-cluster-submit-jobs/)

* [HPC Pack 中的作业管理](https://technet.microsoft.com/zh-cn/library/jj899585.aspx)


## 为 MPI 工作负荷创建 RDMA 群集

* [教程：在 Azure 中的 Linux RDMA 群集上运行 OpenFOAM 和 Microsoft HPC Pack](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster-openfoam/)

* [设置 Linux RDMA 群集以运行 MPI 应用程序](/documentation/articles/virtual-machines-linux-classic-rdma-cluster/)

<!---HONumber=Mooncake_Quality_Review_1215_2016-->