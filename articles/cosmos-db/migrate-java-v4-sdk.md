---
title: Migrace aplikace tak, aby používala sadu Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos)
description: Naučte se, jak upgradovat stávající aplikaci v jazyce Java z použití starší sady Azure Cosmos DB Java SDK na novější sadu Java SDK 4,0 (balíček com. Azure. Cosmos) pro jádro (SQL) API.
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658531"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrace aplikace na používání sady Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Další informace o této sadě SDK najdete v Azure Cosmos DB zpráva k [vydání verze](sql-api-sdk-java-v4.md)Java SDK v4, [úložiště služby Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB tipy k [výkonu](performance-tips-java-sdk-v4-sql.md)Java SDK v4 Azure Cosmos DB a příručka pro [odstraňování potíží s](troubleshoot-java-sdk-v4-sql.md)rozhraním Java SDK v4.
>

Tento článek vysvětluje, jak upgradovat stávající aplikaci Java, která používá starší sadu Azure Cosmos DB Java SDK, na novější Azure Cosmos DB Java SDK 4,0 for Core (SQL) API. Balíček Azure Cosmos DB Java SDK v4 odpovídá `com.azure.cosmos` balíčku. Pokyny v tomto dokumentu můžete použít při migraci aplikace z některé z následujících Azure Cosmos DB sad Java SDK: 

* Synchronizace sady Java SDK 2. x. x
* Async Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Mapování balíčku Azure Cosmos DB Java SDK

V následující tabulce jsou uvedeny různé Azure Cosmos DB Java SDK, název balíčku a informace o verzi:

