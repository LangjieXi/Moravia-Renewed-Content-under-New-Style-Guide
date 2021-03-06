<properties 
	pageTitle="媒体服务发行说明" 
	description="媒体服务发行说明" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags
	ms.service="media-services"
	ms.date="07/14/2016"
	wacn.date=""/>


# Azure 媒体服务发行说明

这些发行说明汇总了与以前版本相比的变更之处和已知的问题。

>[AZURE.NOTE] 我们希望能够倾听客户的心声，并致力于解决对客户造成影响的问题。若要报告问题或提出问题，请在 [Azure 媒体服务 MSDN 论坛]发帖。

- [当前已知的问题](#issues)
- [REST API 版本历史记录](#rest_version_history)
- [2016 年 7 月版本](#july_changes16)
- [2016 年 4 月版本](#apr_changes16)
- [2016 年 2 月版本](#feb_changes16)
- [2016 年 1 月版本](#jan_changes_16)
- [2015 年 12 月版本](#dec_changes_15)
- [2015 年 11 月版本](#nov_changes_15)
- [2015 年 10 月版本](#oct_changes_15)
- [2015 年 9 月版本](#september_changes_15)
- [2015 年 8 月版本](#august_changes_15)
- [2015 年 7 月版本](#july_changes_15)
- [2015 年 6 月版本](#june_changes_15)
- [2015 年 5 月版本](#may_changes_15)
- [2015 年 4 月版本](#april_changes_15)
- [2015 年 3 月版本](#march_changes_15)
- [2015 年 2 月版本](#february_changes_15)
- [2015 年 1 月版本](#january_changes_15)
- [2014 年 12 月版本](#december_changes_14)
- [2014 年 11 月版本](#november_changes_14)
- [2014 年 10 月版本](#october_changes_14)
- [2014 年 9 月版本](#september_changes_14)
- [2014 年 8 月版本](#august_changes_14)
- [2014 年 7 月版本](#july_changes_14)
- [2014 年 5 月版本](#may_changes_14)
- [2014 年 4 月版本](#april_changes_14)
- [2014 年 1/2 月版本](#jan_feb_changes_14)
- [2013 年 12 月版本](#december_changes_13)
- [2013 年 11 月版本](#november_changes_13)
- [2013 年 8 月版本](#august_changes_13)
- [2013 年 6 月版本](#june_changes_13)
- [2012 年 12 月版本](#december_changes_12)
- [2012 年 11 月版本](#november_changes_12)
- [2012 年 6 月预览版](#june_changes_12)


##<a id="issues"></a>当前已知的问题

### <a id="general_issues"></a>媒体服务常规问题

问题|说明
---|---
REST API 中未提供几种常见的 HTTP 标头。|如果你使用 REST API 来开发媒体服务应用程序，你将发现一些常见的 HTTP 标头字段（包括 CLIENT-REQUEST-ID、REQUEST-ID 和 RETURN-CLIENT-REQUEST-ID）不受支持。未来的更新将增加这些标头。
使用包含转义字符（例如 %20）的文件名对资产进行编码失败，出现错误：“MediaProcessor: 找不到文件”。|要添加到资产并编码的文件，其名称应仅包含字母数字字符和空格。将在未来的更新中解决此问题。
Azure 存储 SDK 版本 3.x 中的 ListBlobs 方法将失败。|媒体服务基于 [2012-02-12](http://msdn.microsoft.com/zh-cn/library/azure/dn592123.aspx) 版本生成 SAS URL。如果希望使用 Azure 存储 SDK 列出 Blob 容器中的 Blob，请使用 Azure 存储 SDK 版本 2.x 中的 [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) 方法。Azure 存储 SDK 版本 3.x 中的 ListBlobs 方法将失败。
对于发出过多服务请求的应用程序，媒体服务限制机制将限制其资源使用量。该服务可能返回“服务不可用”(503) HTTP 状态代码。|有关详细信息，请参阅 [Azure 媒体服务错误代码](http://msdn.microsoft.com/zh-cn/library/azure/dn168949.aspx)主题中 503 HTTP 状态代码的说明。
查询实体时，一次返回的实体数限制为 1000 个，因为公共 REST v2 将查询结果数限制为 1000 个。 | 需使用[此 .NET 示例](/documentation/articles/media-services-dotnet-manage-entities/#enumerating-through-large-collections-of-entities)和[此 REST API 示例](/documentation/articles/media-services-rest-manage-entities/#enumerating-through-large-collections-of-entities)中所述的 **Skip** 和 **Take** (.NET)/ **top** (REST)。 
某些客户端可能会在平滑流式处理清单中碰到重复标记问题。|有关详细信息，请参阅[本节](/documentation/articles/media-services-deliver-content-overview/#known-issues)。

### <a id="dotnet_issues"></a>适用于 .NET 的媒体服务 SDK 存在的问题

问题|说明
---|---
SDK 中的媒体服务对象无法进行序列化，因此不能用于 Azure 缓存。|如果尝试对 SDK AssetCollection 对象进行序列化以将其添加到 Azure 缓存，将引发异常。

##<a id="rest_version_history"></a>REST API 版本历史记录

有关媒体服务 REST API 版本历史记录的信息，请参阅 [Azure 媒体服务 REST API 参考]。

##<a id="july_changes16"></a>2016 年 7 月版本

###对由编码任务所生成清单文件 (*.ISM) 的更新

将编码任务提交到媒体编码器标准版或 Azure 媒体编码器后，该编码任务将在输出资产中生成[流式处理清单文件](/documentation/articles/media-services-deliver-content-overview/) (*.ism) 文件。最新的服务版本已更新此流式处理清单文件的语法。

>[AZURE.NOTE]流式处理清单 (.ism) 文件的语法保留供内部使用，在将来的版本中可能会更改。请勿修改或操作此文件的内容。

###当编码任务输出一个或多个 MP4 文件时，即会在输出资产中生成新的客户端清单 (*.ISMC) 文件

从最新服务版本开始，生成一个或多个 MP4 文件的编码任务完成后，输出资产中也会包含流式处理客户端清单 (*.ismc) 文件。该 .ismc 文件可帮助提升动态流式处理的性能。

>[AZURE.NOTE]客户端清单 (.ismc) 文件的语法保留供内部使用，在将来的版本中可能会更改。请勿修改或操作此文件的内容。

有关详细信息，请参阅[这一](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/)博客。

### 已知问题

某些客户端可能会在平滑流式处理清单中碰到重复标记问题。有关详细信息，请参阅[本节](/documentation/articles/media-services-deliver-content-overview/#known-issues)。

##<a id="apr_changes16"></a>2016 年 4 月版本

### Azure 媒体分析

Azure 媒体服务引入了 Azure 媒体分析，可提供功能强大的视频智能。有关详细信息，请参阅 [Azure 媒体服务分析概述](/documentation/articles/media-services-analytics-overview/)。

### Apple FairPlay（预览版）

Azure 媒体服务现允许通过 Apple FairPlay 动态加密 HTTP Live Streaming (HLS) 内容。也可使用 AMS 许可证传递服务将 FairPlay 许可证传递到客户端。如需更多详细信息，请参阅[使用 Azure 媒体服务流式传输受 Apple FairPlay 保护的 HLS 内容](/documentation/articles/media-services-protect-hls-with-fairplay/)。
  
##<a id="feb_changes16"></a>2016 年 2 月版本

适用于 .NET 的 Azure 媒体服务 SDK 最新版本 (3.5.3) 包含 Widevine 相关 Bug 修复。该问题是：无法对 Widevine 加密的多个资产重复使用 AssetDeliveryPolicy。为修复此 Bug，向 SDK 添加了以下属性：**WidevineBaseLicenseAcquisitionUrl**。
	
	Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
	    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
	{
	    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
	    
	};

##<a id="jan_changes_16"></a>2016 年 1 月版本

编码保留单位已重命名，以减少与编码器名称的混淆。

基本、标准和高级编码保留单位已分别重命名为 S1、S2 和 S3 保留单位。目前使用基本编码保留单位的客户在 Azure 门户（和帐单中）将看到 S1 的标签，标准和高级客户将分别看到 S2 和 S3 的标签。

##<a id="dec_changes_15"></a>2015 年 12 月版本

Azure SDK 团队已发布新版 [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) 程序包，其中包含 Microsoft Azure 媒体服务的更新与新功能。具体而言，适用于 PHP 的 Azure 媒体服务 SDK 现在支持最新的[内容保护](/documentation/articles/media-services-content-protection-overview/)功能：在有和没有令牌限制的情况下使用 AES 与 DRM（PlayReady 和 Widevine）动态加密。它还支持缩放[编码单位](/documentation/articles/media-services-dotnet-encoding-units/)。

有关详细信息，请参阅：

- [适用于 PHP 的 Microsoft Azure 媒体服务 SDK](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) 博客。
- 以下[代码示例](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)可帮助你快速入门：
	- **vodworkflow\_aes.php**：这是一个 PHP 文件，演示如何使用 AES-128 动态加密和密钥传递服务。该文件基于[此文](/documentation/articles/media-services-protect-with-aes128/)中所述的 .NET 示例。
	- **vodworkflow\_aes.php**：这是一个 PHP 文件，演示如何使用 PlayReady 动态加密和许可证传递服务。该文件基于[此文](/documentation/articles/media-services-protect-with-drm/)中所述的 .NET 示例。
	- **scale\_encoding\_units.php**：这是一个 PHP 文件，演示如何缩放编码保留单位。


##<a id="nov_changes_15"></a>2015 年 11 月版本

现在，Azure 媒体服务在云中提供 Google Widevine 许可证传递服务。有关更多详细信息，请参阅[此通知博客](http://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)。另请参阅[此教程](/documentation/articles/media-services-protect-with-drm/)和 [GitHub 存储库](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)。

请注意，Azure 媒体服务提供的 Widevine 许可证传递服务是预览版。有关详细信息，请参阅[此博客](http://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)。

##<a id="oct_changes_15"></a>2015 年 10 月版本

Azure 媒体服务 (AMS) 现已在以下数据中心推出：巴西南部、印度西部、印度南部和印度中部。现可使用 Azure 管理门户[创建媒体服务帐户](/documentation/articles/media-services-create-account/#create-a-media-services-account-using-quick-create)，并执行[此处](/documentation/services/media-services/)所述的各项任务。不过，这些数据中心未启用实时编码。此外，这些数据中心并非提供所有类型的编码保留单位。

- 巴西南部：仅提供标准和基本编码保留单位
- 印度西部、印度南部和印度中部：仅提供基本编码保留单位


##<a id="september_changes_15"></a>2015 年 9 月版本 

- AMS 现在提供通过 Widevine 模块化 DRM 技术保护点播视频 (VOD) 和实时流的功能。可通过以下交付服务合作伙伴来帮助交付 Widevine 许可证：[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/)。有关详细信息，请参阅[此博客](http://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)。

	可通过 [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)（从版本 3.5.1 开始）或 REST API 来配置 AssetDeliveryConfiguration 以使用 Widevine。

- AMS 增加了对 Apple ProRes 视频的支持。现可上传使用 Apple ProRes 或其他编解码器的 QuickTime 源视频文件。有关详细信息，请参阅[此博客](http://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)。

- 现可使用媒体编码器标准版来执行子剪辑和实时存档提取操作。有关详细信息，请参阅[此博客](http://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)。

- 在筛选方面做了以下更新：

	- 现可通过 audio-only 筛选器处理 Apple HTTP Live Streaming (HLS) 格式。此更新让用户能够通过在 URL 中指定 (audio-only=false) 来删除仅音频曲目。
	- 为资产定义筛选器时，现可将多个（最多 3 个）筛选器组合到一个 URL 中。

	有关详细信息，请参阅[此博客](http://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support)。

- AMS 现在支持 HLS v4 格式的 I-Frame。I-Frame 支持优化快进和倒带操作。默认情况下，所有 HLS v4 输出包括 I-Frame 播放列表 (EXT-X-I-FRAME-STREAM-INF)。
 
	有关详细信息，请参阅[此博客](http://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support)。

##<a id="august_changes_15"></a>2015 年 8 月版本

- 用于 Java V0.8.0 版的 Azure 媒体服务 SDK 和新示例现已推出。有关详细信息，请参阅：

	- [博客文章](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
	- [Java 示例存储库](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- 支持多音频流的 Azure Media Player 更新。有关详细信息，请参阅：
	- [博客文章](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>2015 年 7 月版本

- 宣布媒体编码器标准版公开上市。有关详细信息，请参阅[此博客文章](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)。

	媒体编码器标准版使用[本](https://msdn.microsoft.com/zh-cn/library/azure/mt269960.aspx)部分中所述的预设值。注意，当使用预设值进行 4K 编码时，应获取“高级版”保留单位类型。有关详细信息，请参阅[如何缩放编码](/documentation/articles/media-services-portal-encoding-units/)。
- Azure 媒体服务和播放器的实时标题。有关详细信息，请参阅[此博客文章](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###媒体服务 .NET SDK 更新

Azure 媒体服务 .NET SDK 当前版本为 3.4.0.0。此版本中增加了以下功能：

- 实现了对实时存档的支持。注意，不能下载包含实时存档的资产。
- 实现了对动态筛选器的支持。
- 实现了允许用户在删除资产时保留存储容器的功能。
- 修复了与频道中的重试策略相关的 Bug。

##<a id="june_changes_15"></a>2015 年 6 月版本

###媒体服务 .NET SDK 更新

Azure 媒体服务 .NET SDK 当前版本为 3.3.0.0。此版本中增加了以下功能：

- 支持 OpenId Connect 发现规范，
- 支持标识提供者端的处理密钥滚动更新。

如果使用标识提供者（如 Azure Active Directory、Google、Salesforce）来公开 OpenID Connect 发现文档，可指导 Azure 媒体服务获取签名密钥，以根据 OpenID Connect 发现规范验证 JWT 令牌。

有关详细信息，请参阅 [Using Json Web Keys from OpenID Connect discovery spec to work with JWT token authentication in Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)（使用 OpenID Connect 发现规范中的 JSON Web 密钥在 Azure 媒体服务中进行 JWT 令牌身份验证）。


##<a id="may_changes_15"></a>2015 年 5 月版本

宣布推出以下新功能：

- [使用媒体服务进行实时编码的预览](/documentation/articles/media-services-manage-live-encoder-enabled-channels/)
- [动态清单](/documentation/articles/media-services-dynamic-manifest-overview/)
- [Azure Media Hyperlapse 媒体处理器的预览](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>2015 年 4 月版本

###媒体服务一般更新

- [宣布推出 Azure 媒体播放器](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)。
- 自媒体服务 REST 2.10 起，使用主要和辅助引入 URL 创建了配置为引入 RTMP 协议的频道。有关详细信息，请参阅[频道引入配置](/documentation/articles/media-services-manage-channels-overview/#channel_input)
- Azure 媒体索引器更新
	- 支持西班牙语
	- 新增配置 XML 格式
	
	有关详细信息，请参阅[此博客](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)。
###媒体服务 .NET SDK 更新

Azure 媒体服务 .NET SDK 当前版本为 3.2.0.0。

以下是一些面向客户的更新：
 
- **重大更改**：**TokenRestrictionTemplate.Issuer** 和 **TokenRestrictionTemplate.Audience** 更改为了字符串类型。
- 与创建自定义重试策略相关的更新。
- 与上传/下载文件相关的 Bug 修复。
- **MediaServicesCredentials** 类现在接受向主要和辅助访问控制终结点进行身份验证。



##<a id="march_changes_15"></a>2015 年 3 月版本

### 媒体服务一般更新

- 媒体服务现在提供 Azure CDN 集成。为了支持集成，将 **CdnEnabled** 属性添加到了 **StreamingEndpoint**。**CdnEnabled** 可用于从版本 2.9 开始的 REST API（有关详细信息，请参阅 [StreamingEndpoint](https://msdn.microsoft.com/zh-cn/library/azure/dn783468.aspx)）。**CdnEnabled** 可用于从版本 3.1.0.2 开始的 NET SDK（有关详细信息，请参阅 [StreamingEndpoint]）https://msdn.microsoft.com/zh-cn/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx))。
- 宣布推出**媒体编码器高级版工作流**。有关详细信息，请参阅[在 Azure 媒体服务中引入高级版编码](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)。
 


##<a id="february_changes_15"></a>2015 年 2 月版本

### 媒体服务一般更新

媒体服务 REST API 当前版本为 2.9。自此版本起，可以通过流式处理终结点启用 Azure CDN 集成。有关详细信息，请参阅 [StreamingEndpoint](https://msdn.microsoft.com/zh-cn/library/dn783468.aspx)。

##<a id="january_changes_15"></a>2015 年 1 月版本

### 媒体服务一般更新

宣布采用动态加密的内容保护公开上市 (GA)。有关详细信息，请参阅 [Azure 媒体服务采用公开上市的 DRM 技术增强流式处理的安全性](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)。

###媒体服务 .NET SDK 更新

Azure 媒体服务 .NET SDK 当前版本为 3.1.0.1。

此版本将默认的 Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate 构造函数标记为已过时。新的构造函数将 TokenType 作为参数。

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>2014 年 12 月版本

###媒体服务一般更新

- Azure 媒体索引器处理器增加了一些更新和新功能。有关详细信息，请参阅 [Azure 媒体索引器版本 1.1.6.7 发行说明](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)。
- 增加了一个新的 REST API，可用于更新编码保留单位：[REST 的 EncodingReservedUnitType](http://msdn.microsoft.com/zh-cn/library/azure/dn859236.aspx)。
- 增加了对密钥传递服务的 CORS 支持。
- 改进了查询授权策略选项的性能。
- 在中国数据中心，现按客户提供[密钥传递 URL](http://msdn.microsoft.com/zh-cn/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url)（与其他数据中心相同）。
- 增加了 HLS 自动目标持续时间。进行实时流式传输时，HLS 始终是动态打包的。默认情况下，媒体服务根据从实时编码器收到的关键帧间隔 (KeyFrameInterval)（也称图片组 - GOP）自动计算 HLS 段打包率 (FragmentsPerSegment)。有关详细信息，请参阅[使用 Azure 媒体服务实时传送视频流]。
 
###媒体服务 .NET SDK 更新

- [Azure 媒体服务 .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) 当前版本为 3.1.0.0。
- 将 .Net SDK 依赖项升级到了 .NET 4.5 Framework。
- 增加了一个新的 API，可用于更新编码保留单位。有关详细信息，请参阅[使用 .NET 更新保留单位类型和增加编码保留单位 (RU)](/documentation/articles/media-services-dotnet-encoding-units/)。
- 增加了对令牌身份验证的 JWT（JSON Web 令牌）支持。有关详细信息，请参阅 [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)（Azure 媒体服务和动态加密中的 JWT 令牌身份验证）。
- 增加了 PlayReady 许可证模板中 BeginDate 和 ExpirationDate 的相对偏移量。


##<a id="november_changes_14"></a>2014 年 11 月版本

- 媒体服务现在允许通过 SSL 连接引入实时平滑流式处理 (FMP4) 内容。若要通过 SSL 进行引入，请确保将引入 URL 更新为 HTTPS。有关实时传送视频流的详细信息，请参阅[使用 Azure 媒体服务实时传送视频流]。
- 注意，当前无法通过 SSL 连接引入 RTMP 实时流。
- 也可通过 SSL 连接流式传输内容。为此，请确保流式处理 URL 以 HTTPS 开头。
- 请注意，仅当要从中传递内容的流式处理终结点是在 2014 年 9 月 10 日以后创建的，才可通过 SSL 进行流式处理。如果流式处理 URL 基于 9 月 10 日之后创建的流式处理终结点，则 URL 会包含“streaming.mediaservices.chinacloudapi.cn”（新格式）。包含“origin.mediaservices.chinacloudapi.cn”（旧格式）的流式处理 URL 不支持 SSL。如果你的 URL 采用旧格式，而你希望能够通过 SSL 进行流式处理，请[创建新的流式处理终结点](/documentation/articles/media-services-manage-origins/)。使用基于新流式处理终结点创建的 URL，通过 SSL 流式处理内容。
   
##<a id="october_changes_14"></a>2014 年 10 月版本

### <a id="new_encoder_release"></a>媒体服务编码器版本

宣布推出新版媒体服务 Azure 媒体编码器。使用最新的 Azure 媒体编码器，只需支付输出 GB 费用，但在其他方面，新编码器的功能与旧编码器兼容。有关详细信息，请参阅[媒体服务定价详细信息]。

### <a id="oct_sdk"></a>媒体服务 .NET SDK 

适用于 .NET 的媒体服务 SDK 扩展当前版本为 2.0.0.3。

适用于 .NET 的媒体服务 SDK 当前版本为 3.0.0.8。

进行了以下更改：

- 重构了重试策略类。
- 在 HTTP 请求标头中增加了用户代理字符串。
- 增加了 Nuget 还原生成步骤。
- 修复了方案测试以使用存储库中的 x509 证书。
- 在更新频道和流式处理端时验证设置。
 

### 用于承载媒体服务示例的新增 GitHub 存储库

示例位于 [Azure 媒体服务示例 GitHub 存储库](https://github.com/Azure/Azure-Media-Services-Samples)中。


##<a id="september_changes_14"></a>2014 年 9 月版本

媒体服务 REST 元数据当前版本为 2.7。有关最新 REST 更新的详细信息，请参阅 [Azure 媒体服务 REST API 参考]。

适用于 .NET 的媒体服务 SDK 当前版本为 3.0.0.7。
 
### <a id="sept_14_breaking_changes"></a>重大更改

* **Origin** 已重命名为 [StreamingEndpoint]。
* 使用**Azure 管理门户**进行编码然后发布 MP4 文件时，默认行为发生变化。

以前，使用 Azure 管理门户发布单文件 MP4 视频资产时，将创建 SAS URL（通过 SAS URL 可从 Blob 存储下载视频）。目前，使用 Azure 管理门户编码并发布单文件 MP4 视频资产时，生成的 URL 将指向 Azure 媒体服务流式处理终结点。此更改不会影响 MP4 视频，此类视频将直接上传到媒体服务并发布，而不经 Azure 媒体服务编码。

目前，可采用以下两个选项来解决该问题。

* 启用流式处理单位，并使用动态打包将 .mp4 资产作为平滑流式处理演示文稿进行流式处理。

* 创建 SAS URL 以下载（或渐进式播放）.mp4。有关如何创建 SAS 定位符的详细信息，请参阅[交付内容]。


### <a id="sept_14_GA_changes"></a>公开上市版本的新增功能/方案

* **索引器媒体处理器**。有关详细信息，请参阅[使用 Azure 媒体索引器对媒体文件进行索引]。

* 现可通过 [StreamingEndpoint] 实体添加自定义域（主机）名。

	要将自定义域名用作媒体服务流式处理终结点名称，需将自定义主机名添加到流式处理终结点。使用媒体服务 REST API 或 .NET SDK 添加自定义主机名。
	
	请注意以下事项：
	
	* 用户必须具有自定义域名的所有权。
	
	* 必须通过 Azure 媒体服务验证域名所有权。若要验证域，请创建 CName，将 <MediaServicesAccountId>.<parent domain> 映射到 verifydns.<mediaservices-dns-zone>。
	
	* 必须创建另一个 CName，将自定义主机名（例如 sports.contoso.com）映射到媒体服务流式处理终结点的主机名（例如 amstest.streaming.mediaservices.chinacloudapi.cn）。


	有关详细信息，请参阅 [StreamingEndpoint] 主题中的 **CustomHostNames** 属性。

### <a id="sept_14_preview_changes"></a>公共预览版的新增功能/方案

* 实时传送视频流预览。有关详细信息，请参阅[使用 Azure 媒体服务实时传送视频流]。

* 密钥传递服务。有关详细信息，请参阅[使用 AES-128 动态加密和密钥传递服务]。

* AES 动态加密。有关详细信息，请参阅[使用 AES-128 动态加密和密钥传递服务]。

* PlayReady 许可证传递服务。有关详细信息，请参阅[使用 PlayReady 动态加密和许可证传递服务]。

* PlayReady 动态加密。有关详细信息，请参阅[使用 PlayReady 动态加密和许可证传递服务]。

* 媒体服务 PlayReady 许可证模板。有关详细信息，请参阅[媒体服务 PlayReady 许可证模板概述]。

* 流式处理存储加密资产。有关详细信息，请参阅[流式处理存储加密内容]。

##<a id="august_changes_14"></a>2014 年 8 月版本

对资产进行编码时，完成编码作业后会生成输出资产。在此版本之前，Azure 媒体服务编码器会生成有关输出资产的元数据。自此版本起，编码器还将生成有关输入资产的元数据。有关详细信息，请参阅[输入元数据]和[输出元数据]主题。


##<a id="july_changes_14"></a>2014 年 7 月版本

对 Azure 媒体服务包装程序和加密程序进行了以下 Bug 修复：

* 传输实时归档资产以进行 HTTP Live Streaming 时仅播放音频 – 此问题已修复，现在将同时播放音频和视频。

* 打包资产以进行 HTTP Live Streaming 和 AES 128 位信封加密时，Android 设备将不会播放已打包的流 – 此 Bug 已修复，现在支持 HTTP Live Streaming 的 Android 设备将播放已打包的流。

##<a id="may_changes_14"></a>2014 年 5 月版本

### <a id="may_14_changes"></a>媒体服务一般更新

现在可以使用[动态打包]对 HTTP Live Streaming 内容 (HLS) v3 进行流式处理。若要对 HLS v3 进行流式处理，请将以下格式添加到原点定位符路径：*.ism/manifest(format=m3u8-aapl-v3)。有关详细信息，请参阅 [Nick Drouin 的博客]。

动态打包现在还支持基于使用 PlayReady 静态加密的平滑流式处理内容传递使用 PlayReady 加密的 HLS（v3 和 v4）。有关如何使用 PlayReady 加密平滑流式处理内容的信息，请参阅[使用 PlayReady 保护平滑流]。

### <a name="may_14_donnet_changes"></a>媒体服务 .NET SDK 更新

媒体服务 .NET SDK 3.0.0.5 版本中包含以下改进：

* 上传/下载媒体资产速度更快、恢复能力更佳。

* 重试逻辑和暂时性异常处理方面的改进：

	* 改进了暂时性错误检测和重试逻辑，以处理由查询、保存更改、上传或下载文件引起的异常。
	
	* 发生 Web 异常时（例如在请求 ACS 令牌期间），可以注意到严重错误现将更快失败。

有关详细信息，请参阅[适用于 .NET 的媒体服务 SDK 中的重试逻辑]。

##<a id="april_changes_14"></a>2014 年 4 月编码器版本

### <a name="april_14_enocer_changes"></a>媒体服务编码器更新

* 增加了对引入使用 Grass Valley EDIUS 非线性编辑器创作的 AVI 文件的支持，该编辑器中使用 Grass Valley HQ/HQX 编解码器对视频进行轻度压缩。有关详细信息，请参阅 [Grass Valley Announces EDIUS 7 Streaming Through the Cloud]（Grass Valley 宣布通过云进行 EDIUS 7 流式处理）。

* 增加了对媒体编码器所生成文件指定命名约定的支持。有关详细信息，请参阅[控制媒体服务编码器输出文件名]。

* 增加了对视频和/或音频覆盖的支持。有关详细信息，请参阅[创建覆盖]。

* 增加了对拼接多个视频片段的支持。有关详细信息，请参阅[拼接视频片段]。

* 修复了与采用 MPEG-1 Audio layer 3（又名 MP3）进行音频编码的 MP4 转码相关的 Bug。


##<a id="jan_feb_changes_14"></a>2014 年 1/2 月版本

### <a name="jan_fab_14_donnet_changes"></a>Azure 媒体服务 .NET SDK 3.0.0.1、3.0.0.2 和 3.0.0.3

3\.0.0.1 和 3.0.0.2 中的更改包括：

* 修复了与具有 OrderBy 语句的 LINQ 查询的使用相关的问题。

* 将 [GitHub] 中的测试解决方案拆分为了基于单位的测试和基于方案的测试。

有关这些更改的更多详细信息，请参阅：[Azure 媒体服务 .NET SDK 3.0.0.1 和 3.0.0.2 版本]。

3\.0.0.3 中进行了以下更改：

* 将 Azure 存储依赖项升级为使用版本 3.0.3.0。

* 修复了 3.0.*.* 版本的向后兼容性问题。


##<a id="december_changes_13"></a>2013 年 12 月版本

### <a name="dec_13_donnet_changes"></a>Azure 媒体服务 .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x 版本与 2.4.x.x 版本不向后兼容。

媒体服务 SDK 当前的最新版本为 3.0.0.0。可从 Nuget 下载最新程序包或从 [GitHub] 获取资料。

自媒体服务 SDK 3.0.0.0 版本起，可以重复使用 [Azure Active Directory 访问控制服务 (ACS)] 令牌。有关详细信息，请参阅[使用适用于 .NET 的媒体服务 SDK 连接到媒体服务]主题中的“重复使用访问控制服务令牌”部分。

### <a name="dec_13_donnet_ext_changes"></a>Azure 媒体服务 .NET SDK 扩展 2.0.0.0

Azure 媒体服务 .NET SDK 扩展是一组扩展方法和帮助器函数，可简化你的代码，并令使用 Azure 媒体服务进行开发变得更加容易。可从 [Azure 媒体服务 .NET SDK 扩展]中获取最新资料。

##<a id="november_changes_13"></a>2013 年 11 月版本

### <a name="nov_13_donnet_changes"></a>Azure 媒体服务 .NET SDK 更改

自此版本起，适用于 .NET 的媒体服务 SDK 可处理调用媒体服务 REST API 层时可能发生的暂时性故障错误。

##<a id="august_changes_13"></a>2013 年 8 月版本

### <a name="aug_13_powershell_changes"></a>Azure SDK 工具中包含的媒体服务 PowerShell Cmdlet

[azure-sdk-tools] 中现在包含以下媒体服务 PowerShell cmdlet。

* Get-AzureMediaServices

	例如，`Get-AzureMediaServicesAccount`。

* New-AzureMediaServicesAccount

	例如，`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`。

* New-AzureMediaServicesKey

	例如，`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`。

* Remove-AzureMediaServicesAccount

	例如，`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`。

##<a id="june_changes_13"></a>2013 年 6 月版本

### <a name="june_13_general_changes"></a>Azure 媒体服务更改

本部分所述的变化是 2013 年 6 月媒体服务版本中包含的更新。

* 将多个存储帐户链接到一个媒体服务帐户的功能。

	StorageAccount
	
	Asset.StorageAccountName 和 Asset.StorageAccount

* 更新 Job.Priority 的功能。

* 与实体和属性相关的通知：

	JobNotificationSubscription
	
	NotificationEndPoint
	
	作业

* Asset.Uri

* Locator.Name

### <a name="june_13_dotnet_changes"></a>Azure 媒体服务 .NET SDK 更改

2013 年 6 月媒体服务 SDK 版本中包含以下更改。GitHub 上提供最新媒体服务 SDK。

* 自 2.3.0.0 版起，媒体服务 SDK 支持将多个存储帐户链接到一个媒体服务帐户。以下 API 支持此功能：
	
	IStorageAccount 类型。
	
	Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts 属性。
	
	StorageAccount 属性。
	
	StorageAccountName 属性。
	
	有关详细信息，请参阅[跨多个存储帐户管理媒体服务资产]。

* 与 API 相关的通知。自 2.2.0.0 版起，可侦听 Azure 队列存储通知。有关详细信息，请参阅[处理媒体服务作业通知]。
	
	Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions 属性。
	
	Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 类型。
	
	Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 类型。
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 类型。
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 类型。
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 类型。

* Azure 存储客户端 SDK 2.0 中的依赖项 (Microsoft.WindowsAzure.StorageClient.dll)。

* OData 5.5 中的依赖项 (Microsoft.Data.OData.dll)。


##<a id="december_changes_12"></a>2012 年 12 月版本

### <a name="dec_12_dotnet_changes"></a>Azure 媒体服务 .NET SDK 更改

* Intellisense：为许多类型增加了缺少的 Intellisense 文档。

* Microsoft.Practices.TransientFaultHandling.Core：修复了 SDK 仍依赖于此程序集的旧版本的问题。SDK 现在引用此程序集的 5.1.1209.1 版本。

修复了 2012 年 11 月版 SDK 中发现的问题：

* IAsset.Locators.Count：删除所有定位符后，现将在新的 IAsset 接口上正确报告此计数。

* IAssetFile.ContentFileSize：通过 IAssetFile.Upload(filepath) 进行上传后，现将正确设置此值。

* IAssetFile.ContentFileSize：现在可以在创建资产文件时设置此属性。此属性以前是只读的。

* IAssetFile.Upload(filepath)：修复了将多个文件上传到资产时，此同步上传方法引发以下错误的问题。错误为“服务器未能对请求进行身份验证。请确保授权标头的值采用正确形式，包括签名。”

* IAssetFile.UploadAsync：修复了不能同时上传 5 个以上文件的问题。

* IAssetFile.UploadProgressChanged：现在由 SDK 提供此事件。

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken)：现在提供了此方法重载。

* IAssetFile.DownloadAsync：修复了不能同时下载 5 以上个文件的问题。

* IAssetFile.Delete()：修复了未为 IAssetFile 上传文件时，调用删除可能引发异常的问题。

* Jobs：修复了使用作业模板将“MP4 到平滑流任务”和“PlayReady 保护任务”链接到一起时，根本不会创建任何任务的问题。

* EncryptionUtils.GetCertificateFromStore()：此方法不会再因证书配置问题找不到证书而引发空引用异常。

##<a id="november_changes_12"></a>2012 年 11 月版本

本部分所述的变化是 2012 年 11 月（2.0.0.0 版）SDK 中包含的更新。这些更改可能要求对 2012 年 6 月预览版 SDK 的代码进行修改或重写。

* 资产
	
	IAsset.Create(assetName) 是唯一的资产创建函数。IAsset.Create 不再在方法调用中上传文件。使用 IAssetFile 上传。
	
	IAsset.Publish 方法和 AssetState.Publish 枚举值已从媒体服务 SDK 中删除。必须重写依赖于此值的任何代码。

* FileInfo

	此类已由 IAssetFile 删除并取代。

	IAssetFiles

	IAssetFile 取代了 FileInfo 并具有不同的行为。若要使用它，请先实例化 IAssetFiles 对象，然后使用媒体服务 SDK 或 Azure 存储 SDK 上传文件。可以使用以下 IAssetFile.Upload 重载：

	* IAssetFile.Upload(filePath)：阻止线程的同步方法，建议仅在上传单个文件时使用。
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken)：异步方法。这是首选上传机制。

	已知 Bug：使用 cancellationToken 确实将取消上传；但是，任务的取消状态可以是多个状态中的任何一个。必须正确捕获并处理异常。

* 定位符
	
	已删除原点特定的版本。SAS 特定的 context.Locators.CreateSasLocator(asset, accessPolicy) 将标记为已弃用或通过 GA 删除。请参阅“新增功能”下的“定位符”部分以了解更新行为。


##<a id="june_changes_12"></a>2012 年 6 月预览版

以下是 11 月版 SDK 中的新增功能。

* 删除实体

	IAsset、IAssetFile、ILocator、IAccessPolicy、IContentKey 对象现在已从对象级别（即 IObject.Delete()）删除，而不要求在集合（即 cloudMediaContext.ObjCollection.Delete(objInstance)）中删除。

* 定位符

	现在必须使用 CreateLocator 方法创建定位符，并使用 LocatorType.SAS 或 LocatorType.OnDemandOrigin 枚举值作为你希望创建的特定类型定位符的参数。

	定位符增加了新的属性，以便更轻松地为你的内容获取可用的 URL。这种重新设计的定位符旨在为将来的第三方扩展提供更大的灵活性，并提高媒体客户端应用程序的易用性。

* 异步方法支持

	对所有方法增加了异步支持。





<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Azure 媒体服务 MSDN 论坛]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure 媒体服务 REST API 参考]: http://msdn.microsoft.com/zh-cn/library/azure/hh973617.aspx
[媒体服务定价详细信息]: /pricing/details/media-services/
[输入元数据]: http://msdn.microsoft.com/zh-cn/library/azure/dn783120.aspx
[输出元数据]: http://msdn.microsoft.com/zh-cn/library/azure/dn783217.aspx
[交付内容]: http://msdn.microsoft.com/zh-cn/library/azure/hh973618.aspx
[使用 Azure 媒体索引器对媒体文件进行索引]: /documentation/articles/media-services-index-content/
[StreamingEndpoint]: http://msdn.microsoft.com/zh-cn/library/azure/dn783468.aspx
[使用 Azure 媒体服务实时传送视频流]: /documentation/articles/media-services-manage-channels-overview/
[使用 AES-128 动态加密和密钥传递服务]: /documentation/articles/media-services-protect-with-aes128/
[使用 PlayReady 动态加密和许可证传递服务]: /documentation/articles/media-services-protect-with-drm/
[媒体服务 PlayReady 许可证模板概述]: http://msdn.microsoft.com/zh-cn/library/azure/dn783459.aspx
[流式处理存储加密内容]: /documentation/articles/media-services-dotnet-configure-asset-delivery-policy/
[Azure Management Portal]: https://manage.windowsazure.cn
[动态打包]: /documentation/articles/media-services-dynamic-packaging-overview/
[Nick Drouin 的博客]: http://blog-ndrouin.chinacloudsites.cn/hls-v3-new-old-thing/
[使用 PlayReady 保护平滑流]: /documentation/articles/media-services-static-packaging/
[适用于 .NET 的媒体服务 SDK 中的重试逻辑]: http://msdn.microsoft.com/zh-cn/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[控制媒体服务编码器输出文件名]: /documentation/articles/media-services-azure-media-customize-ame-presets/
[创建覆盖]: /documentation/articles/media-services-azure-media-customize-ame-presets/
[拼接视频片段]: /documentation/articles/media-services-azure-media-customize-ame-presets/
[Azure 媒体服务 .NET SDK 3.0.0.1 和 3.0.0.2 版本]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory 访问控制服务 (ACS)]: http://msdn.microsoft.com/zh-cn/library/hh147631.aspx
[使用适用于 .NET 的媒体服务 SDK 连接到媒体服务]: /documentation/articles/media-services-dotnet-connect_programmatically/
[Azure 媒体服务 .NET SDK 扩展]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[跨多个存储帐户管理媒体服务资产]: /documentation/articles/meda-services-managing-multiple-storage-accounts/
[处理媒体服务作业通知]: /documentation/articles/media-services-check-job-progress/#check_progress_with_queues
 

<!---HONumber=Mooncake_Quality_Review_1202_2016-->