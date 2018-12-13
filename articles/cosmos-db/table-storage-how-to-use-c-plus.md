---
title: Jak používat Azure Table Storage a Azure Cosmos DB Table API s C++
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 9bfc6ed2c781ddc54815665c492e7f2413033ca0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087112"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak používat službu Azure Table Storage a rozhraní Table API služby Azure Cosmos DB pomocí C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné scénáře pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Popsané scénáře zahrnují **vytvoření a odstranění tabulky** a **práci s entitami tabulky**.

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Doporučená verze klientské knihovny služby Storage je 2.2.0, která je k dispozici přes [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce budete používat funkce úložiště, které je možné spustit v rámci aplikace C++. Abyste mohli pokračovat, musíte si nainstalovat klientskou knihovnu služby Azure Storage pro C++ a vytvořit ve svém předplatném účet úložiště Azure.  

Klientskou knihovnu služby Azure Storage pro C++ můžete nainstalovat následujícími způsoby:

* **Linux:** Postupujte podle pokynů uvedených na stránce [souboru README klientské knihovny služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
* **Windows:** V sadě Visual Studio klikněte na **Nástroje > Správce balíčků NuGet > Konzola Správce balíčků**. Do [konzoly Správce balíčků NuGet](/nuget/tools/package-manager-console) zadejte následující příkaz a stiskněte Enter.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>Konfigurace přístupu ke klientské knihovně služby Table Storage
Na začátek souboru C++, ve kterém chcete pro přístup k tabulkám použít rozhraní API úložiště Azure, přidejte následující příklady pro vložení:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Klient Azure Storage nebo klient Cosmos DB používá připojovací řetězec k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění klientské aplikace musíte zadat připojovací řetězec úložiště nebo připojovací řetězec služby Azure Cosmos DB v příslušném formátu.

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage
 Jako hodnoty *AccountName* a *AccountKey* použijte název a přístupový klíč vašeho účtu služby Storage uvedené na webu [Azure Portal](https://portal.azure.com). Další informace o účtech služby Storage a přístupových klíčích najdete v tématu [Informace o účtech Azure Storage](../storage/common/storage-create-storage-account.md). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce služby Azure Storage:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Nastavení připojovacího řetězce služby Azure Cosmos DB
Jako hodnoty *Název účtu*, *Primární klíč* a *Koncový bod* použijte název vašeho účtu služby Azure Cosmos DB, váš primární klíč a koncový bod uvedené na webu [Azure Portal](https://portal.azure.com). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce služby Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


K testování aplikace na místním počítači s Windows můžete použít [emulátor úložiště](../storage/common/storage-use-emulator.md) Azure, který se instaluje spolu se sadou [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simulujte služby Azure Blob, Queue a Table Storage a zpřístupňuje je na místním počítači pro vývoj. Následující příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pokud chcete spustit emulátor úložiště Azure, klikněte na tlačítko **Spustit** nebo stiskněte klávesu Windows. Začněte zadávat **Emulátor úložiště Azure** a pak v seznamu aplikací vyberte **Emulátor úložiště Microsoft Azure**.  

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.  

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce
Jako reprezentaci informací o vašem účtu úložiště můžete použít třídu **cloud_storage_account**. K načtení informací o vašem účtu úložiště z připojovacího řetězce úložiště můžete použít metodu **parse**.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Dále získejte odkaz na třídu **cloud_table_client**, která umožňuje získat referenční objekty pro tabulky a entity uložené v rámci služby Table Storage. Následující kód vytvoří objekt **cloud_table_client** s použitím objektu účtu úložiště, který jsme načetli dříve:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Vytvoření tabulky
Objekt **cloud_table_client** umožňuje získat referenční objekty pro tabulky a entity. Následující kód vytvoří objekt **cloud_table_client** a použije ho k vytvoření nové tabulky.

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

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Pokud chcete do tabulky přidat entitu, vytvořte nový objekt **table_entity** a předejte ho do metody **table_operation::insert_entity**. Následující kód používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné se (v porovnání s těmi, které mají různé klíče oddílů) rychleji dotazovat, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Další informace najdete v tématu [Kontrolní seznam výkonu a škálovatelnosti služby Microsoft Azure Storage](../storage/common/storage-performance-checklist.md).

Následující kód vytvoří novou instanci objektu **table_entity** a několik zákaznických dat k uložení. Kód pak zavoláním metody **table_operation::insert_entity** vytvoří objekt **table_operation**, který do tabulky vloží entitu a přidruží k ní novou entitu tabulky. Nakonec kód zavolá metodu execute pro objekt **cloud_table**. A nový objekt **table_operation** odešle do služby Table Storage požadavek na vložené nové entity zákazníka do tabulky people.  

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

## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit
V rámci jedné operace zápisu můžete do služby Table Storage vložit dávku entit. Následující kód vytvoří objekt **table_batch_operation** a pak do něj přidá tři operace vložení. Jednotlivé operace vložení se přidají vytvořením nového objektu entity, nastavením jeho hodnot a zavoláním metody insert pro objekt **table_batch_operation**, která k entitě přidruží novou operaci vložení. Pak se zavolá metoda **cloud_table.execute**, která provede operaci.  

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

* V jedné dávce můžete provést jakoukoli kombinaci až 100 operací vložení, odstranění, sloučení, nahrazení, vložení nebo sloučení a vložení nebo nahrazení.  
* Dávková operace může obsahovat operaci načtení, pokud se jedná o jedinou operaci v dávce.  
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.  
* Velikost datové části dávkové operace je omezená na 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu
Pokud chcete zadat dotaz na tabulku pro všechny entity v oddílu, použijte objekt **table_query**. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.  

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

Dotaz v tomto příkladu načte všechny entity, které odpovídají kritériím filtru. Pokud máte velké tabulky a potřebujete často stahovat entity tabulky, doporučujeme místo toho ukládat data v objektech blob v úložišti Azure.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Načtení rozsahu entit v oddílu
Pokud nechcete, aby se zadával dotaz na všechny entity v oddílu, můžete zadat rozsah nakombinováním filtru klíče oddílu s filtrem klíče řádku. Následující příklad kódu používá dva filtry k získání všech entit v oddílu Smith, kde klíč řádku (jméno) začíná písmenem abecedy před písmenem E, a potom zobrazí výsledky dotazu.  

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

## <a name="retrieve-a-single-entity"></a>Načtení jedné entity
Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód používá metodu **table_operation::retrieve_entity** k určení zákazníka Jeff Smith. Tato metoda vrátí místo kolekce pouze jednu entitu a vrácená hodnota bude v objektu **table_result**. Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.  

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

## <a name="replace-an-entity"></a>Nahrazení entity
Pokud chcete entitu nahradit, načtěte ji ze služby Table Storage, upravte objekt entity a pak uložte změny zpět do služby Table Storage. Následující kód změní telefonní číslo a e-mailovou adresu stávajícího zákazníka. Místo volání metody **table_operation::insert_entity** tento kód používá metodu **table_operation::replace_entity**. To způsobí, že entita se na serveru plně nahradí, pokud se entita na serveru od načtení nezměnila, protože v takovém případě se operace nezdaří. Toto selhání zabrání vaší aplikaci v nechtěném přepsání změny provedené mezi načtením a aktualizací provedenou jinou součástí vaší aplikace. Toto selhání by se mělo správně zpracovat tak, že entitu znovu načtete, provedete požadované změny (pokud je stále ještě chcete provést) a pak provedete další operaci **table_operation::replace_entity**. V další části si ukážeme, jak toto chování potlačit.  

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

## <a name="insert-or-replace-an-entity"></a>Vložení nebo nahrazení entity
Operace **table_operation::replace_entity** selžou, pokud se entita od načtení ze serveru změnila. Kromě toho musíte entitu nejprve načíst ze serveru, aby operace **table_operation::replace_entity** proběhla úspěšně. V některých případech ale nevíte, jestli entita existuje na serveru a jestli jsou hodnoty, které jsou v ní aktuálně uložené, relevantní – vaše aktualizace by je měla všechny přepsat. K tomu použijete operaci **table_operation::insert_or_replace_entity**. Tato operace vloží entitu, pokud neexistuje, nebo ji nahradí, pokud existuje, a to bez ohledu na to, kdy byla provedena poslední aktualizace. V následujícím příkladu kódu se opět načte entita zákazníka Jeff Smith, ale pak se uloží zpět na server prostřednictvím metody **table_operation::insert_or_replace_entity**. Jakékoli aktualizace provedené v entitě mezi operací načtení a aktualizace se přepíšou.  

```cpp
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
```

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity
Dotaz na tabulku dokáže z entity načíst pouze několik vlastností. Dotaz v následujícím kódu pomocí metody **table_query::set_select_columns** vrátí pouze e-mailové adresy entit v tabulce.  

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
> 

## <a name="delete-an-entity"></a>Odstranění entity
Entitu můžete snadno odstranit po jejím načtení. Po načtení entity zavolejte metodu **table_operation::delete_entity** s entitou, kterou chcete odstranit. Potom zavolejte metodu **cloud_table.execute**. Následující kód načte a odstraní entitu s klíčem oddílu Smith a klíčem řádku Jeff.  

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

## <a name="delete-a-table"></a>Odstranění tabulky
Následující příklad kódu nakonec odstraní tabulku z účtu úložiště. Tabulku, která byla odstraněna, nebude možné po odstranění nějakou dobu znovu vytvořit.  

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
* Chyby sestavení v sadě Visual Studio 2017 Community Edition

  Pokud u vašeho projektu dochází k chybám sestavení kvůli vkládaným souborům storage_account.h a table.h, odeberte přepínač kompilátoru **/permissive-**. 
  - V **Průzkumníku řešení** klikněte pravým tlačítkem na váš projekt a vyberte **Vlastnosti**.
  - V dialogovém okně **Stránky vlastností** rozbalte **Vlastnosti konfigurace**, pak rozbalte **C/C++** a vyberte **Jazyk**.
  - Nastavte **Režim přizpůsobení** na hodnotu **Ne**.
   
## <a name="next-steps"></a>Další postup
Další informace o službě Azure Storage a rozhraní Table API ve službě Azure Cosmos DB najdete na následujících odkazech: 

* [Úvod do rozhraní Table API](table-introduction.md)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Výpis prostředků Azure Storage v C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Klientská knihovna služby Storage pro C++ – referenční informace](https://azure.github.io/azure-storage-cpp)
* [Dokumentace ke službě Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
