---
title: Použití služby Azure Table Storage nebo Azure Cosmos DB rozhraní API pro tabulky z Java
description: Ukládat strukturovaná data v cloudu pomocí služby Azure Table Storage nebo Azure Cosmos DB rozhraní API pro tabulky z Java.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: a5da5e1717f897d2236fd73f0fff525e157f7a0e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093685"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Jak používat službu Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB z Javy

[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

V tomto článku se dozvíte, jak vytvářet tabulky, ukládat data a provádět operace CRUD s daty. Vyberte buď Azure Table service, nebo rozhraní API pro tabulky Azure Cosmos DB. Ukázky jsou napsané v jazyce Java a používají [sadu Azure Storage SDK V8 for Java][Azure Storage SDK for Java]. Popsané scénáře zahrnují **vytváření**, **výpis** a **odstraňování** tabulek a také **vkládání**, **dotazování**, **úpravy** a **odstraňování** entit v tabulce. Další informace o tabulkách najdete v části [Další kroky](#next-steps).

> [!IMPORTANT]
> Poslední verze sady Azure Storage SDK podporující Table Storage je [V8][Azure Storage SDK for Java]. Brzy bude dostupná nová verze sady Table Storage SDK pro jazyk Java.

> [!NOTE]
> Sada SDK je k dispozici pro vývojáře používající službu Azure Storage na zařízeních s Androidem. Další informace najdete v tématu [Sada SDK služby Azure Storage pro Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Vytvoření účtu úložiště Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Vytvoření účtu služby Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java

V této příručce budete používat funkce úložiště, které můžete spustit místně v aplikaci Java nebo v kódu spuštěném ve webové roli nebo roli pracovního procesu v Azure.

Pokud chcete využít ukázky v tomto článku, nainstalujte si sadu Java Development Kit (JDK) a pak ve svém předplatném Azure vytvořte účet úložiště Azure nebo účet služby Azure Cosmos DB. Jakmile to provedete, ověřte, že váš vývojový systém splňuje minimální požadavky a obsahuje závislosti uvedené v úložišti [sady SDK služby Azure Storage pro Javu][Azure Storage SDK for Java] na GitHubu. Pokud váš systém splňuje tyto požadavky, můžete z tohoto adresáře podle uvedených pokynů stáhnout a nainstalovat ve svém systému knihovny služby Azure Storage pro Javu. Po dokončení těchto úloh můžete vytvořit aplikaci Java využívající příklady v tomto článku.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurace aplikace pro přístup ke službě Table Storage

Na začátek souboru Java, ve kterém chcete pro přístup k tabulkám použít rozhraní API úložiště Azure nebo rozhraní Table API služby Azure Cosmos DB, vložte následující příklady pro import:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-your-connection-string"></a>Přidat připojovací řetězec

Můžete se buď připojit k účtu služby Azure Storage nebo účtu Azure Cosmos DB rozhraní API pro tabulky. Získejte připojovací řetězec na základě typu účtu, který používáte.

### <a name="add-an-azure-storage-connection-string"></a>Přidání připojovacího řetězce úložiště Azure

Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění klientské aplikace musíte zadat připojovací řetězec úložiště v následujícím formátu a jako hodnoty **AccountName** a **AccountKey** použít název a primární přístupový klíč vašeho účtu úložiště uvedené na webu [Azure Portal](https://portal.azure.com). 

Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Přidání připojovacího řetězce rozhraní Table API služby Azure Cosmos DB

Účet služby Azure Cosmos DB používá připojovací řetězec k uložení koncového bodu tabulky a vašich přihlašovacích údajů. Při spuštění klientské aplikace musíte zadat připojovací řetězec služby Azure Cosmos DB v následujícím formátu a jako hodnoty **AccountName** a **AccountKey** použít název a primární přístupový klíč vašeho účtu služby Azure Cosmos DB uvedené na webu [Azure Portal](https://portal.azure.com). 

Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce služby Azure Cosmos DB:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

V aplikaci spuštěné v rámci role v Azure můžete tento řetězec uložit do konfiguračního souboru služby *ServiceConfiguration.cscfg* a přistupovat k němu voláním metody **RoleEnvironment.getConfigurationSettings**. Tady je příklad získání připojovacího řetězce *StorageConnectionString* z elementu **Settings** (Nastavení) v konfiguračním souboru služby:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Připojovací řetězec můžete uložit také do souboru config.properties vašeho projektu:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto metod získali připojovací řetězec úložiště.

## <a name="create-a-table"></a>Vytvoření tabulky

`CloudTableClient`Objekt umožňuje získat referenční objekty pro tabulky a entity. Následující kód vytvoří `CloudTableClient` objekt a použije ho k vytvoření nového `CloudTable` objektu, který představuje tabulku s názvem "lidé". 

> [!NOTE]
> Existují i jiné způsoby, jak vytvářet `CloudStorageAccount` objekty. Další informace naleznete `CloudStorageAccount` v tématu [Azure Storage odkaz na sadu SDK klienta]).
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Výpis tabulek

Pokud chcete zobrazit seznam tabulek, zavolejte metodu **CloudTableClient.listTables()**, která načte seznam názvů tabulek s možností iterace.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Entity se mapují na objekty Java pomocí implementace vlastní třídy `TableEntity` . Pro pohodlí `TableServiceEntity` Třída implementuje `TableEntity` a používá reflexi k mapování vlastností na metody getter a setter s názvem pro vlastnosti. Pokud chcete do tabulky přidat entitu, nejprve vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Entity se stejným klíčem oddílu je možné dotazovat rychleji než entity s různými klíči oddílu.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Operace s tabulkami zahrnující entity vyžadují `TableOperation` objekt. Tento objekt definuje operaci, která má být provedena u entity, která může být provedena s `CloudTable` objektem. Následující kód vytvoří novou instanci `CustomerEntity` třídy s některými Zákaznickými daty, která mají být uložena. Kód Next volá `TableOperation` . insertOrReplace * * pro vytvoření `TableOperation` objektu pro vložení entity do tabulky a přidruží k ní nový objekt `CustomerEntity` . Nakonec kód volá `execute` metodu `CloudTable` objektu, zadáním tabulky "lidé" a nového `TableOperation` , která poté pošle požadavek službě úložiště, aby vložila novou entitu zákazníka do tabulky "lidé", nebo nahradí entitu, pokud již existuje.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit

V rámci jedné operace zápisu můžete do služby Table Storage vložit dávku entit. Následující kód vytvoří `TableBatchOperation` objekt a poté přidá do něj tři operace vložení. Každá operace vložení je přidána vytvořením nového objektu entity, nastavením jeho hodnot a následným voláním `insert` metody `TableBatchOperation` objektu k přidružení entity k nové operaci vložení. Potom kód volá `execute` `CloudTable` objekt a určí tabulku "lidé" a `TableBatchOperation` objekt, který odešle dávku operací tabulky do služby úložiště v jednom požadavku.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

O dávkových operacích byste měli vědět několik věcí:

* V jedné dávce můžete provést jakoukoli kombinaci až 100 operací vložení, odstranění, sloučení, nahrazení, vložení nebo sloučení a vložení nebo nahrazení.
* Dávková operace může obsahovat operaci načtení, pokud se jedná o jedinou operaci v dávce.
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.
* Velikost datové části dávkové operace je omezená na 4 MB.

## <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu

Chcete-li zadat dotaz na tabulku pro entity v oddílu, můžete použít `TableQuery` . Volá `TableQuery.from` se, aby se vytvořil dotaz na konkrétní tabulce, která vrací zadaný typ výsledku. Následující kód určuje filtr pro entity, kde Smith je klíč oddílu. `TableQuery.generateFilterCondition` je pomocná metoda pro vytváření filtrů pro dotazy. Zavolejte `where` na odkaz vrácený `TableQuery.from` metodou pro použití filtru pro dotaz. Když se dotaz spustí s voláním `execute` na `CloudTable` objekt, vrátí `Iterator` se s `CustomerEntity` zadaným typem výsledku. Pak můžete použít `Iterator` vrácenou smyčku foreach k využití výsledků. Tento kód v konzole zobrazí pole každé entity z výsledků dotazu.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Načtení rozsahu entit v oddílu

Pokud nechcete dotazovat všechny entity v oddílu, můžete ve filtru zadat rozsah pomocí operátorů porovnání. Následující kód pomocí kombinace dvou filtrů získá všechny entity v oddílu Smith, kde klíč řádku (jméno) začíná písmenem abecedy až do písmene E. Pak zobrazí výsledky dotazu. Pokud používáte entity přidané do tabulky v části této příručky věnované dávkovému vložení, vrátí se teď pouze dvě entity (Ben a Denise Smith), Jeff Smith se nevrátí.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Načtení jedné entity

Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód volá `TableOperation.retrieve` Parametry klíče oddílu a klíče řádku k určení zákazníka "Jan Novák" místo vytvoření `Table Query` a použití filtrů ke stejnému účelu. Po spuštění operace načtení vrátí místo kolekce pouze jednu entitu. `getResultAsType`Metoda přetypování výsledku na typ cíle přiřazení, `CustomerEntity` objekt. Pokud tento typ není kompatibilní s typem zadaným pro dotaz, vyvolá se výjimka. Pokud se žádná entita zcela neshoduje s klíčem oddílu a řádku, vrátí se hodnota null. Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Úprava entity

Pokud chcete entitu upravit, načtěte ji ze služby Table Storage, proveďte změny objektu entity a uložte změny zpět do služby Table Storage pomocí operace nahrazení nebo sloučení. Následující kód změní telefonní číslo stávajícího zákazníka. Místo volání metody **TableOperation.insert**, kterou jsme použili k vložení, volá tento kód metodu **TableOperation.replace**. Metoda **CloudTable.execute** zavolá službu Table Storage a entita se nahradí, pokud ji od načtení touto aplikací nějaká jiná aplikace nezměnila. Pokud k tomu dojde, vyvolá se výjimka a entitu je potřeba znovu načíst, upravit a uložit. Tento vzorec opakování využívající optimistickou souběžnost je běžný v systémech distribuovaného úložiště.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity

Dotaz na tabulku dokáže z entity načíst pouze několik vlastností. Tato technika, které se říká projekce, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit. Dotaz v následujícím kódu používá `select` metodu pro vrácení pouze e-mailových adres entit v tabulce. Výsledky jsou probíhají do kolekce `String` s použitím `Entity Resolver` , což vede k převodu typu u entit vrácených ze serveru. Další informace o projekci najdete v článku [tabulky Azure: představení Upsert a projekce dotazů] [tabulky Azure: představení Upsert a projekce dotazů]. Projekce není podporována v emulátoru místního úložiště, takže tento kód bude spuštěn pouze při použití účtu ve službě Table Service.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define an Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Vložení nebo nahrazení entity

Často chcete do tabulky přidat entitu, aniž byste věděli, jestli v ní již neexistuje. Operace INSERT-or-Replace umožňuje vytvořit jeden požadavek, který entitu vloží, pokud neexistuje, nebo nahradit existující entitu, pokud k ní dojde. Následující kód staví na předchozích příkladech a vloží nebo nahradí entitu Walter Harp. Po vytvoření nové entity tento kód zavolá metodu **TableOperation.insertOrReplace**. Tento kód pak zavolá metodu **Execute** pro objekt **cloudové tabulky** s tabulkou a operaci vložení nebo nahrazení tabulky jako parametry. Pokud chcete aktualizovat pouze část entity, můžete místo toho použít metodu **TableOperation.insertOrMerge**. Příkaz INSERT-nebo-Replace není podporován v emulátoru místního úložiště, takže tento kód bude spuštěn pouze při použití účtu ve službě Table Service. Další informace o vložení nebo nahrazení a vložení nebo sloučení najdete v tomto [tabulkách Azure: představení Upsert a projekce dotazů] [tabulky Azure: představení Upsert a projekce dotazů].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete snadno odstranit po jejím načtení. Po načtení entity zavolejte `TableOperation.delete` s entitou, která se má odstranit. Pak zavolejte `execute` na `CloudTable` objekt. Následující kód načte a odstraní entitu zákazníka.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Odstranění tabulky

Následující kód nakonec odstraní tabulku z účtu úložiště. Přibližně 40 sekund po odstranění tabulky ji nemůžete znovu vytvořit. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Azure Table Storage v Javě](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Sada SDK služby Azure Storage pro Javu][Azure Storage SDK for Java]
* [Referenční informace ke klientské sadě SDK služby Azure Storage][Azure Storage Client SDK Reference]
* [Rozhraní REST API pro službu Azure Storage][Azure Storage REST API]
* [Blog týmu Azure Storage][Azure Storage Team Blog]

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/Azure/azure-storage-java/tree/v8.6.5
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Referenční informace ke klientské sadě SDK služby Azure Storage]: https://azure.github.io/azure-storage-java/
[Azure Storage REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/