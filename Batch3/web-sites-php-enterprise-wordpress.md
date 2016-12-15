<properties
    pageTitle="Azure App Service 上的企业级 WordPress | Azure"
    description="了解如何在 Azure App Service 上托管企业级 WordPress 网站"
    services="app-service\web"
    documentationcenter=""
    author="sunbuild"
    manager="yochayk"
    editor="" />  

<tags
    ms.assetid="22d68588-2511-4600-8527-c518fede8978"
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    wacn.date=""
    ms.author="sumuth" />

# Azure App Service 上的企业级 WordPress
Azure 应用服务为大规模的关键任务 [WordPress][wordpress] 网站提供了一个可扩展、安全且易用的环境。Microsoft 自身在运营 [Office][officeblog] 和 [Bing][bingblog] 博客等企业级网站。本文档说明如何使用 Azure App Service Web 应用建立和维护一个可以处理大量访客、基于云的企业级 WordPress 网站。

## <a name="planning"></a>体系结构与规划
基本的 WordPress 安装有只有两个要求。

* **MySQL 数据库** - 可以在使用 [Windows][mysqlwindows] 或 [Linux][mysqllinux] 的 Azure 虚拟机上管理自己的 MySQL 安装，或使用 **Azure 上的 MySQL 数据库**。

* **PHP 5.2.4 或更高版本** - Azure App Service 目前提供 [PHP 5.4、5.5 和 5.6 版本][phpwebsite]。

	> [AZURE.NOTE] 我们建议始终在最新版本的 PHP 上运行，以确保拥有最新的安全补丁。

### 基本部署
只使用基本要求，可以在一个 Azure 区域中创建一个基本解决方案。

![在单个 Azure 区域中托管的 Azure Web 应用和 MySQL 数据库][basic-diagram]  


尽管用户可以通过创建多个 Web 应用实例扩展其应用程序，但所有内容都托管在特定地理区域的数据中心。此区域之外的访客在使用网站时，响应速度可能会很慢，如果此区域的数据中心停机，您的应用也将无法使用。

### 多区域部署
通过使用 Azure [流量管理器][trafficmanager]，可以在多个地理地区扩展 WordPress 网站，同时仅为访客提供一个 URL。所有访客都通过流量管理器进来，然后基于负载平衡配置被路由到某一区域。

![一个托管在多个区域中的 Azure Web 应用，使用 MySQL 群集 CGE][multi-region-diagram]

在每个区域中，WordPress 网站仍会扩展到多个 Web 应用实例，但这种扩展是区域特定的；高流量区域的扩展方式可以与低流量区域不同。

可以使用 [MySQL 群集 CGE][cge] 进行复制和路由到多个 MySQL 数据库。

>[AZURE.NOTE] 对于多区域部署，需要在 IaaS 虚拟机中托管 MySQL 群集。Azure 上的 MySQL 数据库不支持多区域部署。

### 使用媒体存储和缓存的多区域部署
如果该网站接受上载或主机媒体文件，请使用 Azure Blob 存储。如果你需要进行缓存，可考虑 [Redis 缓存][rediscache]。

![一个托管在多个区域的 Azure Web 应用，使用 MySQL 群集 CGE，带有托管缓存、Blob 存储和 CDN][performance-diagram]

在默认情况下 Blob 存储分散在各个地区，因此无需担心跨所有站点复制文件的问题。也可以为 Blob 存储启用 Azure [内容分发网络 (CDN)][cdn]，这样可以将文件分发至距离访客更近的终端节点。

