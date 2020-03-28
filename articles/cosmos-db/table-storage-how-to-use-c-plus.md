---
title: Použití azure table storage a rozhraní API tabulky Azure Cosmos DB s C++
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: e6d61e329ba91f53b11ace4d258b35950e188dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76771223"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak používat službu Azure Table Storage a rozhraní Table API služby Azure Cosmos DB pomocí C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Tato příručka ukazuje běžné scénáře pomocí služby Azure Table storage nebo Azure Cosmos DB Table API. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Tento článek popisuje následující scénáře:

* Vytvoření a odstranění tabulky
* Práce s entitami tabulky

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Doporučená verze je klientská knihovna úložiště 2.2.0, která je k dispozici pomocí [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Vytvořit účty

### <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++

V této příručce používáte funkce úložiště z aplikace jazyka C++. Chcete-li tak učinit, nainstalujte klientskou knihovnu úložiště Azure pro C++.

Chcete-li nainstalovat klientskou knihovnu úložiště Azure pro C++, použijte následující metody:

* **Linux:** Postupujte podle pokynů uvedených v [klientské knihovně úložiště Azure pro C++ README: Začínáme na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stránce Linux.
* **Windows:** V systému Windows použijte [vcpkg](https://github.com/microsoft/vcpkg) jako správce závislostí. Postupujte podle [rychlého startu](https://github.com/microsoft/vcpkg#quick-start) a inicializovat vcpkg. Potom použijte následující příkaz k instalaci knihovny:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Můžete najít návod, jak vytvořit zdrojový kód a exportovat do Nuget v souboru [README.](https://github.com/Azure/azure-storage-cpp#download--install)

### <a name="configure-access-to-the-table-client-library"></a>Konfigurace přístupu ke klientské knihovně služby Table Storage

Pokud chcete k tabulkám použít api `include` úložiště Azure, přidejte do horní části souboru C++následující příkazy:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Klient Azure Storage nebo klient Cosmos DB používá připojovací řetězec k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění klientské aplikace, musíte zadat připojovací řetězec úložiště nebo připojovací řetězec Azure Cosmos DB v příslušném formátu.

### <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

Tento příklad ukazuje, jak deklarovat statické pole pro uložení připojovacího řetězce Azure Storage:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Použijte název účtu úložiště `<your_storage_account>`pro aplikaci . Pro <your_storage_account_key> použijte přístupový klíč pro účet úložiště uvedený na [webu Azure Portal](https://portal.azure.com). Informace o účtech úložiště a přístupových klíčích najdete [v tématu Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Nastavení připojovacího řetězce služby Azure Cosmos DB

Tento příklad ukazuje, jak deklarovat statické pole pro uložení připojovacího řetězce Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Použijte název účtu Azure Cosmos `<your_cosmos_db_account>`DB pro . Zadejte primární `<your_cosmos_db_account_key>`klíč pro aplikaci . Zadejte koncový bod uvedený na `<your_cosmos_db_endpoint>`webu Azure [Portal](https://portal.azure.com) for .

K testování aplikace na místním počítači s Windows můžete použít emulátor úložiště Azure, který se instaluje spolu se sadou [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simulujte služby Azure Blob, Queue a Table Storage a zpřístupňuje je na místním počítači pro vývoj. Následující příklad ukazuje, jak deklarovat statické pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pokud chcete spustit emulátor úložiště Azure, vyberte z plochy Windows tlačítko **Start** nebo klíč Windows. Zadejte a spusťte *emulátor úložiště Microsoft Azure*. Další informace najdete [v tématu použití emulátoru úložiště Azure pro vývoj a testování](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce

Třídu `cloud_storage_account` můžete použít k reprezentaci informací o účtu úložiště. Chcete-li načíst informace o účtu úložiště `parse` z připojovacího řetězce úložiště, použijte metodu.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Dále získat odkaz na `cloud_table_client` třídu. Tato třída umožňuje získat referenční objekty pro tabulky a entity uložené v rámci služby table storage. Následující kód vytvoří `cloud_table_client` objekt pomocí objektu účtu úložiště, který jste načetli dříve:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Vytvoření a přidání entit do tabulky

### <a name="create-a-table"></a>Vytvoření tabulky

Objekt `cloud_table_client` umožňuje získat referenční objekty pro tabulky a entity. Následující kód vytvoří `cloud_table_client` objekt a použije jej k vytvoření nové tabulky.

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

Chcete-li do tabulky přidat entitu, vytvořte nový `table_entity` objekt a předajte jej aplikaci `table_operation::insert_entity`. Následující kód používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Entity se stejným klíčem oddílu mohou být dotazovány rychleji než entity s různými klíči oddílu. Použití různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Další informace najdete v tématu [Kontrolní seznam výkonu a škálovatelnosti služby Microsoft Azure Storage](../storage/common/storage-performance-checklist.md).

Následující kód vytvoří novou `table_entity` instanci s některá data zákazníka pro uložení. Kód další `table_operation::insert_entity` volání vytvořit `table_operation` objekt vložit entitu do tabulky a přidruží nové entity tabulky s ním. Nakonec kód volá `execute` metodu `cloud_table` na objektu. Nový `table_operation` odešle požadavek na službu Tabulka vložit `people` nový odběratel entity do tabulky.  

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

V rámci jedné operace zápisu můžete do služby Table Storage vložit dávku entit. Následující kód vytvoří `table_batch_operation` objekt a přidá k němu tři operace vložení. Každá operace vložení je přidána vytvořením nového objektu `insert` entity, `table_batch_operation` nastavením jeho hodnot a následným voláním metody na objektu, která přidruží entitu k nové operaci vložení. Potom kód volá `cloud_table.execute` ke spuštění operace.  

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

* V libovolné kombinaci v `insert` `delete`jedné `merge` `replace`dávce můžete provést až 100 , , , `insert-or-merge`, a `insert-or-replace` operací.  
* Operace dávky může mít operaci načtení, pokud je to jediná operace v dávce.  
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.  
* Velikost datové části dávkové operace je omezená na 4 MB.  

## <a name="query-and-modify-entities"></a>Dotaz a úpravy entit

### <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu

Chcete-li zadat dotaz na tabulku pro `table_query` všechny entity v oddílu, použijte objekt. Následující příklad kódu určuje filtr pro `Smith` entity, kde je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.  

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

Dotaz v tomto příkladu vrátí všechny entity, které odpovídají kritériím filtru. Pokud máte velké tabulky a potřebujete často stahovat entity tabulky, doporučujeme místo toho ukládat data v objektech blob v úložišti Azure.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Načtení rozsahu entit v oddílu

Pokud nechcete dotazovat všechny entity v oddílu, můžete zadat oblast. Zkombinujte filtr klíče oddílu s filtrem klíče řádku. Následující příklad kódu používá dva filtry k `Smith` získání všech entit v oddílu, `E` kde klíč řádku (křestní jméno) začíná písmenem starším než v abecedě, a poté vytiskne výsledky dotazu.  

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

Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód `table_operation::retrieve_entity` používá k `Jeff Smith`určení zákazníka . Tato metoda vrátí pouze jednu entitu, nikoli `table_result`kolekci a vrácená hodnota je v . Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.  

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

Pokud chcete entitu nahradit, načtěte ji ze služby Table Storage, upravte objekt entity a pak uložte změny zpět do služby Table Storage. Následující kód změní telefonní číslo a e-mailovou adresu stávajícího zákazníka. Místo volání `table_operation::insert_entity`tento kód `table_operation::replace_entity`používá . Tento přístup způsobí, že entita bude plně nahrazena na serveru, pokud se entita na serveru od načtení nezměnila. Pokud byla změněna, operace se nezdaří. Toto selhání zabrání aplikaci v přepsání změny provedené mezi načítání a aktualizace jinou komponentou. Správné zpracování této chyby je znovu načíst entitu, provést změny, `table_operation::replace_entity` pokud je stále platný, a pak provést jinou operaci.  

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

### <a name="insert-or-replace-an-entity"></a>Vložení nebo nahrazení entity

`table_operation::replace_entity`operace se nezdaří, pokud byla entita od načtení ze serveru změněna. Kromě toho je nutné načíst entitu `table_operation::replace_entity` ze serveru nejprve, aby byla úspěšná. Někdy nevíte, zda entita existuje na serveru. Aktuální hodnoty uložené v něm jsou irelevantní, protože aktualizace by měla přepsat všechny. K dosažení tohoto výsledku `table_operation::insert_or_replace_entity` použijte operaci. Tato operace vloží entitu, pokud neexistuje. Operace nahradí entitu, pokud existuje. V následujícím příkladu kódu je `Jeff Smith` entita zákazníka pro stále načtena, ale `table_operation::insert_or_replace_entity`pak je uložena zpět na server pomocí . Jakékoli aktualizace provedené v entitě mezi operací načtení a aktualizace se přepíšou.  

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

Dotaz na tabulku dokáže z entity načíst pouze několik vlastností. Dotaz v následujícím kódu `table_query::set_select_columns` používá metodu k vrácení pouze e-mailové adresy entit v tabulce.  

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

## <a name="delete-content"></a>Odstranění obsahu

### <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete odstranit po načtení. Po načtení entity `table_operation::delete_entity` volejte s entitou odstranit. Pak volání `cloud_table.execute` metody. Následující kód načte a odstraní entitu `Smith` s klíčem oddílu a klíčem řádku aplikace `Jeff`.

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

Následující příklad kódu nakonec odstraní tabulku z účtu úložiště. Tabulka, která byla odstraněna, není po určitou dobu po odstranění k dispozici.  

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

Pro Visual Studio Community Edition, pokud váš projekt získá chyby sestavení z důvodu zahrnout soubory *storage_account.h* a *table.h*, odeberte přepínač kompilátoru **/tolerantní:**

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na váš projekt a vyberte **Vlastnosti**.
1. V dialogovém okně **Stránky vlastností** rozbalte **Vlastnosti konfigurace**, pak rozbalte **C/C++** a vyberte **Jazyk**.
1. Nastavte **Režim přizpůsobení** na hodnotu **Ne**.

## <a name="next-steps"></a>Další kroky

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

Další informace o službě Azure Storage a rozhraní Table API ve službě Azure Cosmos DB najdete na následujících odkazech:

* [Úvod do rozhraní Table API](table-introduction.md)
* [Výpis prostředků Azure Storage v C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Klientská knihovna služby Storage pro C++ – referenční informace](https://azure.github.io/azure-storage-cpp)
* [Dokumentace k úložišti Azure](https://azure.microsoft.com/documentation/services/storage/)
