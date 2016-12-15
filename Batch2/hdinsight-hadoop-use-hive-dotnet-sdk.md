<properties
	pageTitle="使用 HDInsight .NET SDK 运行 Hive 查询 | Azure"
	description="了解如何使用 HDInsight .NET SDK 将 Hadoop 作业提交到 Azure HDInsight Hadoop。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.date="04/06/2016"
	wacn.date=""/>

# 使用 HDInsight .NET SDK 运行 Hive 查询

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]


了解如何使用 HDInsight .NET SDK 提交 Hive 查询。

##先决条件

开始阅读本文之前，必须具备以下条件：

- **HDInsight 中有 Hadoop 群集**。请参阅[创建群集和 SQL 数据库](/documentation/articles/hdinsight-use-sqoop#create-cluster-and-sql-database)。
- **Visual Studio 2012/2013/2015**。

##使用 HDInsight .NET SDK 提交 Hive 查询

HDInsight .NET SDK 提供了 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。

以下示例使用用户交互式身份验证。要使用非交互式身份验证，请参阅 [Create non-interactive authentication .NET HDInsight applications](/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications)（创建非交互式身份验证 .NET HDInsight 应用程序）

**提交作业**

1. 在 Visual Studio 中创建 C# 控制台应用程序。
2. 通过 Nuget 包管理器控制台运行以下命令。

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. 使用以下代码：

		using System;
		using System.Collections.Generic;
		using System.Security;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHDInsightJobDotNet
		{
		    class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
		
		        private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.cn";
		        private const string ExistingClusterUsername = "<Cluster Username>";
		        private const string ExistingClusterPassword = "<Cluster User Password>";
		
		        private const string DefaultStorageAccountName = "<Default Storage Account Name>";
		        private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
		        private const string DefaultStorageContainerName = "<Default Blob Container Name>";
		
		        static void Main(string[] args)
		        {
		            System.Console.WriteLine("The application is running ...");
		
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();

		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), creds.Token);
		        }
		        
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                Query = "SHOW TABLES",
		                Defines = defines,
		                Arguments = args
		            };
		
		            Console.WriteLine("Submitting the Hive job to the cluster...");
		            var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            var jobId = jobResponse.JobSubmissionJsonResponse.Id;
		            Console.WriteLine("Response status code is " + jobResponse.StatusCode);
		            Console.WriteLine("JobId is " + jobId);

		            Console.WriteLine("Waiting for the job completion ...");
		
		            // Wait for job completion
		            var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
		            while (!jobDetail.Status.JobComplete)
		            {
		                Thread.Sleep(1000);
		                jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
		            }
		
		            // Get job output
		            var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
		                DefaultStorageContainerName);
		            var output = (jobDetail.ExitValue == 0)
		                ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
		                : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
		            
		            Console.WriteLine("Job output is: ");
		            
		            using (var reader = new StreamReader(output, Encoding.UTF8))
		            {
		            	string value = reader.ReadToEnd();
		            	Console.WriteLine(value);
		            }
		        }
		    }
		}

5. 按 **F5** 运行应用程序。


## 后续步骤

在本文中，你已经学习了几种创建 HDInsight 群集的方法。要了解更多信息，请参阅下列文章：

* [Azure HDInsight 入门][hdinsight-get-started]
* [在 HDInsight 中创建 Hadoop 群集][hdinsight-provision]
* [使用 Azure 门户管理 HDInsight 中的 Hadoop 群集](/documentation/articles/hdinsight-administer-use-management-portal-v1)
* [HDInsight .NET SDK 参考](https://msdn.microsoft.com/zh-cn/library/mt271028.aspx)
* [将 Pig 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-pig)
* [将 Sqoop 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-sqoop)
* [创建非交互式身份验证 .NET HDInsight 应用程序](/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications)


[hdinsight-provision]: /documentation/articles/hdinsight-provision-clusters-v1
[hdinsight-get-started]: /documentation/articles/hdinsight-hadoop-tutorial-get-started-windows-v1

<!---HONumber=Mooncake_Quality_Review_1202_2016-->