### 规划
#### 其他要求
| 为此，请执行以下操作... | 使用此方法... |
| --- | --- |
| **上载或存储大型文件** |[适用于使用 Blob 存储的 WordPress 插件][storageplugin] |
| **发送电子邮件** | [适用于使用 SendGrid 的 WordPress 插件][sendgridplugin] |
| **自定义域名** |[在 Azure App Service 中配置自定义域名][customdomain] |
| **HTTPS** |[在 Azure App Service 中启用 Web 应用的 HTTPS][httpscustomdomain] |
| **预生产验证** |[在 Azure 应用服务中设置 Web 应用的过渡环境][staging]<p>将 Web 应用从过渡切换到生产也会移动 WordPress 配置。在将过渡应用切换为生产应用之前，您应确保所有设置均针对您生产应用的要求进行了更新。</p> |
| **监视和故障排除** |[在 Azure App Service 中启用 Web 应用的诊断日志][log]和[在 Azure App Service 中监视 Web 应用][monitor] |
| **部署站点** |[在 Azure App Service 中部署 Web 应用][deploy] |

#### 可用性和灾难恢复
| 为此，请执行以下操作... | 使用此方法... |
| --- | --- |
| **负载平衡站点**或**地理分配站点** |[通过 Azure 流量管理器路由流量][trafficmanager] |
| **备份和还原** |[在 Azure App Service 中备份 Web 应用][backup]和[在 Azure App Service 中存储 Web 应用][restore] |

#### 性能
云中的性能主要通过缓存和横向扩展实现；但内存、带宽和 Web 应用托管的其他属性也应考虑在内。

| 为此，请执行以下操作... | 使用此方法... |
| --- | --- |
| **了解应用服务实例功能** |[定价详细信息，其中包括应用服务层的功能][websitepricing] |
| **缓存资源** |[Redis 缓存][rediscache] |
| **扩展您的应用程序** |[在 Azure 应用服务中缩放 Web 应用][websitescale]和 [MySQL 集群 CGE][cge] |

#### 迁移
有两种方法可以将现有的 WordPress 网站迁移到 Azure App Service。

* **[WordPress 导出][export]** - 它可以导出您的博客内容，然后使用 [WordPress 导入程序插件][import]导入至 Azure App Service 上的新 WordPress 网站。

  > [AZURE.NOTE]
  尽管此过程允许迁移内容，但不会迁移任何插件、主题或其他自定义内容。这些必须再次手动安装。
  >
  >
* **手动迁移** - [备份您的网站][wordpressbackup]和[数据库][wordpressdbbackup]，然后将其手动恢复为 Azure App Service 中的 Web 应用和相关的 MySQL 数据库，以便迁移高度定制化的网站，并避免手动安装插件、主题和其他自定义内容的麻烦。

## 分步说明
### <a name="Create-a-new-WordPress-site"></a> 创建 WordPress 站点
请遵照[在 Azure 中创建 PHP-MySQL Web 应用并使用 Git 进行部署](/documentation/articles/web-sites-php-mysql-deploy-use-git/)中的步骤创建新的 PHP Web 应用。

在本地将 PHP Web 应用配置到 WordPress 站点，并将其推送到 Azure。

