---
title: Použití Table Storage Azure a Azure Cosmos DB rozhraní API pro tabulky s C++
description: Ukládat strukturovaná data v cloudu pomocí služby Azure Table Storage nebo Azure Cosmos DB rozhraní API pro tabulky pomocí jazyka C++.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489875"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak používat službu Azure Table Storage a rozhraní Table API služby Azure Cosmos DB pomocí C++
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Tato příručka popisuje běžné scénáře pomocí služby Azure Table Storage nebo Azure Cosmos DB rozhraní API pro tabulky. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Tento článek se zabývá následujícími scénáři:

* Vytvoření a odstranění tabulky
* Práce s entitami tabulky

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Doporučená verze je Klientská knihovna pro úložiště 2.2.0, která je k dispozici pomocí [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHubu](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Vytváření účtů

### <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++

V této příručce použijete funkce úložiště z aplikace C++. Provedete to tak, že nainstalujete klientskou knihovnu Azure Storage pro C++.

Pro instalaci klientské knihovny Azure Storage pro jazyk C++ použijte následující metody:

* **Linux:** Postupujte podle pokynů uvedených v tématu [Azure Storage Client Library for C++ Ready: Začínáme na stránce systému Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) .
* **Windows:** Ve Windows použijte [vcpkg](https://github.com/microsoft/vcpkg) jako správce závislostí. Postupujte podle pokynů pro [rychlé zahájení](https://github.com/microsoft/vcpkg#quick-start) inicializace vcpkg. Potom pomocí následujícího příkazu nainstalujte knihovnu:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Můžete najít průvodce pro sestavení zdrojového kódu a exportovat ho do NuGet v souboru [Readme](https://github.com/Azure/azure-storage-cpp#download--install) .

### <a name="configure-access-to-the-table-client-library"></a>Konfigurace přístupu ke klientské knihovně služby Table Storage

Pokud chcete používat rozhraní API služby Azure Storage pro přístup k tabulkám, přidejte `include` do horní části souboru C++ následující příkazy:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Klient Azure Storage nebo klient Cosmos DB používá připojovací řetězec k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění klientské aplikace musíte zadat připojovací řetězec úložiště nebo připojovací řetězec Azure Cosmos DB v příslušném formátu.

### <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

Tento příklad ukazuje, jak deklarovat statické pole pro uchování Azure Storage připojovacího řetězce:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Použijte název svého účtu úložiště pro `<your_storage_account>` . Pro <your_storage_account_key> použijte přístupový klíč pro účet úložiště uvedený v [Azure Portal](https://portal.azure.com). Informace o účtech úložiště a přístupových klíčích najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Nastavení připojovacího řetězce služby Azure Cosmos DB

Tento příklad ukazuje, jak deklarovat statické pole pro uchování Azure Cosmos DB připojovacího řetězce:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Použijte název účtu Azure Cosmos DB pro `<your_cosmos_db_account>` . Zadejte svůj primární klíč pro `<your_cosmos_db_account_key>` . Zadejte koncový bod uvedený v [Azure Portal](https://portal.azure.com) pro `<your_cosmos_db_endpoint>` .

K otestování vaší aplikace v místním počítači se systémem Windows můžete použít emulátor Azure Storage, který je nainstalovaný se sadou [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje služby Azure Blob, Queue a Table, které jsou k dispozici na vašem místním vývojovém počítači. Následující příklad ukazuje, jak deklarovat statické pole pro uložení připojovacího řetězce do místního emulátoru úložiště:  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Chcete-li spustit emulátor Azure Storage, vyberte z plochy systému Windows tlačítko **Start** nebo klávesu Windows. Zadejte a spusťte *emulátor úložiště Microsoft Azure*. Další informace najdete v tématu [použití emulátoru Azure Storage pro vývoj a testování](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce

Třídu můžete použít `cloud_storage_account` k reprezentaci informací o svém účtu úložiště. K načtení informací o účtu úložiště z připojovacího řetězce úložiště použijte `parse` metodu.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Potom Získejte odkaz na `cloud_table_client` třídu. Tato třída umožňuje získat referenční objekty pro tabulky a entity uložené ve službě Table Storage. Následující kód vytvoří `cloud_table_client` objekt pomocí objektu účtu úložiště, který jste dříve získali:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Vytvoření a přidání entit do tabulky

### <a name="create-a-table"></a>Vytvoření tabulky

`cloud_table_client`Objekt umožňuje získat referenční objekty pro tabulky a entity. Následující kód vytvoří `cloud_table_client` objekt a použije ho k vytvoření nové tabulky.

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

Chcete-li přidat entitu do tabulky, vytvořte nový `table_entity` objekt a předejte ho do `table_operation::insert_entity` . Následující kód používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné zadávat dotazy rychleji než entity s různými klíči oddílů. Použití různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Další informace najdete v tématu [Kontrolní seznam výkonu a škálovatelnosti služby Microsoft Azure Storage](../storage/blobs/storage-performance-checklist.md).

Následující kód vytvoří novou instanci `table_entity` s některými Zákaznickými daty k uložení. Kód při dalším volání `table_operation::insert_entity` k vytvoření `table_operation` objektu pro vložení entity do tabulky a přidruží k ní novou entitu tabulka. Nakonec kód volá `execute` metodu `cloud_table` objektu. Nový `table_operation` pošle požadavek do Table Service, aby se do tabulky vložila nová entita zákazníka `people` .  

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

V rámci jedné operace zápisu můžete do služby Table Storage vložit dávku entit. Následující kód vytvoří `table_batch_operation` objekt a poté přidá do něj tři operace vložení. Každá operace vložení je přidána vytvořením nového objektu entity, nastavením jeho hodnot a následným voláním `insert` metody `table_batch_operation` objektu k přidružení entity k nové operaci vložení. Potom kód volá `cloud_table.execute` ke spuštění operace.  

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

* V rámci jedné dávky můžete provádět operace až 100,,,, a `insert` `delete` `merge` `replace` `insert-or-merge` `insert-or-replace` v libovolné kombinaci.  
* Dávková operace může mít operaci načtení, pokud se jedná o jedinou operaci v dávce.  
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.  
* Velikost datové části dávkové operace je omezená na 4 MB.  

## <a name="query-and-modify-entities"></a>Dotazování a úpravy entit

### <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu

Chcete-li zadat dotaz na tabulku pro všechny entity v oddílu, použijte `table_query` objekt. Následující příklad kódu určuje filtr entit, kde `Smith` je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.  

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

Pokud nechcete zadávat dotazy na všechny entity v oddílu, můžete zadat rozsah. Kombinovat filtr klíče oddílu s filtrem klíče řádku. Následující příklad kódu používá dva filtry k získání všech entit v oddílu, `Smith` kde klíč řádku (jméno) začíná písmenem starším než `E` abecedou a následně vypíše výsledky dotazu.  

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

Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód používá `table_operation::retrieve_entity` k určení zákazníka `Jeff Smith` . Tato metoda vrátí pouze jednu entitu, nikoli kolekci, a vrácenou hodnotu je v `table_result` . Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.  

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

Pokud chcete entitu nahradit, načtěte ji ze služby Table Storage, upravte objekt entity a pak uložte změny zpět do služby Table Storage. Následující kód změní telefonní číslo a e-mailovou adresu stávajícího zákazníka. Místo volání `table_operation::insert_entity` , tento kód používá `table_operation::replace_entity` . Tento přístup způsobí, že entita se na serveru úplně nahradí, pokud se entita na serveru od načtení nezměnila. Pokud byl změněn, operace se nezdařila. Tato chyba zabraňuje vaší aplikaci přepsat změnu provedenou mezi načítáním a aktualizací jiné součásti. Řádným zpracováním této chyby je znovu načíst entitu, provést změny, pokud je stále platná, a pak provést jinou `table_operation::replace_entity` operaci.  

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

`table_operation::replace_entity` operace se nezdaří, pokud byla entita od načtení ze serveru změněna. Kromě toho je nutné nejprve načíst entitu ze serveru, aby `table_operation::replace_entity` byla úspěšná. Někdy nevíte, jestli na serveru existuje entita. Aktuální hodnoty, které jsou v něm uložené, nejsou důležité, protože by je vaše aktualizace měla přepsat. K dosažení tohoto výsledku použijte `table_operation::insert_or_replace_entity` operaci. Tato operace vloží entitu, pokud neexistuje. Operace nahradí entitu, pokud existuje. V následujícím příkladu kódu je entita zákazníka pro `Jeff Smith` stále načtena, ale je pak uložena zpět na server pomocí `table_operation::insert_or_replace_entity` . Jakékoli aktualizace provedené v entitě mezi operací načtení a aktualizace se přepíšou.  

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

Dotaz na tabulku dokáže z entity načíst pouze několik vlastností. Dotaz v následujícím kódu používá `table_query::set_select_columns` metodu pro vrácení pouze e-mailových adres entit v tabulce.  

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

## <a name="delete-content"></a>Odstranit obsah

### <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete po jejím načtení odstranit. Po načtení entity zavolejte `table_operation::delete_entity` s entitou, která se má odstranit. Pak zavolejte `cloud_table.execute` metodu. Následující kód načte a odstraní entitu s klíčem oddílu `Smith` a klíčem řádku `Jeff` .

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

Následující příklad kódu nakonec odstraní tabulku z účtu úložiště. Odstraněná tabulka není k dispozici, aby ji bylo možné po odstranění znovu vytvořit.  

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

## <a name="troubleshooting"></a>Odstraňování potíží

Pro Visual Studio Community Edition, pokud projekt získá chyby sestavení z důvodu souborů include *storage_account. h* a *Table. h* , odeberte přepínač kompilátoru **/Permissive-** :

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na váš projekt a vyberte **Vlastnosti**.
1. V dialogovém okně **Stránky vlastností** rozbalte **Vlastnosti konfigurace** , pak rozbalte **C/C++** a vyberte **Jazyk**.
1. Nastavte **Režim přizpůsobení** na hodnotu **Ne**.

## <a name="next-steps"></a>Další kroky

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

Další informace o službě Azure Storage a rozhraní Table API ve službě Azure Cosmos DB najdete na následujících odkazech:

* [Úvod do rozhraní Table API](table-introduction.md)
* [Výpis prostředků Azure Storage v C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Klientská knihovna služby Storage pro C++ – referenční informace](https://azure.github.io/azure-storage-cpp)
* [Dokumentace ke službě Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