| Java SDK| Datum vydání | Sada rozhraní API   | Maven jar  | Název balíčku Java  |Referenční informace k rozhraním API   | Poznámky k verzi  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Červen 2018    | Asynchronní (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [Rozhraní API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Zpráva k vydání verze](sql-api-sdk-async-java.md) |
| Synchronizace 2. x. x     | Září 2018    | Sync   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [Rozhraní API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Zpráva k vydání verze](sql-api-sdk-java.md)  |
| 3. x. x    | Červenec 2019    | Async (Reactor) –/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [Rozhraní API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Červen 2020   | Async (Reactor) –/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [Rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Změny implementace na úrovni sady SDK

Níže jsou uvedené klíčové rozdíly v implementaci mezi různými sadami SDK:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava se nahrazuje třídou Reactor v Azure Cosmos DB Java SDK verze 3. x. x a 4,0

Pokud nejste obeznámeni s asynchronním programováním nebo reaktivním programováním, přečtěte si [Průvodce vzorem reaktoru](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) pro Úvod do asynchronního programování a reaktoru projektu. Tato příručka může být užitečná, pokud jste v minulosti používali Azure Cosmos DB synchronizaci sady Java SDK 2. x. x nebo Azure Cosmos DB Java SDK 3. x. x Sync API.

Pokud jste používali Azure Cosmos DB Async Java SDK 2. x. x a plánujete migrovat na sadu 4,0 SDK, přečtěte si článek [Průvodce RxJava a příručka](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) k převádění RxJava kódu na použití reaktoru.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK V4 má přímý režim připojení v rozhraních API Async i Sync.

Pokud jste používali Azure Cosmos DB synchronizaci sady Java SDK 2. x. x, Všimněte si, že přímý režim připojení založený na protokolu TCP (na rozdíl od protokolu HTTP) je implementován v Azure Cosmos DB Java SDK 4,0 pro rozhraní API Async i Sync.

## <a name="api-level-changes"></a>Změny úrovně rozhraní API

Níže jsou uvedené změny úrovně rozhraní API v Azure Cosmos DB Java SDK 4. x. x v porovnání s předchozími sadami SDK (Java SDK 3. x. x, Async Java SDK 2. x. x a Sync Java SDK 2. x. x):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB konvence vytváření názvů pro Java SDK":::

* Azure Cosmos DB Java SDK 3. x. x a 4,0 odkazuje na prostředky klienta jako `Cosmos<resourceName>` . Například,, `CosmosClient` `CosmosDatabase` `CosmosContainer` . Vzhledem k tomu, že verze 2. x. x, Azure Cosmos DB Java SDK, nemají jednotné schéma pojmenování.

* Azure Cosmos DB Java SDK 3. x. x a 4,0 nabízí jak synchronizační, tak asynchronní rozhraní API.

  * **Java SDK 4,0** : všechny třídy patří do rozhraní API pro synchronizaci, pokud není název třídy připojen pomocí `Async` After `Cosmos` .

  * **Java SDK 3. x. x**: všechny třídy patří do ASYNCHRONNÍHO rozhraní API, pokud není název třídy připojen pomocí `Async` After `Cosmos` .

  * **Async Java SDK 2. x. x**: názvy tříd jsou podobné synchronizaci sady Java SDK 2. x. x, ale název začíná na *Async*.

### <a name="hierarchical-api-structure"></a>Hierarchická struktura rozhraní API

Azure Cosmos DB Java SDK 4,0 a 3. x. x zavádí hierarchickou strukturu rozhraní API, která uspořádá klienty, databáze a kontejnery vnořeným způsobem, jak je znázorněno v následujícím 4,0 fragmentu kódu sady SDK:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Ve verzi 2. x. x sady Azure Cosmos DB Java SDK jsou všechny operace s prostředky a dokumenty prováděny prostřednictvím instance klienta.

### <a name="representing-documents"></a>Reprezentace dokumentů

V Azure Cosmos DB Java SDK 4,0, Custom POJO 's a `JsonNodes` jsou dvě možnosti čtení a zápisu dokumentů z Azure Cosmos DB.

V sadě Azure Cosmos DB Java SDK 3. x. x `CosmosItemProperties` je objekt zpřístupněn veřejným rozhraním API a obsluhován jako reprezentace dokumentu. Tato třída již není veřejně vystavena ve verzi 4,0.

### <a name="imports"></a>Objem

* Balíčky Azure Cosmos DB Java SDK 4,0 začínají na `com.azure.cosmos`
* Azure Cosmos DB balíčky Java SDK 3. x. x začínají na `com.azure.data.cosmos`
* Azure Cosmos DB balíčky rozhraní API pro synchronizaci Java SDK 2. x. x začínají na `com.microsoft.azure.documentdb`

* Azure Cosmos DB Java SDK 4,0 umístí do vnořeného balíčku několik tříd `com.azure.cosmos.models` . Mezi tyto balíčky patří:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Rozhraní API pro asynchronní rozhraní API pro všechny výše uvedené balíčky
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... atd.

### <a name="accessors"></a>Přístupové objekty

Azure Cosmos DB sada Java SDK 4,0 zpřístupňuje `get` a `set` metody pro přístup ke členům instance. Například `CosmosContainer` instance `container.getId()` a `container.setId()` metody.

To se liší od Azure Cosmos DB Java SDK 3. x. x, která zpřístupňuje rozhraní Fluent. Například `CosmosSyncContainer` instance má `container.id()` přetížení, aby získala nebo nastavila `id` hodnotu.

## <a name="code-snippet-comparisons"></a>Porovnání fragmentů kódu

### <a name="create-resources"></a>Vytvoření prostředků

Následující fragment kódu ukazuje rozdíly v způsobu vytváření prostředků mezi asynchronními rozhraními API 4,0, 3. x. x a 2. x. x synchronizace:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Operace položky

Následující fragment kódu ukazuje rozdíly v tom, jak se provádějí operace s položkami mezi asynchronními rozhraními API 4,0, 3. x. x a 2. x. x synchronizace:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Indexování

Následující fragment kódu ukazuje rozdíly v způsobu vytváření indexování mezi asynchronními rozhraními API 4,0, 3. x. x a 2. x. x synchronizace:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Uložené procedury

Následující fragment kódu ukazuje rozdíly v způsobu vytváření uložených procedur mezi asynchronními rozhraními API 4,0, 3. x. x a 2. x. x synchronizace rozhraní API:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Změna kanálu

Následující fragment kódu ukazuje rozdíly v tom, jak se provádějí operace změny kanálu mezi asynchronními rozhraními API 4,0 a 3. x. x:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

* Tato funkce není podporovaná od verze Java SDK v2 Sync. 
---

### <a name="container-level-time-to-livettl"></a>Hodnota TTL (Time to Live) na úrovni kontejneru

Následující fragment kódu ukazuje rozdíly v tom, jak vytvořit dynamický čas pro data v kontejneru pomocí asynchronních rozhraní API 4,0, 3. x. x a 2. x. x synchronizace rozhraní API:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>Hodnota TTL (Time to Live) na úrovni položky

Následující fragment kódu ukazuje rozdíly v tom, jak vytvořit dynamický čas pro položku pomocí asynchronních rozhraní API 4,0, 3. x. x a 2. x. x synchronizace rozhraní API:

# <a name="java-sdk-40-async-api"></a>[Rozhraní Java SDK 4,0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Rozhraní Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Rozhraní API pro synchronizaci Java SDK 2. x. x](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Další kroky

* [Vytvoření aplikace Java](create-sql-api-java.md) pro správu Azure Cosmos DB dat rozhraní SQL API pomocí sady v4 SDK
* Další informace o sadách [Java SDK založených na reaktorech](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)
* Přečtěte si, jak převést asynchronní kód RxJava na reactoring Async Code pomocí třídy [actor vs RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) .
