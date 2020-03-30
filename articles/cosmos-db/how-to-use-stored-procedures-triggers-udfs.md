---
title: Registrace a použití uložených procedur, aktivačních událostí a uživatelem definovaných funkcí v sadách Azure Cosmos DB SDK
description: Zjistěte, jak zaregistrovat a volat uložené procedury, aktivační události a uživatelem definované funkce pomocí sad Azure Cosmos DB SDK
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: mjbrown
ms.openlocfilehash: 00740bc2255962089789682e3227ce414fd0ce64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582496"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Postup registrace a používání uložených procedur, triggerů a funkcí definovaných uživatelem ve službě Azure Cosmos DB

Rozhraní SQL API ve službě Azure Cosmos DB podporuje registraci a vyvolávání uložených procedur, triggerů a funkcí definovaných uživatelem napsaných v JavaScriptu. K registraci a vyvolání uložených procedur můžete použít sql api [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md)nebo [Python](sql-api-sdk-python.md) SDK. Jakmile definujete jednu nebo více uložených procedur, aktivačních událostí a uživatelem definovaných funkcí, můžete je načíst a zobrazit na [webu Azure Portal](https://portal.azure.com/) pomocí Průzkumníka dat.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>Jak spustit uložené procedury

Uložené procedury jsou napsány pomocí JavaScriptu. Můžou vytvářet, aktualizovat, číst, dotazovat a odstraňovat položky v kontejneru Azure Cosmos. Další informace o tom, jak psát uložené procedury v Azure Cosmos DB, najdete v článku [Jak psát uložené procedury v článku Azure Cosmos DB.](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)

Následující příklady ukazují, jak zaregistrovat a volat uloženou proceduru pomocí sad Azure Cosmos DB SDK. Viz [Vytvořit dokument](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) jako zdroj pro tuto uloženou proceduru je uložen jako `spCreateToDoItem.js`.

> [!NOTE]
> Pro rozdělené kontejnery při provádění uložené procedury musí být v možnostech požadavku uvedena hodnota klíče oddílu. Uložené procedury jsou vždy vymezeny na klíč oddílu. Položky, které mají jinou hodnotu klíče oddílu nebude viditelná pro uloženou proceduru. To platí i pro aktivační události stejně.

### <a name="stored-procedures---net-sdk-v2"></a>Uložené procedury - .NET SDK V2

Následující příklad ukazuje, jak zaregistrovat uloženou proceduru pomocí sady .NET SDK V2:

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

Následující kód ukazuje, jak volat uloženou proceduru pomocí sady .NET SDK V2:

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
```

### <a name="stored-procedures---net-sdk-v3"></a>Uložené procedury - .NET SDK V3

Následující příklad ukazuje, jak zaregistrovat uloženou proceduru pomocí sady .NET SDK V3:

```csharp
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("database", "container").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = "spCreateToDoItem",
    Body = File.ReadAllText(@"..\js\spCreateToDoItem.js")
});
```

Následující kód ukazuje, jak volat uloženou proceduru pomocí sady .NET SDK V3:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), newItems);
```

### <a name="stored-procedures---java-sdk"></a>Uložené procedury - Java SDK

Následující příklad ukazuje, jak zaregistrovat uloženou proceduru pomocí sady Java SDK:

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

Následující kód ukazuje, jak volat uloženou proceduru pomocí sady Java SDK:

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
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Uložené procedury - JavaScript SDK

Následující příklad ukazuje, jak zaregistrovat uloženou proceduru pomocí sady JavaScript SDK

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Následující kód ukazuje, jak volat uloženou proceduru pomocí sady JavaScript SDK:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Uložené procedury - Python SDK

Následující příklad ukazuje, jak zaregistrovat uloženou proceduru pomocí sady Python SDK

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

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>Jak spustit předběžné aktivační události

Následující příklady ukazují, jak zaregistrovat a volat předběžnou aktivační událost pomocí sad Azure Cosmos DB SDK. Odkazovat na [příkladu pre-trigger](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) jako zdroj pro `trgPreValidateToDoItemTimestamp.js`tento pre-trigger je uložen jako .

Při provádění jsou předběžné aktivační události předány v objektu RequestOptions zadáním `PreTriggerInclude` a předáním názvu aktivační události v objektu List.

> [!NOTE]
> Přestože je název aktivační události předán jako seznam, stále můžete spustit pouze jednu aktivační událost na operaci.

### <a name="pre-triggers---net-sdk-v2"></a>Předaktivační události - .NET SDK V2

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační událost pomocí sady .NET SDK V2:

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

Následující kód ukazuje, jak volat předběžnou aktivační událost pomocí sady .NET SDK V2:

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

### <a name="pre-triggers---net-sdk-v3"></a>Předaktivační události - .NET SDK V3

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační událost pomocí sady .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

