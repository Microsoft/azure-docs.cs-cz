---
title: Volání uložené procedury, triggery a uživatelem definované funkce pomocí sady SDK služby Azure Cosmos DB
description: Další informace o registraci a volání uložené procedury, triggery a uživatelem definovaných funkcí, použití se sadami SDK služby Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/08/2018
ms.author: mjbrown
ms.openlocfilehash: d06a1efd1b706d242cbc5af9e93b3d08c84c575e
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410911"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Postup registrace a použití uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB

Rozhraní SQL API ve službě Azure Cosmos DB podporuje registrace a vyvolání uložené procedury, triggery a uživatelem definované funkce (UDF) napsané v jazyce JavaScript. Můžete použít rozhraní SQL API [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) nebo [Python](sql-api-sdk-python.md) sady SDK k registraci a volání uložené procedury. Pokud jste definovali jeden nebo více uložené procedury, triggery a uživatelem definované funkce, můžete načíst a zobrazit v [webu Azure portal](https://portal.azure.com/) pomocí Průzkumníku dat.

## <a id="stored-procedures"></a>Spuštění uložených procedur

Uložené procedury jsou zapsány pomocí jazyka JavaScript. Uživatelé moct vytvářet, aktualizovat, číst, dotazu a odstraňovat položky v rámci kontejneru Azure Cosmos. Další informace o tom, jak psát uložených procedurách najdete v tématu služby Azure Cosmos DB [jak psát uložené procedury ve službě Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) článku.

Následující příklady ukazují, jak zaregistrovat a volání uložené procedury s použitím se sadami SDK služby Azure Cosmos DB. Odkazovat na [vytvořit dokument](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) jako zdroj pro tuto uloženou proceduru se uloží jako `spCreateToDoItem.js`.

> [!NOTE]
> Pro dělené kontejnery, při spouštění uložené procedury je třeba zadat hodnotu klíče oddílu v možnosti žádosti. Uložené procedury jsou vždy s rozsahem klíče oddílu. Položky, které mají hodnotu klíče oddílu různých nebudou viditelné pro uloženou proceduru. To také u triggerů také.

### <a name="stored-procedures---net-sdk"></a>Uložené procedury – sada .NET SDK

Následující příklad ukazuje, jak zaregistrovat uložené procedury pomocí sady .NET SDK:

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

Následující kód ukazuje, jak pomocí sady .NET SDK pro volání uložené procedury:

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

### <a name="stored-procedures---java-sdk"></a>Uložené procedury - sady Java SDK

Následující příklad ukazuje, jak zaregistrovat uložené procedury pomocí sady Java SDK:

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

Následující kód ukazuje, jak volání uložené procedury pomocí sady Java SDK:

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

### <a name="stored-procedures---javascript-sdk"></a>Uložené procedury - JavaScript SDK

Následující příklad ukazuje, jak zaregistrovat uložené procedury pomocí sady JavaScript SDK

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Následující kód ukazuje, jak pomocí sady JavaScript SDK volání uložené procedury:

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

### <a name="stored-procedures---python-sdk"></a>Uložené procedury - Python SDK

Následující příklad ukazuje, jak zaregistrovat uložené procedury pomocí sady Python SDK

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

Následující kód ukazuje, jak pomocí sady Python SDK pro volání uložené procedury

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

## <a id="pre-triggers"></a>Jak spustit před aktivační události

Následující příklady ukazují, jak zaregistrovat a s využitím Azure Cosmos DB SDK volat před aktivační události. Odkazovat [příkladu aktivační procedury předběžné](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) jako zdroj této předběžné aktivační události je uložen jako `trgPreValidateToDoItemTimestamp.js`.

Při provádění, před aktivační události jsou předány v objektu RequestOptions zadáním `PreTriggerInclude` a potom předejte název aktivační události v seznamu objektů.

> [!NOTE]
> I v případě, že název triggeru je předán jako seznam, můžete spustit jenom jedna aktivační událost pro každou operaci.

### <a name="pre-triggers---net-sdk"></a>Předběžné aktivační události – sada .NET SDK

Následující kód ukazuje, jak zaregistrovat před aktivační událost pomocí sady .NET SDK:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Následující kód ukazuje, jak volat před aktivační událost pomocí sady .NET SDK:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Předběžné aktivační události – Java SDK

Následující kód ukazuje, jak zaregistrovat před aktivační událost pomocí sady Java SDK:

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

Následující kód ukazuje, jak volat před aktivační událost pomocí sady Java SDK:

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

### <a name="pre-triggers---javascript-sdk"></a>Předběžné aktivační události – JavaScript SDK

Následující kód ukazuje, jak zaregistrovat před aktivační událost pomocí sady JavaScript SDK:

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

Následující kód ukazuje, jak volat před aktivační událost pomocí sady JavaScript SDK:

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

### <a name="pre-triggers---python-sdk"></a>Předběžné aktivační události – Python SDK

Následující kód ukazuje, jak zaregistrovat před aktivační událost pomocí sady Python SDK:

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

Následující kód ukazuje, jak volat před aktivační událost pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'category': 'Personal', 'name': 'Groceries', 'description':'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, { 'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Jak spustit po aktivační události

Následující příklady ukazují, jak zaregistrovat po triggeru s použitím se sadami SDK služby Azure Cosmos DB. Odkazovat [po triggeru příklad](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) jako zdroj pro toto po triggeru je uložen jako `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Po aktivační události – sada .NET SDK

Následující kód ukazuje, jak zaregistrovat po aktivační událost pomocí sady .NET SDK:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js");,
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Následující kód ukazuje, jak volat po aktivační událost pomocí sady .NET SDK:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

var options = { postTriggerInclude: "trgPostUpdateMetadata" };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Po aktivační události – Java SDK

Následující kód ukazuje, jak zaregistrovat po aktivační událost pomocí sady Java SDK:

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

Následující kód ukazuje, jak volat po aktivační událost pomocí sady Java SDK:

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

### <a name="post-triggers---javascript-sdk"></a>Po aktivační události – JavaScript SDK

Následující kód ukazuje, jak zaregistrovat po aktivační událost pomocí sady JavaScript SDK:

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

Následující kód ukazuje, jak volat po aktivační událost pomocí sady JavaScript SDK:

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

### <a name="post-triggers---python-sdk"></a>Po aktivační události – Python SDK

Následující kód ukazuje, jak zaregistrovat po aktivační událost pomocí sady Python SDK:

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

Následující kód ukazuje, jak volat po aktivační událost pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'name': 'artist_profile_1023', 'artist': 'The Band', 'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, { 'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Jak pracovat s uživatelsky definovaných funkcí

Následující příklady ukazují, jak zaregistrovat uživatelem definované funkce pomocí sad SDK Azure Cosmos DB. Projít tento [uživatelem definovanou funkci příklad](how-to-write-stored-procedures-triggers-udfs.md#udfs) jako zdroj pro toto po triggeru je uložen jako `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Uživatelem definované funkce – sada .NET SDK

Následující kód ukazuje, jak zaregistrovat uživatelem definované funkce pomocí sady .NET SDK:

```csharp
string udfId = "udfTax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Následující kód ukazuje, jak volat uživatelsky definované funkce pomocí sady .NET SDK:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Uživatelem definované funkce – Java SDK

Následující kód ukazuje, jak zaregistrovat uživatelem definované funkce pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "udfTax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Následující kód ukazuje, jak volat uživatelsky definované funkce pomocí sady Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000", new FeedOptions());
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

Následující kód ukazuje, jak zaregistrovat uživatelem definované funkce pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "udfTax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Následující kód ukazuje, jak volat uživatelsky definované funkce pomocí sady JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Uživatelem definované funkce - Python SDK

Následující kód ukazuje, jak zaregistrovat uživatelem definované funkce pomocí sady Python SDK:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
        }
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Následující kód ukazuje, jak volat uživatelsky definované funkce pomocí sady Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(container_link, 'SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Další postup

Přečtěte si další koncepty a postupy: zápis nebo pomocí uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB:

- [Práce s Azure Cosmos DB uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Práce s jazykem JavaScript integrovaná rozhraní API pro dotazy ve službě Azure Cosmos DB](javascript-query-api.md)
- [Jak napsat uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Jak napsat uložených procedur a aktivačních událostí ve službě Azure Cosmos DB pomocí rozhraní API pro dotazy jazyka Javascript](how-to-write-javascript-query-api.md)
