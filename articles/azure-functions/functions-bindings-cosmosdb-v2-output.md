---
title: Azure Cosmos DB výstupní vazba pro Functions 2. x
description: Naučte se používat výstupní vazbu Azure Cosmos DB v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277762"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Azure Cosmos DB výstupní vazba pro Azure Functions 2. x

Azure Cosmos DB výstupní vazbu umožňuje zapsat nový dokument k databázi Azure Cosmos DB pomocí rozhraní SQL API.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, zápis jednoho dokumentu](#queue-trigger-write-one-doc-c)
* [Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Příklady odkazují na jednoduchý `ToDoItem` typ:

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

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, jeden zápis dokumentu

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která přidá dokument do databáze pomocí dat poskytovaných ve zprávě z úložiště Queue.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentace pomocí IAsyncCollector

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která do databáze přidává kolekci dokumentů pomocí dat zadaných ve formátu JSON zprávy fronty.

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, zápis jednoho dokumentu](#queue-trigger-write-one-doc-c-script)
* [Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, jeden zápis dokumentu

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce používá fronty vstupní vazby pro fronty, která přijímá JSON v následujícím formátu:

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

Tady jsou data vazby v souboru *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód skriptu jazyka C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentace pomocí IAsyncCollector

Chcete-li vytvořit více dokumentů, můžete vytvořit vazby na `ICollector<T>` nebo `IAsyncCollector<T>`, kde `T` je jeden z podporovaných typů.

Tento příklad odkazuje na jednoduchý typ `ToDoItem`:

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

Tady je soubor function.json:

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

Tady je kód skriptu jazyka C#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce používá fronty vstupní vazby pro fronty, která přijímá JSON v následujícím formátu:

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

Tady jsou data vazby v souboru *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód jazyka JavaScript:

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

Definice vazby je definována v *Function. JSON* , kde *typ* je nastaven na `cosmosDB`.

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

* [Aktivační událost fronty, uložení zprávy do databáze prostřednictvím návratové hodnoty](#queue-trigger-save-message-to-database-via-return-value-java)
* [Aktivační událost HTTP – uložte jeden dokument do databáze prostřednictvím návratové hodnoty.](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Aktivační procedura HTTP – uložení jednoho dokumentu do databáze prostřednictvím OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Aktivační událost HTTP, uložení více dokumentů do databáze prostřednictvím OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Aktivační událost fronty, uložení zprávy do databáze prostřednictvím návratové hodnoty

Následující příklad ukazuje funkci jazyka Java, která přidá dokument do databáze s daty z zprávy ve front-Queue Storage.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Aktivační událost HTTP – uložte jeden dokument do databáze prostřednictvím návratové hodnoty.

Následující příklad ukazuje funkci jazyka Java, jejíž signatura je opatřena poznámkou ```@CosmosDBOutput``` a má návratovou hodnotu typu ```String```. Dokument JSON vrácený funkcí se automaticky zapíše do odpovídající kolekce CosmosDB.

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

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Aktivační procedura HTTP – uložení jednoho dokumentu do databáze prostřednictvím OutputBinding

Následující příklad ukazuje funkci jazyka Java, která zapisuje dokument do CosmosDB prostřednictvím výstupní parametr ```OutputBinding<T>```. V tomto příkladu musí být parametr ```outputItem``` poznámkou s ```@CosmosDBOutput```, nikoli signaturou funkce. Použití ```OutputBinding<T>``` umožňuje funkci využít vazby k zápisu dokumentu do CosmosDB a současně umožňuje vracet jinou hodnotu volajícímu funkci, jako je JSON nebo dokument XML.

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

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Aktivační událost HTTP, uložení více dokumentů do databáze prostřednictvím OutputBinding

Následující příklad ukazuje funkci jazyka Java, která zapisuje více dokumentů do CosmosDB prostřednictvím výstupní parametr ```OutputBinding<T>```. V tomto příkladu je parametr ```outputItem``` označen ```@CosmosDBOutput```, nikoli signaturou funkce. Výstupní parametr ```outputItem``` obsahuje seznam ```ToDoItem``` objektů jako typ parametru šablony. Použití ```OutputBinding<T>``` umožňuje funkci využít vazby k zápisu dokumentů do CosmosDB a zároveň umožňuje vracet jiné hodnoty volajícímu funkci, jako je JSON nebo dokument XML.

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

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBOutput` anotaci u parametrů, které budou zapsány do Cosmos DB.  Typ parametru anotace by měl být ```OutputBinding<T>```, kde T je buď nativní typ Java, nebo POJO.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které lze konfigurovat, naleznete v tématu [Output-Configuration](#configuration). Tady je příklad atributu `CosmosDB` v signatuře metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Poznámka `CosmosDBOutput` je k dispozici pro zápis dat do Cosmos DB. Můžete použít poznámku na funkci nebo na konkrétní parametr funkce. Při použití v metodě Function je vrácená hodnota funkce ta, která je zapsána do Cosmos DB. Použijete-li anotaci s parametrem, typ parametru musí být deklarován jako `OutputBinding<T>`, kde `T` nativní typ Java nebo POJO.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `CosmosDB`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     | neuvedeno | Musí být nastavené na `cosmosDB`.        |
|**direction**     | neuvedeno | Musí být nastavené na `out`.         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databáze** | **Databáze**|Databáze obsahující kolekci, do které se vytvoří dokument.     |
|**collectionName** |**CollectionName**  | Název kolekce, ve kterém se vytvoří dokument. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logická hodnota označující, zda kolekce je vytvořena při neexistuje. Výchozí hodnota je *false* , protože nové kolekce jsou vytvořeny s rezervovanou propustností, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Pokud je `CreateIfNotExists` true, definuje cestu ke klíči oddílu pro vytvořenou kolekci.|
|**collectionThroughput**|**CollectionThroughput**| Pokud je `CreateIfNotExists` true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Název nastavení aplikace, které obsahuje připojovací řetězec služby Azure Cosmos DB.        |
|**preferredLocations**| **PreferredLocations**| Volitelné Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být oddělené čárkami. Například "Východní USA, Střed USA – jih, Severní Evropa". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| Volitelné Když nastavíte `true` společně s `PreferredLocations`, může ve službě Azure Cosmos DB využívat [zápis ve více oblastech](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) . |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

Ve výchozím nastavení při zápisu do výstupního parametru ve funkci, je dokument vytvořené ve vaší databázi. Tento dokument obsahuje automaticky generovaný identifikátor GUID jako identifikátor dokumentu Můžete zadat ID dokumentu výstupního dokumentu zadáním vlastnosti `id` do objektu JSON předaného do výstupního parametru.

> [!Note]
> Pokud chcete zadat ID existující dokument, získá přepsána nový výstupní dokument.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Odkaz |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální konfiguraci nastavení k dispozici pro tuto vazbu ve verzi 2.x. Další informace o globálních nastaveních konfigurace verze 2. x naleznete v tématu [reference Host. JSON pro Azure Functions verze 2. x](functions-host-json.md).

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
|GatewayMode|Brána|Režim připojení, který funkce používá při připojování ke službě Azure Cosmos DB. Možnosti jsou `Direct` a `Gateway`|
|Protokol|Https|Protokol připojení, který funkce používá při připojení ke službě Azure Cosmos DB.  Přečtěte si [zde pro vysvětlení obou režimů](../cosmos-db/performance-tips.md#networking) .|
|leasePrefix|neuvedeno|Předpona zapůjčení pro použití ve všech funkcích aplikace|

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření nebo úpravě dokumentu Azure Cosmos DB (aktivační událost)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)