如果要迁移现有的 WordPress 网站，请在创建新 Web 应用后，参阅[将现有 WordPress 网站迁移到 Azure](#Migrate-an-existing-WordPress-site-to-Azure)。

### <a name="Migrate-an-existing-WordPress-site-to-Azure"></a>将现有 WordPress 站点迁移到 Azure
如[架构与规划](#planning)部分所述，有两种方法迁移 WordPress 网站。

* **导出和导入** - 针对不含大量定制内容的网站，或者只需移动内容的位置。
* **备份和还原** - 针对包含大量定制内容、您想要移动所有内容的网站。

使用下述部分之一迁移你的网站。

#### 导出和导入方法
1. 使用 [WordPress 导出][export]导出您的现有网站。
2. 使用[创建 WordPress 网站](#Create-a-new-WordPress-site)部分的步骤创建 Web 应用。
3. 登录到 Web 应用上的 WordPress 网站，然后单击“插件”->“新增”。搜索并安装 **WordPress 导入程序**插件。
4. 安装导入程序插件后，单击“工具”->“导入”，然后选择“WordPress”，以使用 WordPress 导入程序插件。
5. 在**导入 WordPress** 页面上，单击**选择文件**。浏览到从您的现有 WordPress 网站导出的 WXR 文件，然后选择**上传文件和导入**。
6. 单击“提交”。系统提示导入成功。
7. 完成所有这些步骤后，从 [Azure 门户预览版][mgmtportal]中的 Web 应用边栏选项卡重新启动站点。

导入网站后，您可能需要执行以下步骤，以启用导入文件中不包含的设置。

| 如果你在使用... | 采取的措施： |
| --- | --- |
| **固定链接** |从新网站的 WordPress 仪表板中，单击**设置** -> **固定链接**，然后更新固定链接结构 |
| **图像/媒体链接** |若要将链接更新到新位置，请使用搜索和替换工具 [Velvet Blues Update URLs 插件][velvet]，或在您的数据库中手动更新 |
| **主题** |转到**外观** -> **主题**并根据需要更新网站主题 |
| **菜单** |如果主题支持菜单，那么主页的链接可能仍有嵌入的旧子目录。转到**外观** -> **菜单**并对其进行更新 |

#### 备份和恢复方法
1. 使用 [WordPress 备份][wordpressbackup]上的信息，备份您现有的 WordPress 网站。
2. 使用[备份您的数据库][wordpressdbbackup]中的信息，备份您现有的数据库。
3. 创建数据库并恢复备份。

   1.  在“Azure 上的 MySQL 数据库”中创建一个数据库，或者在 [Windows][mysqlwindows] 或 [Linux][mysqllinux] VM 上设置一个 MySQL 数据库。
   2. 使用 MySQL 客户端，如 [MySQL Workbench][workbench]，连接到新数据库，然后导入 WordPress 数据库。
   3. 更新数据库，将域条目更改为新 Azure App Service 域。例如，mywordpress.chinacloudsites.cn。使用[搜索和替换为 WordPress 数据库脚本][searchandreplace]，安全地更改所有实例。
4. 在 Azure 门户预览版中创建 Web 应用并发布 WordPress 备份。

   1. 在 [Azure 门户预览版][mgmtportal]中创建带数据库的 Web 应用，方法是： 单击“新建”->“Web + 移动”->“Web 应用”->“创建”。配置所有所需的设置来创建空 Web 应用。
   2. 在 WordPress 备份中，找到 **wp-config.php** 文件，并在编辑器中打开它。将以下项替换为新 MySQL 数据库的信息。

      * **DB\_NAME** - 数据库的用户名称
      * **DB\_USER** - 用来访问数据库的用户名称
      * **DB\_PASSWORD** - 用户密码

        更改这些条目之后，保存并关闭 **wp-config.php** 文件。
   3. 使用[在 Azure App Service 中部署 Web 应用][deploy]信息来启用您想要使用的部署方法，然后将 WordPress 备份部署到 Azure App Service 中的 Web 应用。
5. 部署 WordPress 网站后，你应该能够使用网站的 *. azurewebsite.net URL（作为应用服务 Web 应用）访问新站点。

### 配置网站
创建 WordPress 网站或将其迁移之后，可以根据以下信息改进性能或启用其他功能。

| 为此，请执行以下操作... | 使用此方法... |
| --- | --- |
| **设置 App Service 计划模式、大小和启用缩放** |[在 Azure App Service 中缩放 Web 应用][websitescale] |
| **启用持久的数据库连接** |默认情况下，WordPress 不使用持久的数据库连接，这可能导致数据库的连接在多次连接后成为限制。若要启用持久连接，请安装（持久连接适配器插件）[https://wordpress.org/plugins/persistent-database-connection-updater/installation/]。 |
| **提高性能** |<ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">禁用 ARR cookie</a> - 在多个 Web Apps 实例上运行 WordPress 时可以提高性能</p></li><li><p>启用缓存。<a href="/documentation/services/redis-cache">Redis 缓存</a>（预览）可以与 <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 对象缓存 WordPress 插件</a>一起使用，</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">如何使用 Wincache 提高 WordPress 速度</a> - 对于 Web 应用，Wincache 默认处于启用状态</p></li><li><p>[在 Azure 应用服务中缩放 Web 应用][websitescale]，以及使用 <a href="http://www.mysql.com/products/cluster/">MySQL 群集 CGE</a></p></li></ul> |
| **使用 blob 进行存储处理** |<ol><li><p>[创建 Azure 存储帐户](/documentation/articles/storage-create-storage-account/)</p></li><li><p>了解如何[使用内容分发网络 (CDN) ][cdn]地理分配 Blob 中存储的数据。</p></li><li><p>安装和配置 <a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress 插件的 Azure 存储</a>。</p><p>有关该插件的详细设置和配置信息，请参阅<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">用户指南</a>。</p> </li></ol> |
| **启用电子邮件** | 安装 WordPress 的 <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">SendGrid 插件</a>。 |
| **配置自定义域名** |[在 Azure App Service 中配置自定义域名][customdomain] |
| **启用自定义域名的 HTTPS** |[在 Azure App Service 中启用 Web 应用的 HTTPS][httpscustomdomain] |
| **负载平衡或地理分配站点** |[通过 Azure 流量管理器路由流量][trafficmanager]。如果您使用自定义域，请参阅[在 Azure App Service 中使用自定义域名][customdomain]，了解有关使用含自定义域名的流量管理器的信息 |
| **启用自动化的备份** |[在 Azure App Service 中备份 Web 应用][backup] |
| **启用诊断日志记录** |[在 Azure App Service 中启用 Web 应用的诊断日志记录][log] |

## 后续步骤
* [WordPress 优化](http://codex.wordpress.org/WordPress_Optimization)
* [在 Azure App Service 中将 WordPress 转换为 Multisite](/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [在 Azure App Service 的 Web 应用的子文件夹中托管 WordPress](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [分步说明：使用 Azure 创建 WordPress 网站](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [在 Azure 上托管现有的 WordPress 博客](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [在 WordPress 中启用美观的固定链接](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [如何在 Azure App Service 上免费运行 WordPress](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [在 2 分钟或更短时间内在 Azure 上运行 WordPress](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [将 WordPress 博客移至 Azure - 第 1 部分：在 Azure 上创建 WordPress 博客](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [将 WordPress 博客移至 Azure - 第 2 部分：传输您的内容](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [将 WordPress 博客移至 Azure-第 3 部分：设置您的自定义域](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [将 WordPress 博客移至 Azure - 第 4 部分：美观的固定链接和 URL 重写规则](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [将 WordPress 博客移至 Azure - 第 5 部分：将一个子文件夹移动到根](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [如何在 Azure 帐户中设置 WordPress Web 应用](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [在 Azure 上支持 WordPress](http://www.johnpapa.net/wordpress-on-azure/)
* [在 Azure 上支持 WordPress 的技巧](http://www.johnpapa.net/azurecleardbmysql/)

## 发生的更改
* 有关从网站更改为应用服务的指南，请参阅 [Azure App Service 及其对现有 Azure 服务的影响](/documentation/articles/app-service-changes-existing-services/)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: /documentation/articles/web-sites-php-configure/
[customdomain]: /documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: /documentation/articles/traffic-manager-overview/
[backup]: /documentation/articles/web-sites-backup/
[restore]: /documentation/articles/web-sites-restore/
[rediscache]: /documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/zh-cn/library/azure/dn386122.aspx
[websitescale]: /documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/zh-cn/library/azure/dn386113.aspx
[staging]: /documentation/articles/web-sites-staged-publishing/
[monitor]: /documentation/articles/web-sites-monitor/
[log]: /documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: /documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: /documentation/articles/virtual-machines-windows-classic-mysql-2008r2/
[mysqllinux]: /documentation/articles/virtual-machines-linux-classic-mysql-on-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/overview/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.cn/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: /documentation/articles/web-sites-deploy/
[posh]: /documentation/articles/powershell-install-configure/
[Azure CLI]: /documentation/articles/xplat-cli-install/
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: /documentation/articles/cdn-overview/

<!---HONumber=Mooncake_Quality_Review_1215_2016-->