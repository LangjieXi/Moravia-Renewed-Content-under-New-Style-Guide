<!-- Ibiza Portal: not suitable for Mooncake -->


<properties
    pageTitle="使用 Azure 门户预览版创建 Linux VM | Azure"
    description="使用 Azure 门户预览版创建 Linux VM。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
	ms.service="virtual-machines-linux"
	ms.date="04/29/2016"
	wacn.date=""/>  


# 使用门户在 Azure 上创建 Linux VM

本文介绍如何使用 [Azure 门户预览版](https://portal.azure.cn/)快速创建 Linux 虚拟机。唯一必要条件是 [Azure 帐户](/pricing/1rmb-trial/)和 [SSH 公钥与私钥文件](/documentation/articles/virtual-machines-linux-mac-create-ssh-keys/)。

> [AZURE.NOTE] 如果选择使用密码保护对 VM 的访问，则密码长度必须超过 12 个字符，并且至少必须包含一个大写字符、一个小写字符、一个特殊字符和一个数字。


1. 使用 Azure 帐户标识登录到 Azure 门户预览版，然后单击左上角的“+ 新建”：

    ![screen1](./media/virtual-machines-linux-quick-create-portal/screen1.png)

2. 在“应用商店”中单击“虚拟机”，然后从“特色应用”映像列表中选择“Ubuntu Server 14.04 LTS”。确认底部显示的部署模型是 `Resource Manager`，然后单击“创建”。

    ![screen2](./media/virtual-machines-linux-quick-create-portal/screen2.png)

3. 在“基本信息”页上输入：
    - VM 的名称
    - 管理员用户的用户名
    - 设置为“SSH 公钥”的身份验证类型
    - 字符串形式的 SSH 公钥（默认位于 `~/.ssh/` 目录中）
    - 资源组名称（用于创建新的部署组），或选择现有的组

    单击“确定”继续并选择 VM 大小；显示如下：

    ![screen3](./media/virtual-machines-linux-quick-create-portal/screen3.png)

4. 选择“DS1”大小（在高级 SSD 上安装 Ubuntu），然后单击“选择”以配置设置。

    ![screen4](./media/virtual-machines-linux-quick-create-portal/screen4.png)

5. 在“设置”中，保留“存储和网络”值的默认设置，然后单击“确定”查看摘要。请注意，已通过选择 DS1（**S** 表示 SSD）将磁盘类型设置为高级 SSD。

    ![screen5](./media/virtual-machines-linux-quick-create-portal/screen5.png)

6. 确认新 Ubuntu VM 的设置，然后单击“确定”。

    ![screen6](./media/virtual-machines-linux-quick-create-portal/screen6.png)

7. 打开门户仪表板，并在“网络接口”中选择 NIC

    ![screen7](./media/virtual-machines-linux-quick-create-portal/screen7.png)

8. 打开 NIC 设置下方的公共 IP 地址菜单

    ![screen8](./media/virtual-machines-linux-quick-create-portal/screen8.png)

9. 使用 SSH 公钥通过 SSH 连接到公共 IP

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## 后续步骤

现在，已快速创建了用于测试或演示的 Linux VM。若要创建为基础结构自定义的 Linux VM，可以按照下列任一文章所述执行操作。

- [Create a Linux VM on Azure using Templates（使用模板在 Azure 上创建 Linux VM）](/documentation/articles/virtual-machines-linux-cli-deploy-templates/)
- [Create a SSH Secured Linux VM on Azure using Templates（使用模板在 Azure 上创建受 SSH 保护的 Linux VM）](/documentation/articles/virtual-machines-linux-create-ssh-secured-vm-from-template/)
- [Create a Linux VM using the Azure CLI（使用 Azure CLI 创建 Linux VM）](/documentation/articles/virtual-machines-linux-create-cli-complete/)

这些文章可帮助你开始构建 Azure 基础结构，并介绍多种专属和开源基础结构部署、配置与协调工具。

<!---HONumber=Mooncake_Quality_Review_1118_2016-->