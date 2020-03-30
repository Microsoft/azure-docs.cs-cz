---
title: Výstupní vazba Azure Cosmos DB pro funkce 2.x
description: Naučte se používat výstupní vazbu Azure Cosmos DB ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277762"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Výstupní vazba Azure Cosmos DB pro Funkce Azure 2.x

Výstupní vazba Azure Cosmos DB umožňuje zapsat nový dokument do databáze Azure Cosmos DB pomocí rozhraní SQL API.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, zápis jednoho dokumentu](#queue-trigger-write-one-doc-c)
* [Aktivační událost fronty, zápis dokumentů pomocí nástroje IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Příklady odkazují na `ToDoItem` jednoduchý typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, zápis jednoho dokumentu

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která přidá dokument do databáze, pomocí dat poskytnutých ve zprávě z úložiště fronty.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí nástroje IAsyncCollector

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která přidá kolekci dokumentů do databáze, pomocí dat uvedených ve zprávě fronty JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, zápis jednoho dokumentu](#queue-trigger-write-one-doc-c-script)
* [Aktivační událost fronty, zápis dokumentů pomocí nástroje IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, zápis jednoho dokumentu

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří dokumenty Azure Cosmos DB v následujícím formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde jsou data vazby v souboru *function.json:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí nástroje IAsyncCollector

Chcete-li vytvořit více dokumentů, `IAsyncCollector<T>` `T` můžete vytvořit vazbu na `ICollector<T>` nebo kde je jeden z podporovaných typů.

Tento příklad odkazuje na `ToDoItem` jednoduchý typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Zde je soubor function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Zde je kód skriptu C#:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří dokumenty Azure Cosmos DB v následujícím formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde jsou data vazby v souboru *function.json:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak zapsat dokument do databáze Azure CosmosDB jako výstup funkce.

Definice vazby je definována v *souboru function.json,* kde je *typ* nastaven na `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Chcete-li zapisovat do databáze, předejte objekt dokumentu metodě `set` parametru databáze.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Aktivační událost fronty, uložení zprávy do databáze pomocí vrácené hodnoty](#queue-trigger-save-message-to-database-via-return-value-java)
* [Aktivační událost HTTP, uložení jednoho dokumentu do databáze pomocí vrácené hodnoty](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Aktivační událost HTTP, uložení jednoho dokumentu do databáze pomocí funkce OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Aktivační událost HTTP, uložení více dokumentů do databáze pomocí outputbinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Aktivační událost fronty, uložení zprávy do databáze pomocí vrácené hodnoty

Následující příklad ukazuje funkci Java, která přidá dokument do databáze s daty ze zprávy v úložišti fronty.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Aktivační událost HTTP, uložení jednoho dokumentu do databáze pomocí vrácené hodnoty

Následující příklad ukazuje funkci Java, jejíž podpis ```@CosmosDBOutput``` je anotován a má vrácenou hodnotu typu ```String```. Dokument JSON vrácený funkcí bude automaticky zapsán do odpovídající kolekce CosmosDB.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Aktivační událost HTTP, uložení jednoho dokumentu do databáze pomocí funkce OutputBinding

Následující příklad ukazuje funkci Java, která zapisuje dokument do CosmosDB prostřednictvím výstupního parametru. ```OutputBinding<T>``` V tomto příkladu ```outputItem``` musí být parametr anotován s ```@CosmosDBOutput```, nikoli podpisem funkce. Použití ```OutputBinding<T>``` umožňuje vaše funkce využít vazby k zápisu dokumentu cosmosdb a zároveň umožňuje vrácení jinou hodnotu volajícího funkce, jako je například Dokument JSON nebo XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Aktivační událost HTTP, uložení více dokumentů do databáze pomocí outputbinding

Následující příklad ukazuje funkci Java, která zapisuje více dokumentů do CosmosDB prostřednictvím výstupního parametru. ```OutputBinding<T>``` V tomto příkladu ```outputItem``` je parametr anotován s ```@CosmosDBOutput```, nikoli podpisem funkce. Výstupní parametr ```outputItem``` má jako ```ToDoItem``` typ parametru šablony seznam objektů. Použití ```OutputBinding<T>``` umožňuje vaše funkce využít vazby k zápisu dokumentů do CosmosDB a zároveň umožňuje vrácení jinou hodnotu volajícího funkce, jako je například Dokument JSON nebo XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

V [knihovně runtime funkcí Javy](/java/api/overview/azure/functions/runtime)použijte poznámku `@CosmosDBOutput` o parametrech, které budou zapsány do Cosmos DB.  Typ parametru poznámky ```OutputBinding<T>```by měl být , kde T je buď nativní typ Java nebo POJO.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, naleznete v [tématu Output - configuration](#configuration). Zde je `CosmosDB` příklad atributu v podpisu metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace `CosmosDBOutput` je k dispozici pro zápis dat do Cosmos DB. Poznámku můžete použít na funkci nebo na parametr jednotlivé funkce. Při použití na metodu funkce vrácená hodnota funkce je co je zapsána do Cosmos DB. Pokud použijete poznámku s parametrem, musí být typ `OutputBinding<T>` parametru `T` deklarován jako typ where typu Java nebo POJO.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `CosmosDB` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ**     | neuvedeno | Musí být `cosmosDB`nastavena na .        |
|**direction**     | neuvedeno | Musí být `out`nastavena na .         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databasename** | **DatabaseName**|Databáze obsahující kolekci, kde je dokument vytvořen.     |
|**název kolekce** |**CollectionName**  | Název kolekce, kde je dokument vytvořen. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logická hodnota označující, zda je kolekce vytvořena, když neexistuje. Výchozí hodnota je *false,* protože nové kolekce jsou vytvořeny s vyhrazenou propustnost, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Když `CreateIfNotExists` je true, definuje cestu klíče oddílu pro vytvořenou kolekci.|
|**collectionThroughput**|**CollectionThroughput**| Když `CreateIfNotExists` je true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Název nastavení aplikace obsahující připojovací řetězec Azure Cosmos DB.        |
|**preferredLocations**| **Preferované umístění**| (Nepovinné) Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být odděleny čárkami. Například "Východní USA, jižní střed USA, severní Evropa". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| (Nepovinné) Když je `true` nastavena na spolu s `PreferredLocations`, může využít [zápisy ve více oblastech](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) ve službě Azure Cosmos DB. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

Ve výchozím nastavení je při zápisu do výstupního parametru ve funkci vytvořen dokument v databázi. Tento dokument má jako ID dokumentu automaticky vygenerovaný identifikátor GUID. ID dokumentu výstupního dokumentu můžete určit zadáním `id` vlastnosti v objektu JSON předané výstupnímu parametru.

> [!Note]
> Když zadáte ID existujícího dokumentu, přepíše se nový výstupní dokument.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Odkaz |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení host.json

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzi 2.x. Další informace o nastavení globální konfigurace ve verzi 2.x najdete v [tématu host.json reference for Azure Functions version 2.x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|Režim brány|brána|Režim připojení používaný funkcí při připojování ke službě Azure Cosmos DB. Možnosti `Direct` jsou a`Gateway`|
|Protocol (Protokol)|Protokol Https|Protokol připojení používaný funkcí při připojení ke službě Azure Cosmos DB.  Přečtěte si [zde vysvětlení obou režimů](../cosmos-db/performance-tips.md#networking)|
|zapůjčení předběžné hospo|neuvedeno|Pronajměte předponu, která se použije ve všech funkcích aplikace.|

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při vytvoření nebo úpravě dokumentu Azure Cosmos DB (aktivační událost)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)