Následující kód ukazuje, jak volat předběžnou aktivační událost pomocí sady .NET SDK V3:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Předběžné aktivační události - Java SDK

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

Následující kód ukazuje, jak volat předběžnou aktivační událost pomocí sady Java SDK:

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

### <a name="pre-triggers---javascript-sdk"></a>Předběžné aktivační události - JavaScript SDK

Následující kód ukazuje, jak zaregistrovat předaktivační událost pomocí sady JavaScript SDK:

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

Následující kód ukazuje, jak volat předběžnou aktivační událost pomocí sady JavaScript SDK:

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

### <a name="pre-triggers---python-sdk"></a>Předběžné aktivační události - Python SDK

Následující kód ukazuje, jak zaregistrovat předběžnou aktivační událost pomocí sady Python SDK:

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

Následující kód ukazuje, jak volat předběžnou aktivační událost pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>Jak spustit následné aktivační události

Následující příklady ukazují, jak zaregistrovat post-trigger pomocí sad Azure Cosmos DB SDK. Odkazovat na [post-trigger příklad](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) jako zdroj pro tento `trgPostUpdateMetadata.js`post-trigger je uložen jako .

### <a name="post-triggers---net-sdk-v2"></a>Po aktivačních událostech - .NET SDK V2

Následující kód ukazuje, jak zaregistrovat post-trigger pomocí .NET SDK V2:

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

Následující kód ukazuje, jak volat post-trigger pomocí .NET SDK V2:

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

### <a name="post-triggers---net-sdk-v3"></a>Po aktivačních událostech - .NET SDK V3

Následující kód ukazuje, jak zaregistrovat post-trigger pomocí .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

Následující kód ukazuje, jak volat post-trigger pomocí .NET SDK V3:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Následné spouštěče - Java SDK

Následující kód ukazuje, jak zaregistrovat post-trigger pomocí sady Java SDK:

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

Následující kód ukazuje, jak volat post-trigger pomocí Java SDK:

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

### <a name="post-triggers---javascript-sdk"></a>Post-triggery - JavaScript SDK

Následující kód ukazuje, jak zaregistrovat post-trigger pomocí sady JavaScript SDK:

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

Následující kód ukazuje, jak volat post-trigger pomocí sady JavaScript SDK:

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

### <a name="post-triggers---python-sdk"></a>Následné aktivační události - Python SDK

Následující kód ukazuje, jak zaregistrovat post-trigger pomocí Python SDK:

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

Následující kód ukazuje, jak volat post-trigger pomocí Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>Jak pracovat s uživatelem definovanými funkcemi

Následující příklady ukazují, jak zaregistrovat uživatelem definovanou funkci pomocí sad Azure Cosmos DB SDK. Viz tento [příklad uživatelem definované funkce](how-to-write-stored-procedures-triggers-udfs.md#udfs) jako zdroj pro `udfTax.js`tento post-trigger je uložen jako .

### <a name="user-defined-functions---net-sdk-v2"></a>Uživatelem definované funkce - .NET SDK V2

Následující kód ukazuje, jak zaregistrovat uživatelem definovanou funkci pomocí sady .NET SDK V2:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Následující kód ukazuje, jak volat uživatelem definovanou funkci pomocí sady .NET SDK V2:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Uživatelem definované funkce – sada .NET SDK V3

Následující kód ukazuje, jak zaregistrovat uživatelem definovanou funkci pomocí sady .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

Následující kód ukazuje, jak volat uživatelem definovanou funkci pomocí sady .NET SDK V3:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Uživatelem definované funkce - Java SDK

Následující kód ukazuje, jak zaregistrovat uživatelem definovanou funkci pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Následující kód ukazuje, jak volat uživatelem definovanou funkci pomocí sady Java SDK:

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

### <a name="user-defined-functions---javascript-sdk"></a>Uživatelem definované funkce - JavaScript SDK

Následující kód ukazuje, jak zaregistrovat uživatelem definovanou funkci pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Následující kód ukazuje, jak volat uživatelem definovanou funkci pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Uživatelem definované funkce - Python SDK

Následující kód ukazuje, jak zaregistrovat uživatelem definovanou funkci pomocí sady Python SDK:

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

Následující kód ukazuje, jak volat uživatelem definovanou funkci pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Další kroky

Další koncepty a postupy pro zápis nebo použití uložených procedur, aktivačních událostí a uživatelem definovaných funkcí v Azure Cosmos DB:

- [Práce s uloženými procedurami, aktivačními událostmi a uživateli definovanými funkcemi Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Práce s rozhraním API pro integrované dotazy jazyka JavaScript v Azure Cosmos DB](javascript-query-api.md)
- [Jak psát uložené procedury, aktivační události a uživatelem definované funkce v Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Jak psát uložené procedury a aktivační události pomocí rozhraní Javascript Query API v Azure Cosmos DB](how-to-write-javascript-query-api.md)
