---
title: Jak používat Azure Table storage nebo rozhraní API služby Azure Cosmos DB tabulky z prostředí Java | Microsoft Docs
description: Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 4ac25fd9e1d7233546b34da89eb1bcaf37f6f38b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Jak používat Azure Table storage nebo Azure Cosmos DB tabulky API z Javy
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Přehled
Tento článek ukazuje, jak provádět běžné scénáře s využitím služby Azure Table storage a Azure Cosmos DB. Ukázky jsou napsané v jazyce Java a použít [sada SDK úložiště Azure pro jazyk Java][Azure Storage SDK for Java]. Pokryté scénáře zahrnují **vytváření**, **výpis**, a **odstraňování** tabulky, a také **vkládání**, **dotazování**, **úprava**, a **odstraňování** entity v tabulce. Další informace o tabulkách najdete v tématu [další kroky](#next-steps) části.

> [!NOTE]
> Sada SDK je k dispozici pro vývojáře, kteří na zařízení se systémem Android používají Azure Storage. Další informace najdete v tématu [sada SDK úložiště Azure pro Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu Azure Cosmos DB tabulky rozhraní API
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
V této příručce bude používat funkce úložiště, které můžou běžet v aplikaci Java, místně nebo v kód spuštěný ve webové roli nebo role pracovního procesu v Azure.

Pokud chcete používat ukázky v tomto článku, nainstalujte Java Development Kit (JDK) a potom vytvořit účet úložiště Azure ve vašem předplatném Azure. Jakmile provedete, ověřte, zda váš vývojový systém splňuje minimální požadavky a závislosti, které jsou uvedeny v [sada SDK úložiště Azure pro jazyk Java] [ Azure Storage SDK for Java] úložišti na Githubu. Pokud váš systém splňuje tyto požadavky, můžete podle pokynů ke stažení a instalaci knihovny úložiště Azure pro jazyk Java systému z tohoto úložiště. Po dokončení těchto úloh můžete vytvořit aplikaci Java, která se používá v příkladech v tomto článku.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurace aplikace k přístupu k table storage
Přidejte následující příkazy pro import do horní části souboru Java, ve které chcete použít rozhraním API v Azure storage nebo Cosmos DB tabulky rozhraní API služby Azure s tabulkami přístup:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-an-azure-storage-connection-string"></a>Přidat připojovací řetězec úložiště Azure
Klienta Azure storage používá připojovací řetězec úložiště k ukládání koncových bodů a pověření pro přístup ke službám dat správy. Když spustíte v aplikaci klienta, je nutné zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a primární přístupový klíč pro účet úložiště uvedené v [portál Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. 

Tento příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

## <a name="add-an-azure-cosmos-db-connection-string"></a>Přidat připojovací řetězec databázi Azure Cosmos
Účet Azure Cosmos DB používá připojovací řetězec k uložení koncový bod tabulek a přihlašovacích údajů. Když spustíte v aplikaci klienta, je nutné zadat připojovací řetězec Azure Cosmos DB v následujícím formátu, pomocí názvu účtu Azure Cosmos DB a je primární přístupový klíč pro účet uveden v [portál Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. 

Tento příklad ukazuje, jak můžou deklarovat statické pole pro Azure Cosmos DB připojovací řetězec:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

V spuštěna aplikace v rámci role v Azure, můžete uložit tento řetězec v konfiguračním souboru služby *souboru ServiceConfiguration.cscfg*, a můžete k němu přístup pomocí volání  **RoleEnvironment.getConfigurationSettings** metoda. Tady je příklad získávání připojovacího řetězce z **nastavení** element s názvem *StorageConnectionString* v konfiguračním souboru služby:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Také můžete uložit připojovací řetězec v souboru projektu na config.properties:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

Následující ukázky předpokládejme, že používáte jednu z těchto metod k získání připojovacího řetězce úložiště.

## <a name="create-a-table"></a>Vytvoření tabulky
A **CloudTableClient** objektu umožňuje získat odkaz na objekty pro tabulky a entity. Následující kód vytvoří **CloudTableClient** objektu a použije k vytvoření nového **CloudTable** objekt, který reprezentuje tabulku s názvem "uživatelé". 

> [!NOTE]
> Existují jiné způsoby vytvoření **CloudStorageAccount** objekty; Další informace najdete v tématu **CloudStorageAccount** v [odkaz SDK klienta úložiště Azure]).
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

## <a name="list-the-tables"></a>Seznam tabulek
Chcete-li získat seznam tabulek, zavolejte **CloudTableClient.listTables()** metoda pro načtení iterable seznam názvů tabulek.

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
Entity se mapují na objekty Java pomocí vlastní třídy implementace **TableEntity**. Pro usnadnění práce **TableServiceEntity** třída implementuje **TableEntity** a pomocí reflexe vlastnosti metody getter a setter s názvem vlastnosti. Chcete-li do tabulky přidat entitu, nejdřív vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která používá jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Entity se stejným klíčem oddílu můžete položit dotaz rychleji než ty, které se různé klíče oddílů.

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

Operace s tabulkou zahrnující entity vyžadují **TableOperation** objektu. Tento objekt definuje operace provést u entity, které lze spustit s **CloudTable** objektu. Následující kód vytvoří novou instanci třídy **CustomerEntity** se některé zákaznické údaje uložit. Další kód zavolá metodu **TableOperation.insertOrReplace** k vytvoření **TableOperation** objektu pro vložení entity do tabulky a přidruží nový **CustomerEntity** s ním. Nakonec kód volá **provést** metodu **CloudTable** objekt, zadání "lidé" tabulky a nové **TableOperation**, který pak odešle požadavek na službu úložiště vložení nové entity zákazníka do tabulky "lidé", nebo nahrazení entity, pokud již existuje.

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
V rámci jedné operace zápisu můžete vložit dávku entit do služby table. Následující kód vytvoří **TableBatchOperation** objekt a potom přidá tři operace k němu vložení. Každé operace insert přidá se při vytvoření nového objektu entity, nastavení jeho hodnoty a potom volání **vložit** metodu **TableBatchOperation** objekt, který chcete přidružit nové operace insert entity. Potom kód zavolá metodu **provést** na **CloudTable** objekt, zadání "lidé" tabulky a **TableBatchOperation** objekt, který dávku operace s tabulkou se odešle do služby úložiště v jedné žádosti.

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

Některé věci poznamenat ohledně dávkových operací:

* Můžete provést až 100 vložit, odstranit, sloučení, nahradit, vložit nebo sloučení a vložit nebo nahrazovat operace v libovolné kombinace v jedné dávce.
* Dávková operace může mít operaci načtení, pokud je ale jediná operace v dávce.
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.
* Dávkové operace je omezený na datovou částí 4 MB volného místa.

## <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu
Dotaz na tabulku pro entity v oddílu, můžete použít **TableQuery**. Volání **TableQuery.from** k vytvoření dotazu na konkrétní tabulku, která vrátí typ určeného výsledku. Následující kód určuje filtr pro entity, kde je: Váša' klíč oddílu. **TableQuery.generateFilterCondition** je metoda helper pro vytvoření filtrů pro dotazy. Volání **kde** na odkaz na vrácený **TableQuery.from** metoda použít filtr dotazu. Provedení dotazu s výzvou k **provést** na **CloudTable** objektu se vrátí **Iterator** s **CustomerEntity** způsobit zadaný typ. Pak můžete použít **Iterator** , vrátí se v pro každou smyčku využívat výsledky. Tento kód zobrazí pole každé entity ve výsledcích dotazu do konzoly.

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
Pokud nechcete, aby k dotazování všechny entity v oddílu, můžete zadat rozsah pomocí relačních operátorů ve filtru. Následující kód kombinuje dva filtry k získání všech entit v oddílu "Smith", kde klíč řádku (jméno) začíná písmenem až "E" abecedy. Potom zobrazí výsledky dotazu. Pokud používáte entity přidat do tabulky v dávce vložení této příručce, pouze dvě entity, jsou vráceny tentokrát (Ben a Karel Smith); Jeff Smith není zahrnutý.

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
Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód volání **TableOperation.retrieve** s oddílem klíč a řádek klíče parametry k určení zákazníka "Jeff Smith", místo vytvoření **TableQuery** a stejnou věc udělat pomocí filtrů. Po provedení operace načtení vrátí místo kolekce pouze jednu entitu. **GetResultAsType** metoda vrhá výsledek, který má typ přiřazení cíle, **CustomerEntity** objektu. Pokud tento typ není kompatibilní s typem parametru dotazu, je vyvolána výjimka. Pokud žádná entita má přesný klíč oddílu a řádku shodovat, je vrácena hodnota null. Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.

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

## <a name="modify-an-entity"></a>Změnu entity
Upravte entitu, načtěte ji ze služby table, změnit objekt entity a uložte změny zpět do služby table v operaci nahrazení nebo merge. Následující kód změní telefonní číslo stávajícího zákazníka. Místo volání **TableOperation.insert** jako jsme to udělali vložení, tento kód zavolá **TableOperation.replace**. **CloudTable.execute** metoda volá služby table a nahradí entitu, pokud jiná aplikace změnil v čase vzhledem k tomu, že jejím načtení této aplikace. Pokud k tomu dojde, je vyvolána výjimka, a entity musí být načteny, upravit a uložit znovu. Tento vzor opakování optimistickou metodu souběžného je běžné v systém distribuovaného úložiště.

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
Dotaz na tabulku můžete načíst jenom několik z entity. Tato technika, které se říká projekce, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit. Dotaz v následující kód používá **vyberte** metoda vrátí pouze e-mailové adresy entit v tabulce. Výsledky jsou promítnout do kolekce **řetězec** za pomoci **EntityResolver**, na které se převod typů na entity vrácená serverem. Další informace o projekce v [tabulky Azure: Představení funkcí Upsert a projekce dotazu][Azure Tables: Introducing Upsert and Query Projection]. Všimněte si, že projekci nepodporuje emulátor místního úložiště, takže tento kód spustí pouze v případě, že se pomocí účtu služby table.

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

    // Define a Entity resolver to project the entity to the Email value.
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
Často budete chtít do tabulky přidat entitu, aniž by věděly, pokud již existuje v tabulce. Operace vložení nebo nahrazení můžete jedné žádosti, která budou vkládat entity, pokud ji neexistuje nebo pokud k tomu nahradí existující. Sestavování na předchozí příklady, následující kód vložení nebo nahrazení entity pro "Walter tuleňů grónských". Po vytvoření nové entity, tento kód zavolá **TableOperation.insertOrReplace** metoda. Tento kód pak zavolá **provést** na **CloudTable** objektu s tabulkou a úlohy insert nebo nahradit tabulku operace jako parametry. Chcete-li aktualizovat jenom část entity, **TableOperation.insertOrMerge** metodu je možné použít místo. Všimněte si, že vložení nebo nahrazení není podporována na emulátor místního úložiště, takže tento kód spustí pouze v případě, že se pomocí účtu služby table. Můžete další informace o vložení nebo nahrazení a vložení nebo sloučení v tomto [tabulky Azure: Představení funkcí Upsert a projekce dotazu][Azure Tables: Introducing Upsert and Query Projection].

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
Entitu můžete snadno odstranit po jejím načtení. Jakmile se načíst entity, volání **TableOperation.delete** s entity, která má odstranit. Potom zavolejte **provést** na **CloudTable** objektu. Následující kód načte a odstraní entitu zákazníka.

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
Následující kód nakonec odstraní tabulku z účtu úložiště. O 40 sekund po odstranění tabulky, nelze znovu vytvořit ji. 

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

## <a name="next-steps"></a>Další postup

* [Začínáme se službou Azure tabulky v jazyce Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Úložiště Azure SDK pro jazyk Java][Azure Storage SDK for Java]
* [Referenční informace sady SDK úložiště Azure klienta][odkaz SDK klienta úložiště Azure]
* [REST API pro Azure Storage][Azure Storage REST API]
* [Blog týmu Azure Storage][Azure Storage Team Blog]

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[odkaz SDK klienta úložiště Azure]: http://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
