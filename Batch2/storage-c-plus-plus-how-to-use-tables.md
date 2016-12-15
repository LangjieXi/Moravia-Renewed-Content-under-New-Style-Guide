<properties
    pageTitle="如何使用表存储 (C++) | Azure"
    description="使用 Azure 表存储（一种 NoSQL 数据存储）将结构化数据存储在云中。"
    services="storage"
    documentationcenter=".net"
    author="dineshmurthy"
    manager="jahogg"
    editor="tysonn" />  

<tags
    ms.assetid="f191f308-e4b2-4de9-85cb-551b82b1ea7c"
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/16/2016"
    ms.author="dineshm" />

# 如何通过 C++ 使用表存储
[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## 概述
本指南将演示如何使用 Azure 表存储服务执行常见方案。示例用 C++ 编写，并使用[适用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。涉及的方案包括**创建和删除表**，以及**使用表条目**。

>[AZURE.NOTE] 本指南主要面向适用于 C++ 的 Azure 存储客户端库 1.0.0 版及更高版本。建议的版本是存储空间客户端库 2.2.0，它可以通过 [NuGet](http://www.nuget.org/packages/wastorage) 或 [GitHub](https://github.com/Azure/azure-storage-cpp/) 获得。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 创建 C++ 应用程序
在本指南中，将使用可在 C++ 应用程序内运行的存储功能。为此，需要安装适用于 C++ 的 Azure 存储客户端库，并在 Azure 订阅中创建 Azure 存储帐户。

若要安装适用于 C++ 的 Azure 存储客户端库，可使用以下方法：

* **Linux**：按照[适用于 C++ 的 Azure 存储客户端库自述文件](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)页中提供的说明进行操作。
* **Windows**：在 Visual Studio 主菜单中，单击“工具”->“NuGet 程序包管理器”->“程序包管理器控制台”。在 [NuGet 程序包管理器控制台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)窗口中输入以下命令，然后按 Enter。
  
     Install-Package wastorage

## 配置应用程序以访问表存储
将以下 include 语句添加到要在其中使用 Azure 存储 API 访问表的 C++ 文件的顶部：

	#include "was/storage_account.h"
	#include "was/table.h"

## 设置 Azure 存储连接字符串  
Azure 存储客户端使用存储连接字符串来存储用于访问数据管理服务的终结点和凭据。运行客户端应用程序时，必须提供以下格式的存储连接字符串。使用 [Azure 门户](https://portal.azure.cn)中列出的存储帐户的存储帐户名称和存储访问密钥作为 *AccountName* 和 *AccountKey* 值。有关存储帐户和访问密钥的信息，请参阅[关于 Azure 存储帐户](/documentation/articles/storage-create-storage-account/)。此示例演示如何声明一个静态字段以保存连接字符串：

	// Define the connection string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key;EndpointSuffix=core.chinacloudapi.cn"));

若要在本地基于 Windows 的计算机中测试应用程序，可以使用随 [Azure SDK](/downloads/) 一起安装的 Azure [存储模拟器](/documentation/articles/storage-use-emulator/)。存储模拟器是一种用于模拟本地开发计算机上提供的 Azure Blob、队列和表服务的实用程序。以下示例演示如何声明静态字段以将连接字符串保存到本地存储模拟器：

	// Define the connection string with Azure storage emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

若要启动 Azure 存储模拟器，请单击“开始”按钮或按 Windows 键。开始键入“Azure 存储模拟器”，然后从应用程序列表中选择“Microsoft Azure 存储模拟器”。

下面的示例假定你使用了这两个方法之一来获取存储连接字符串。

## 检索连接字符串
可以使用 **cloud\_storage\_account** 类来表示存储帐户信息。若要从存储连接字符串中检索存储帐户信息，可使用 parse 方法。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

接下来，获取对 **cloud\_table\_client** 类的引用，因为使用它可以获取表存储服务中存储的表和条目的引用对象。以下代码使用我们在上面检索到的存储帐户对象创建 **cloud\_table\_client** 对象：

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## 创建表
使用 **cloud\_table\_client** 对象，可以获得表和条目的引用对象。以下代码将创建 **cloud\_table\_client** 对象并使用它创建新表。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## 向表中添加条目
若要将条目添加到表，请创建一个新的 **table\_entity** 对象并将其传递到 **table\_operation::insert\_entity**。以下代码使用客户的名字作为行键，并使用姓氏作为分区键。条目的分区键和行键共同唯一地标识表中的条目。查询分区键相同的条目的速度快于查询分区键不同的条目的速度，但使用不同的分区键可实现更高的并行操作可伸缩性。有关详细信息，请参阅 [Microsoft Azure 存储性能和可伸缩性清单](/documentation/articles/storage-performance-checklist/)。

以下代码创建了包含要存储的某些客户数据的 **table\_entity** 类的新实例。接下来，该代码调用 **table\_operation::insert\_entity** 以创建一个 **table\_operation** 对象，以便将条目插入表中，并将新的表条目与之关联。最后，该代码调用 **cloud\_table** 对象的 execute 方法。而新的 **table\_operation** 向表服务发送请求，以将新的客户条目插入“people”表中。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();

	// Create a new customer entity.
	azure::storage::table_entity customer1(U("Harp"), U("Walter"));

	azure::storage::table_entity::properties_type& properties = customer1.properties();
	properties.reserve(2);
	properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

	properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

	// Create the table operation that inserts the customer entity.
	azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

	// Execute the insert operation.
	azure::storage::table_result insert_result = table.execute(insert_operation);

## 插入一批条目
可通过一个写入操作将一批条目插入到表服务。以下代码创建一个 **table\_batch\_operation** 对象，然后向其中添加三个插入操作。每个插入操作的添加方法如下：创建一个新的条目对象，设置它的值，然后对 **table\_batch\_operation** 对象调用 insert 方法以将条目与新的插入操作相关联。然后调用 **cloud\_table.execute** 以执行此操作。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define a batch operation.
	azure::storage::table_batch_operation batch_operation;

	// Create a customer entity and add it to the table.
	azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

	azure::storage::table_entity::properties_type& properties1 = customer1.properties();
	properties1.reserve(2);
	properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
	properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

	// Create another customer entity and add it to the table.
	azure::storage::table_entity customer2(U("Smith"), U("Ben"));

	azure::storage::table_entity::properties_type& properties2 = customer2.properties();
	properties2.reserve(2);
	properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
	properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

	// Create a third customer entity to add to the table.
	azure::storage::table_entity customer3(U("Smith"), U("Denise"));

	azure::storage::table_entity::properties_type& properties3 = customer3.properties();
	properties3.reserve(2);
	properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
	properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

	// Add customer entities to the batch insert operation.
	batch_operation.insert_or_replace_entity(customer1);
	batch_operation.insert_or_replace_entity(customer2);
	batch_operation.insert_or_replace_entity(customer3);

	// Execute the batch operation.
	std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

批处理操作的注意事项如下：

* 在单次批处理操作中最多可以执行 100 个插入、删除、合并、替换、插入或合并以及插入或替换操作（可以是这些操作的任意组合）。
* 批处理操作也可以包含检索操作，但前提是检索操作是批处理中仅有的操作。
* 单次批处理操作中的所有条目都必须具有相同的分区键。
* 批处理操作的数据负载限制为 4MB。

## 检索分区中的所有条目
若要查询表以获取分区中的所有条目，请使用 **table\_query** 对象。以下代码示例指定了一个筛选器，以筛选分区键为“Smith”的条目。此示例会将查询结果中每个条目的字段输出到控制台。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Construct the query operation for all customer entities where PartitionKey="Smith".
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Print the fields for each customer.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

此示例中的查询将检索出与筛选条件匹配的所有条目。如果有大型表并需要经常下载表条目，建议改为将数据存储在 Azure 存储 Blob 中。

## 检索分区中的一部分条目
如果不想查询分区中的所有条目，则可以通过结合使用分区键筛选器与行键筛选器来指定一个范围。以下代码示例使用两个筛选器来获取分区“Smith”中的、行键（名字）以字母“E”前面的字母开头的所有条目，然后输出查询结果。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table query.
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
		azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
		azure::storage::query_comparison_operator::equal, U("Smith")),
		azure::storage::query_logical_operator::op_and,
		azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Loop through the results, displaying information about the entity.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

## 检索单个条目
可编写查询以检索单个特定条目。以下代码使用 **table\_operation::retrieve\_entity** 来指定客户“Jeff Smith”。此方法只返回一个条目，而不是一个集合，并且返回的值在 **table\_result** 中。在查询中指定分区键和行键是从表服务中检索单个条目的最快方法。

	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Output the entity.
	azure::storage::table_entity entity = retrieve_result.entity();
	const azure::storage::table_entity::properties_type& properties = entity.properties();

	std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
		<< U(", Property1: ") << properties.at(U("Email")).string_value()
		<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## 替换条目
若要替换条目，请从表服务中检索它，修改条目对象，然后将更改保存回表服务。以下代码更改现有客户的电话号码和电子邮件地址。此代码不是调用 **table\_operation::insert\_entity**，而是使用 **table\_operation::replace\_entity**。这将导致在服务器上完全替换该条目，除非服务器上的该条目自检索到它以后发生更改，在此情况下，该操作将失败。操作失败将防止应用程序无意中覆盖应用程序的其他组件在检索与更新之间所做的更改。正确处理此失败的方法是再次检索条目，进行更改（如果仍有效），然后执行另一个 **table\_operation::replace\_entity** 操作。下一节将演示如何重写此行为。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Replace an entity.
	azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
	properties_to_replace.reserve(2);

	// Specify a new phone number.
	properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

	// Specify a new email address.
	properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

	// Create an operation to replace the entity.
	azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result replace_result = table.execute(replace_operation);

## 插入或替换条目
如果该条目自从服务器中检索到它以后发生更改，则 **table\_operation::replace\_entity** 操作将失败。此外，必须首先从服务器中检索该条目，**table\_operation::replace\_entity** 才能成功。但是，有时不知道服务器上是否存在该条目以及存储在其中的当前值是否无关 - 更新操作应将其全部覆盖。为实现此目的，请使用 **table\_operation::insert\_or\_replace\_entity** 操作。如果该条目不存在，此操作将插入它，如果存在则替换它，而不考虑上次更新时间。在以下代码示例中，仍将检索 Jeff Smith 的客户条目，但稍后会通过 **table\_operation::insert\_or\_replace\_entity** 将其保存回服务器。将覆盖在检索与更新操作之间对条目进行的任何更新。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

	// Create an operation to insert-or-replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## 查询条目属性的子集  
对表的查询可以只检索条目的几个属性。以下代码中的查询使用 **table\_query::set\_select\_columns** 方法，仅返回表中条目的电子邮件地址。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define the query, and select only the Email property.
	azure::storage::table_query query;
	std::vector<utility::string_t> columns;

	columns.push_back(U("Email"));
	query.set_select_columns(columns);

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Display the results.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

		const azure::storage::table_entity::properties_type& properties = it->properties();
		for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
		{
			std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
		}

		std::wcout << std::endl;
	}

>[AZURE.NOTE] 查询条目的几个属性是比检索所有属性更高效的操作。

## 删除条目
检索到条目后可将其轻松删除。检索到条目后，对要删除的条目调用 **table\_operation::delete\_entity**。然后调用 **cloud\_table.execute** 方法。以下代码检索并删除分区键为“Smith”、行键为“Jeff”的条目。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);  

## 删除表
最后，以下代码示例将从存储帐户中删除表。在删除表之后的一段时间内无法重新创建它。

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);

## 后续步骤
既已了解表存储的基础知识，可打开以下链接了解有关 Azure 存储的详细信息：

-	[如何通过 C++ 使用 Blob 存储](/documentation/articles/storage-c-plus-plus-how-to-use-blobs/)
-	[如何通过 C++ 使用队列存储](/documentation/articles/storage-c-plus-plus-how-to-use-queues/)
-	[使用 C++ 列出 Azure 存储资源](/documentation/articles/storage-c-plus-plus-enumeration/)
-	[适用于 C++ 的存储空间客户端库参考](http://azure.github.io/azure-storage-cpp)
-	[Azure 存储文档](/documentation/services/storage/)
 

<!---HONumber=Mooncake_1128_2016-->