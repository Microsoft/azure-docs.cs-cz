---
title: Volání uložených procedur, triggerů a uživatelsky definovaných funkcí pomocí Azure Cosmos DB SDK
description: Naučte se registrovat a volat uložené procedury, triggery a uživatelsky definované funkce pomocí sad Azure Cosmos DB SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: 7732039ff2494ef16fda5afe384a824ec786a8cf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092930"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Postup při registraci a používání uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB

Rozhraní SQL API v Azure Cosmos DB podporuje registraci a vyvolání uložených procedur, triggerů a uživatelsky definovaných funkcí (UDF) zapsaných v JavaScriptu. K registraci a vyvolání uložených procedur můžete použít sady SDK rozhraní API [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node. js](sql-api-sdk-node.md)nebo [Python](sql-api-sdk-python.md) . Po definování jednoho nebo více uložených procedur, triggerů a uživatelsky definovaných funkcí je můžete načíst a zobrazit v [Azure Portal](https://portal.azure.com/) pomocí Průzkumník dat.

## <a id="stored-procedures"></a>Spuštění uložených procedur

Uložené procedury jsou zapisovány pomocí JavaScriptu. Můžou vytvářet, aktualizovat, číst, dotazovat a odstraňovat položky v rámci kontejneru Azure Cosmos. Další informace o tom, jak zapisovat uložené procedury v Azure Cosmos DB, najdete v tématu [Postup zápisu uložených procedur v Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) článku.

Následující příklady ukazují, jak registrovat a volat uloženou proceduru pomocí sad Azure Cosmos DB SDK. Odkaz na [Vytvoření dokumentu můžete vytvořit](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) jako zdroj pro tuto uloženou proceduru, `spCreateToDoItem.js`která se uloží jako.

> [!NOTE]
> U dělených kontejnerů při provádění uložené procedury musí být v možnostech žádosti uvedena hodnota klíče oddílu. Uložené procedury jsou vždy vymezeny na klíč oddílu. Položky, které mají jinou hodnotu klíče oddílu, nebudou viditelné pro uloženou proceduru. To se také aplikuje i na triggery.

### <a name="stored-procedures---net-sdk"></a>Uložené procedury – sada .NET SDK

Následující příklad ukazuje, jak registrovat uloženou proceduru pomocí sady .NET SDK:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Následující kód ukazuje, jak volat uloženou proceduru pomocí sady .NET SDK:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Uložené procedury – Java SDK

Následující příklad ukazuje, jak registrovat uloženou proceduru pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Následující kód ukazuje, jak zavolat uloženou proceduru pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Uložené procedury – JavaScript SDK

Následující příklad ukazuje, jak registrovat uloženou proceduru pomocí sady JavaScript SDK

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Následující kód ukazuje, jak zavolat uloženou proceduru pomocí sady JavaScript SDK:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Uložené procedury – Python SDK

Následující příklad ukazuje, jak registrovat uloženou proceduru pomocí sady Python SDK

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

Následující kód ukazuje, jak volat uloženou proceduru pomocí sady Python SDK

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Jak spustit předběžné triggery

Následující příklady ukazují, jak registrovat a volat předběžnou Trigger pomocí sad Azure Cosmos DB SDK. Podívejte se na [příklad](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) předběžného triggeru, protože zdroj pro tuto předběžnou aktivační událost `trgPreValidateToDoItemTimestamp.js`je uložen jako.

Při spuštění jsou předběžné triggery předány do objektu RequestOptions zadáním `PreTriggerInclude` a následným předáním názvu triggeru do objektu list.

> [!NOTE]
> I když se název triggeru předává jako seznam, můžete i tak spustit jenom jednu Trigger na operaci.

### <a name="pre-triggers---net-sdk"></a>Předběžné triggery – sada .NET SDK

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační událost pomocí sady .NET SDK:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Následující kód ukazuje, jak zavolat předběžnou aktivační proceduru pomocí sady .NET SDK:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Před triggery – Java SDK

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační událost pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Následující kód ukazuje, jak zavolat předběžnou triggerovou sadu pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Předběžné triggery – JavaScript SDK

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační událost pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Následující kód ukazuje, jak zavolat předběžnou aktivační proceduru pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Předběžné triggery – Python SDK

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační proceduru pomocí sady Python SDK:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Následující kód ukazuje, jak zavolat předběžnou aktivační proceduru pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Spuštění po triggerech

Následující příklady ukazují, jak registrovat aktivační událost pomocí sad Azure Cosmos DB SDK. Podívejte se na [příklad po triggeru](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) , který označuje, že zdroj pro tuto aktivační událost je `trgPostUpdateMetadata.js`uložen jako.

### <a name="post-triggers---net-sdk"></a>Post-Triggers – sada .NET SDK

Následující kód ukazuje, jak pomocí sady .NET SDK zaregistrovat aktivační událost po triggeru:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Následující kód ukazuje, jak zavolat post-Trigger pomocí sady .NET SDK:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Post-Triggers – Java SDK

Následující kód ukazuje, jak registrovat po triggeru pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Následující kód ukazuje, jak zavolat po triggeru pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Post-Triggers – JavaScript SDK

Následující kód ukazuje, jak zaregistrovat aktivační událost pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Následující kód ukazuje, jak zavolat post-Trigger pomocí sady JavaScript SDK:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Post-Triggers – Python SDK

Následující kód ukazuje, jak pomocí sady Python SDK zaregistrovat aktivační událost po triggeru:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Následující kód ukazuje, jak volat po triggeru pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Jak pracovat s uživatelsky definovanými funkcemi

Následující příklady ukazují, jak registrovat uživatelsky definovanou funkci pomocí sad Azure Cosmos DB SDK. Podívejte se na tento [příklad uživatelsky definované funkce](how-to-write-stored-procedures-triggers-udfs.md#udfs) , protože zdroj pro tuto aktivační událost je uložen jako `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Uživatelsky definované funkce – sada .NET SDK

Následující kód ukazuje, jak zaregistrovat uživatelsky definovanou funkci pomocí sady .NET SDK:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Následující kód ukazuje, jak volat uživatelsky definovanou funkci pomocí sady .NET SDK:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Uživatelsky definované funkce – Java SDK

Následující kód ukazuje, jak zaregistrovat uživatelsky definovanou funkci pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Následující kód ukazuje, jak volat uživatelsky definovanou funkci pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Uživatelsky definované funkce – sada JavaScript SDK

Následující kód ukazuje, jak zaregistrovat uživatelsky definovanou funkci pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Následující kód ukazuje, jak volat uživatelsky definovanou funkci pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Uživatelsky definované funkce – Python SDK

Následující kód ukazuje, jak zaregistrovat uživatelsky definovanou funkci pomocí sady Python SDK:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Následující kód ukazuje, jak volat uživatelsky definovanou funkci pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Další postup

Přečtěte si další koncepty a postupy psaní a používání uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB:

- [Práce s Azure Cosmos DB uloženými procedurami, triggery a uživatelsky definovanými funkcemi v Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Práce s integrovaným rozhraním API pro integrované dotazy jazyka JavaScript v Azure Cosmos DB](javascript-query-api.md)
- [Postup zápisu uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Jak zapisovat uložené procedury a triggery pomocí rozhraní API dotazů jazyka JavaScript v Azure Cosmos DB](how-to-write-javascript-query-api.md)
