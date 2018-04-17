---
title: Jak používat Azure Table Storage a Azure Cosmos DB s jazykem C++ | Microsoft Docs
description: Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: f191f308-e4b2-4de9-85cb-551b82b1ea7c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 60c419a45e795d5968b58fbb6b469d06750a0679
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak používat Azure Table storage a rozhraní API služby Azure Cosmos DB tabulky s C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Azure Table storage nebo Azure Cosmos DB tabulky API. Ukázky jsou napsané v C++ a použít [Klientská knihovna pro úložiště Azure pro jazyk C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Pokryté scénáře zahrnují **vytváření a odstraňování tabulek** a **práce s entity tabulky**.

> [!NOTE]
> Tato příručka se zaměřuje Klientská knihovna pro úložiště Azure pro jazyk C++ verze 1.0.0 a vyšší. Doporučená verze je klientská knihovna pro úložiště 2.2.0, která je k dispozici prostřednictvím [NuGet](http://www.nuget.org/packages/wastorage) nebo [Githubu](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu Azure Cosmos DB tabulky rozhraní API
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce bude používat funkce úložiště, které lze spustit v rámci aplikace C++. V takovém případě budete muset nainstalovat Klientská knihovna pro úložiště Azure pro jazyk C++ a vytvoření účtu úložiště Azure ve vašem předplatném Azure.  

Pokud chcete nainstalovat Klientská knihovna pro úložiště Azure pro jazyk C++, můžete použít následující metody:

* **Linux:** postupujte podle pokynů na [Klientská knihovna pro úložiště Azure pro C++ – soubor README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) stránky.  
* **Windows:** v sadě Visual Studio, klikněte na tlačítko **nástroje > Správce balíčků NuGet > Konzola správce balíčků**. Pomocí následujícího příkazu do [Konzola správce balíčků NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) a stiskněte klávesu Enter.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>Konfigurace přístupu k tabulce klientské knihovny
Přidejte následující příkazy na začátek souboru C++, ve které chcete používat službu Azure storage rozhraní API pro přístup k tabulky obsahovat:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Klienta Azure Storage nebo Cosmos DB klienta používá k uložení koncových bodů a pověření pro přístup ke službám správy dat připojovací řetězec. Při spuštění klienta aplikace, je nutné zadat připojovací řetězec úložiště nebo Azure Cosmos DB připojovací řetězec v příslušném formátu.

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavit připojovací řetězec službě Azure Storage
 Použijte název účtu úložiště a přístupový klíč pro účet úložiště, které jsou uvedené v [portálu Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. Informace o účtech úložiště a přístupové klávesy, najdete v části [účty Azure Storage](../storage/common/storage-create-storage-account.md). Tento příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovacího řetězce úložiště Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Nastavit připojovací řetězec databázi Azure Cosmos
Použijte název účtu Azure Cosmos DB, primární klíč a koncový bod uvedený v [portálu Azure](https://portal.azure.com) pro *název účtu*, *primární klíč*, a  *Koncový bod* hodnoty. Tento příklad ukazuje, jak můžou deklarovat statické pole pro Azure Cosmos DB připojovací řetězec:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


K testování aplikace v místním počítači a systému Windows, můžete použít Azure [emulátor úložiště](../storage/common/storage-use-emulator.md) který se instaluje s [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje služby Azure Blob, fronty a tabulky, které jsou k dispozici na místním vývojovém počítači. Následující příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec k vaší emulátor místního úložiště:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Chcete-li spustit emulátoru úložiště Azure, klikněte na tlačítko **spustit** tlačítko nebo stiskněte klávesu Windows. Začněte psát **emulátoru úložiště Azure**a potom vyberte **emulátor úložiště Microsoft Azure** ze seznamu aplikací.  

Následující ukázky předpokládejme, že používáte jednu z těchto dvou metod k získání připojovacího řetězce úložiště.  

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce
Můžete použít **cloud_storage_account** třída představující informace o účtu úložiště. Chcete-li načíst informace o účtu úložiště z připojovacího řetězce úložiště, můžete použít **analyzovat** metoda.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

V dalším kroku získat odkaz na **cloud_table_client** třídy, jako umožňuje získat odkaz na objekty pro tabulky a entity, které jsou uložené v rámci služby úložiště Table. Následující kód vytvoří **cloud_table_client** objekt pomocí objektu účtu úložiště jsme načíst výše:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Vytvoření tabulky
A **cloud_table_client** objektu umožňuje získat odkaz na objekty pro tabulky a entity. Následující kód vytvoří **cloud_table_client** objektu a pomocí něj vytvořit novou tabulku.

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
Pokud chcete přidat entitu do tabulky, vytvořte novou **table_entity** objektu a předejte ji do **table_operation::insert_entity**. Následující kód používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Entity se stejným klíčem oddílu můžete zadat dotaz rychleji než ty, které se různé klíče oddílů, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operaci. Další informace najdete v tématu [Microsoft Azure storage výkon a škálovatelnost kontrolní seznam](../storage/common/storage-performance-checklist.md).

Následující kód vytvoří novou instanci třídy **table_entity** s některé zákaznické údaje uložit. Další kód zavolá metodu **table_operation::insert_entity** k vytvoření **table_operation** objektu pro vložení entity do tabulky a přidruží novou entitu tabulky. Nakonec kód volá metodu execute na **cloud_table** objektu. A nové **table_operation** odešle požadavek do služby Table pro vložení nové entity zákazníka do tabulky "lidé".  

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
V rámci jedné operace zápisu můžete vložit dávku entit do služby Table. Následující kód vytvoří **table_batch_operation** objektu a potom se přidají tři operace k němu vložení. Každé operace insert přidá se při vytvoření nového objektu entity, nastavení jeho hodnoty a potom volání metody insert u **table_batch_operation** objekt, který chcete přidružit nové operace insert entity. Potom **cloud_table.execute** je volána k provedení operace.  

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

Některé věci poznamenat ohledně dávkových operací:  

* Až 100 vložení, odstranění, sloučení, nahraďte, operace insert nebo sloučení a vložení nebo nahrazení můžete provádět v libovolné kombinace v jedné dávce.  
* Dávková operace může mít operaci načtení, pokud je ale jediná operace v dávce.  
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.  
* Dávkové operace je omezena na 4 MB datovou částí.  

## <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu
Dotaz na tabulku pro všechny entity v oddílu, použijte **table_query** objektu. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.  

> [!NOTE]
> Tyto metody nejsou aktuálně podporovány pro jazyk C++ v Azure Cosmos DB.

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

V tomto příkladu dotaz zobrazí všechny entity, které odpovídají kritériím filtru. Pokud máte velké tabulky a potřebovat stáhnout entity tabulky často, doporučujeme, aby data ukládáte do objektů BLOB Azure storage místo.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Načtení rozsahu entit v oddílu
Pokud nechcete, aby se zadával dotaz na všechny entity v oddílu, můžete zadat rozsah nakombinováním filtru klíče oddílu s filtrem klíče řádku. Následující příklad kódu používá dva filtry k získání všech entit v oddílu Smith, kde klíč řádku (jméno) začíná písmenem abecedy před písmenem E, a potom zobrazí výsledky dotazu.  

> [!NOTE]
> Tyto metody nejsou aktuálně podporovány pro jazyk C++ v Azure Cosmos DB.

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
Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód používá **table_operation::retrieve_entity** k určení zákazníka 'Jeff Smith'. Tato metoda vrátí místo kolekce pouze jednu entitu a vrácená hodnota v **table_result**. Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.  

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
Jako náhrada za entitu, načtěte ji ze služby Table, upravte objekt entity a potom uložte změny zpět do služby Table. Následující kód změní stávající zákazník telefonní číslo a e-mailovou adresu. Místo volání **table_operation::insert_entity**, tento kód používá **table_operation::replace_entity**. To způsobí, že entita se na serveru plně nahradí, pokud se entita na serveru od načtení nezměnila, protože v takovém případě se operace nezdaří. Toto selhání zabrání vaší aplikaci v nechtěném přepsání změny provedené mezi načtením a aktualizací provedenou jinou součástí vaší aplikace. Správné zpracování této chyby je, že entitu znovu načtete, provedené změny (Pokud je stále platný) a pak provedete další **table_operation::replace_entity** operaci. V další části si ukážeme, jak toto chování potlačit.  

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
**table_operation::replace_entity** operace se nezdaří, pokud byl změněn entita od načtení ze serveru. Kromě toho musí načtení entity ze serveru první v pořadí pro **table_operation::replace_entity** úspěšný. V některých případech ale nevíte, pokud entita existuje na serveru a aktuální hodnoty v ní uloženy jsou důležité – vaše aktualizace by je měla všechny přepsat. Chcete-li dosáhnout, použijte **table_operation::insert_or_replace_entity** operaci. Tato operace vloží entitu, pokud neexistuje, nebo ji nahradí, pokud existuje, a to bez ohledu na to, kdy byla provedena poslední aktualizace. V následujícím příkladu kódu je entita zákazník Jeff Smith stále načtena, ale pak je uložena zpět na server prostřednictvím **table_operation::insert_or_replace_entity**. Jakékoli aktualizace provedené v entitě mezi načtením a operace aktualizace budou přepsány.  

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
Dotaz na tabulku můžete načíst jenom několik z entity. Dotaz v následující kód používá **table_query::set_select_columns** metoda vrátí pouze e-mailové adresy entit v tabulce.  

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
> Dotazování několik vlastností z entity, je efektivnější operace než načítání všechny vlastnosti.
> 
> 

## <a name="delete-an-entity"></a>Odstranění entity
Entitu můžete snadno odstranit po jejím načtení. Jakmile se načíst entity, volání **table_operation::delete_entity** s entity, která má odstranit. Potom zavolejte **cloud_table.execute** metoda. Následující kód načte a odstraní entitu s klíčem oddílu pro "Smith" a klíč řádku z "Jan".  

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
* Chyby ve Visual Studio 2017 Community Edition sestavení

  Pokud váš projekt získá chyby sestavení z důvodu zahrnout soubory storage_account.h a table.h, odeberte **/ projektovou-** přepínače kompilátoru. 
  - V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**.
  - V **stránky vlastností** dialogové okno, rozbalte seznam **vlastnosti konfigurace**, rozbalte položku **C/C++**a vyberte **jazyk**.
  - Nastavit **shoda režimu** k **ne**.
   
## <a name="next-steps"></a>Další postup
Další informace o Azure Storage a Table API v Azure Cosmos DB na následujících odkazech: 

* [Úvod do tabulky rozhraní API](table-introduction.md)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Seznam prostředků úložiště Azure v jazyce C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Klientská knihovna pro úložiště pro C++ – referenční informace](http://azure.github.io/azure-storage-cpp)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
