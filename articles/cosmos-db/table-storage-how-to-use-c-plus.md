---
title: How to use Azure Table Storage and Azure Cosmos DB Table API with C++
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: b8fa0a3cebd87f4da1a47c605ba21b0cb10a2517
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220051"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak používat službu Azure Table Storage a rozhraní Table API služby Azure Cosmos DB pomocí C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

This guide shows you common scenarios by using the Azure Table storage service or Azure Cosmos DB Table API. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). This article covers the following scenarios:

* Create and delete a table
* Work with table entities

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. The recommended version is Storage Client Library 2.2.0, which is available by using [NuGet](https://www.nuget.org/packages/wastorage) or [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Create accounts

### <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++

In this guide, you use storage features from a C++ application. To do so, install the Azure Storage Client Library for C++.

To install the Azure Storage Client Library for C++, use the following methods:

* **Linux:** Follow the instructions given in the [Azure Storage Client Library for C++ README: Getting Started on Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) page.
* **Windows:** On Windows, use [vcpkg](https://github.com/microsoft/vcpkg) as the dependency manager. Follow the [quick-start](https://github.com/microsoft/vcpkg#quick-start) to initialize vcpkg. Then, use the following command to install the library:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

You can find a guide for how to build the source code and export to Nuget in the [README](https://github.com/Azure/azure-storage-cpp#download--install) file.

### <a name="configure-access-to-the-table-client-library"></a>Konfigurace přístupu ke klientské knihovně služby Table Storage

To use the Azure storage APIs to access tables, add the following `include` statements to the top of the C++ file:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Klient Azure Storage nebo klient Cosmos DB používá připojovací řetězec k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. When you run a client application, you must provide the storage connection string or Azure Cosmos DB connection string in the appropriate format.

### <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

This example shows how to declare a static field to hold the Azure Storage connection string:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Use the name of your Storage account for `<your_storage_account>`. For <your_storage_account_key>, use the access key for the Storage account listed in the [Azure portal](https://portal.azure.com). For information on Storage accounts and access keys, see [Create a storage account](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Nastavení připojovacího řetězce služby Azure Cosmos DB

This example shows how to declare a static field to hold the Azure Cosmos DB connection string:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Use the name of your Azure Cosmos DB account for `<your_cosmos_db_account>`. Enter your primary key for `<your_cosmos_db_account_key>`. Enter the endpoint listed in the [Azure portal](https://portal.azure.com) for `<your_cosmos_db_endpoint>`.

To test your application in your local Windows-based computer, you can use the Azure storage emulator that is installed with the [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simulujte služby Azure Blob, Queue a Table Storage a zpřístupňuje je na místním počítači pro vývoj. The following example shows how to declare a static field to hold the connection string to your local storage emulator:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

To start the Azure storage emulator, from your Windows desktop, select the **Start** button or the Windows key. Enter and run *Microsoft Azure Storage Emulator*. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce

You can use the `cloud_storage_account` class to represent your storage account information. To retrieve your storage account information from the storage connection string, use the `parse` method.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Next, get a reference to a `cloud_table_client` class. This class lets you get reference objects for tables and entities stored within the Table storage service. The following code creates a `cloud_table_client` object by using the storage account object you retrieved previously:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Create and add entities to a table

### <a name="create-a-table"></a>Vytvoření tabulky

A `cloud_table_client` object lets you get reference objects for tables and entities. The following code creates a `cloud_table_client` object and uses it to create a new table.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

To add an entity to a table, create a new `table_entity` object and pass it to `table_operation::insert_entity`. Následující kód používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Entities with the same partition key can be queried faster than entities with different partition keys. Using diverse partition keys allows for greater parallel operation scalability. Další informace najdete v tématu [Kontrolní seznam výkonu a škálovatelnosti služby Microsoft Azure Storage](../storage/common/storage-performance-checklist.md).

The following code creates a new instance of `table_entity` with some customer data to store. The code next calls `table_operation::insert_entity` to create a `table_operation` object to insert an entity into a table, and associates the new table entity with it. Finally, the code calls the `execute` method on the `cloud_table` object. The new `table_operation` sends a request to the Table service to insert the new customer entity into the `people` table.  

```cpp
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
```

### <a name="insert-a-batch-of-entities"></a>Vložení dávky entit

V rámci jedné operace zápisu můžete do služby Table Storage vložit dávku entit. The following code creates a `table_batch_operation` object, and then adds three insert operations to it. Each insert operation is added by creating a new entity object, setting its values, and then calling the `insert` method on the `table_batch_operation` object to associate the entity with a new insert operation. Then, the code calls `cloud_table.execute` to run the operation.  

```cpp
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
```

O dávkových operacích byste měli vědět několik věcí:

* You can do up to 100 `insert`, `delete`, `merge`, `replace`, `insert-or-merge`, and `insert-or-replace` operations in any combination in a single batch.  
* A batch operation can have a retrieve operation, if it's the only operation in the batch.  
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.  
* Velikost datové části dávkové operace je omezená na 4 MB.  

## <a name="query-and-modify-entities"></a>Query and modify entities

### <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu

To query a table for all entities in a partition, use a `table_query` object. The following code example specifies a filter for entities where `Smith` is the partition key. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.  

> [!NOTE]
> Tyto metody se ve službě Azure Cosmos DB v současné době nepodporují pro C++.

```cpp
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
```

The query in this example returns all the entities that match the filter criteria. Pokud máte velké tabulky a potřebujete často stahovat entity tabulky, doporučujeme místo toho ukládat data v objektech blob v úložišti Azure.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Načtení rozsahu entit v oddílu

If you don't want to query all the entities in a partition, you can specify a range. Combine the partition key filter with a row key filter. The following code example uses two filters to get all entities in partition `Smith` where the row key (first name) starts with a letter earlier than `E` in the alphabet, and then prints the query results.  

> [!NOTE]
> Tyto metody se ve službě Azure Cosmos DB v současné době nepodporují pro C++.

```cpp
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
```

### <a name="retrieve-a-single-entity"></a>Načtení jedné entity

Můžete napsat dotaz pro načtení jedné konkrétní entity. The following code uses `table_operation::retrieve_entity` to specify the customer `Jeff Smith`. This method returns just one entity, rather than a collection, and the returned value is in `table_result`. Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.  

```cpp
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
```

### <a name="replace-an-entity"></a>Nahrazení entity

Pokud chcete entitu nahradit, načtěte ji ze služby Table Storage, upravte objekt entity a pak uložte změny zpět do služby Table Storage. Následující kód změní telefonní číslo a e-mailovou adresu stávajícího zákazníka. Instead of calling `table_operation::insert_entity`, this code uses `table_operation::replace_entity`. This approach causes the entity to be fully replaced on the server, unless the entity on the server has changed since it was retrieved. If it has been changed, the operation fails. This failure prevents your application from overwriting a change made between the retrieval and update by another component. The proper handling of this failure is to retrieve the entity again, make your changes, if still valid, and then do another `table_operation::replace_entity` operation.  

```cpp
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
```

### <a name="insert-or-replace-an-entity"></a>Insert or replace an entity

`table_operation::replace_entity` operations fail if the entity has been changed since it was retrieved from the server. Furthermore, you must retrieve the entity from the server first in order for `table_operation::replace_entity` to be successful. Sometimes, you don't know if the entity exists on the server. The current values stored in it are irrelevant, because your update should overwrite them all. To accomplish this result, use a `table_operation::insert_or_replace_entity` operation. This operation inserts the entity if it doesn't exist. The operation replaces the entity if it exists. In the following code example, the customer entity for `Jeff Smith` is still retrieved, but it's then saved back to the server by using `table_operation::insert_or_replace_entity`. Jakékoli aktualizace provedené v entitě mezi operací načtení a aktualizace se přepíšou.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity

Dotaz na tabulku dokáže z entity načíst pouze několik vlastností. The query in the following code uses the `table_query::set_select_columns` method to return only the email addresses of entities in the table.  

```cpp
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
```

> [!NOTE]
> Dotazování několika vlastností entity je efektivnější operace než načítání všech vlastností.
>

## <a name="delete-content"></a>Delete content

### <a name="delete-an-entity"></a>Odstranění entity

You can delete an entity after you retrieve it. After you retrieve an entity, call `table_operation::delete_entity` with the entity to delete. Then call the `cloud_table.execute` method. The following code retrieves and deletes an entity with a partition key of `Smith` and a row key of `Jeff`.

```cpp
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
```

### <a name="delete-a-table"></a>Odstranění tabulky

Následující příklad kódu nakonec odstraní tabulku z účtu úložiště. A table that has been deleted is unavailable to be re-created for some time following the deletion.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Řešení potíží

For Visual Studio Community Edition, if your project gets build errors because of the include files *storage_account.h* and *table.h*, remove the **/permissive-** compiler switch:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na váš projekt a vyberte **Vlastnosti**.
1. V dialogovém okně **Stránky vlastností** rozbalte **Vlastnosti konfigurace**, pak rozbalte **C/C++** a vyberte **Jazyk**.
1. Nastavte **Režim přizpůsobení** na hodnotu **Ne**.

## <a name="next-steps"></a>Další kroky

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

Další informace o službě Azure Storage a rozhraní Table API ve službě Azure Cosmos DB najdete na následujících odkazech:

* [Úvod do rozhraní Table API](table-introduction.md)
* [Výpis prostředků Azure Storage v C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Klientská knihovna služby Storage pro C++ – referenční informace](https://azure.github.io/azure-storage-cpp)
* [Dokumentace ke službě Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
