---
title: Azure Cosmos DB vstupní vazby pro Functions 2. x a vyšší
description: Naučte se používat vstupní vazbu Azure Cosmos DB v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 21ca30b24c4824a2d303d02f3df712328885e199
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435201"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB vstupní vazby pro Azure Functions 2. x a vyšší

Vstupní vazba služby Azure Cosmos DB načítá pomocí rozhraní SQL API jeden nebo více dokumentů Azure Cosmos DB a předává je do vstupního parametru funkce. Parametry ID dokumentu nebo dotaz se dají určit podle aktivační události, která funkci volá.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Pokud je kolekce [rozdělená na oddíly](../cosmos-db/partitioning-overview.md#logical-partitions), operace vyhledávání musí také určovat hodnotu klíče oddílu.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-c)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-c)
* [Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Trigger HTTP, získání více dokumentů pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Trigger HTTP, získání více dokumentů pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Příklady odkazují na jednoduchý `ToDoItem` Typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON 

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována zprávou fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON na objekt typu `ToDoItemLookup` , který obsahuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

>[!NOTE]
>Parametr řetězce dotazu HTTP rozlišuje velká a malá písmena.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Příklad ukazuje, jak použít výraz vazby v `SqlQuery` parametru. Můžete předat údaje o trasách k `SqlQuery` parametru, jak je znázorněno, ale v současné době [nelze předat hodnoty řetězce dotazu](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Pokud potřebujete dotazovat pouze podle ID, doporučuje se použít hledání, podobně jako v [předchozích příkladech](#http-trigger-look-up-id-from-query-string-c), protože bude spotřebovávat méně [jednotek žádosti](../cosmos-db/request-units.md). Operace čtení bodu čtení (GET) [jsou efektivnější](../cosmos-db/optimize-cost-reads-writes.md) než dotazy podle ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Trigger HTTP, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Dotaz je zadán ve `SqlQuery` vlastnosti atributu.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Trigger HTTP, získání více dokumentů pomocí DocumentClient

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Kód používá `DocumentClient` instanci poskytnutou Azure Cosmos DB vazbou ke čtení seznamu dokumentů. `DocumentClient`Instanci lze také použít pro operace zápisu.

> [!NOTE]
> K usnadnění testování můžete také použít rozhraní [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient) .

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledání ID z řetězce](#queue-trigger-look-up-id-from-string-c-script)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c-script)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Trigger HTTP, získání více dokumentů pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Trigger HTTP, získání více dokumentů pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Příklady triggeru HTTP odkazují na jednoduchý `ToDoItem` Typ:

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

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Aktivační událost fronty, vyhledání ID z řetězce

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód skriptu jazyka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí triggeru fronty pro přizpůsobení parametrů dotazu.

Aktivační událost fronty poskytuje parametr `departmentId` . Zpráva fronty `{ "departmentId" : "Finance" }` by vrátila všechny záznamy finančního oddělení.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód skriptu jazyka C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje [funkci skriptu jazyka C#](functions-reference-csharp.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje [funkci skriptu jazyka C#](functions-reference-csharp.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Trigger HTTP, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje [funkci skriptu jazyka C#](functions-reference-csharp.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Dotaz je zadán ve `SqlQuery` vlastnosti atributu.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Trigger HTTP, získání více dokumentů pomocí DocumentClient

Následující příklad ukazuje [funkci skriptu jazyka C#](functions-reference-csharp.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Kód používá `DocumentClient` instanci poskytnutou Azure Cosmos DB vazbou ke čtení seznamu dokumentů. `DocumentClient`Instanci lze také použít pro operace zápisu.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="java"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Trigger HTTP, vyhledání ID z řetězce dotazu – parametr řetězce](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Trigger HTTP, vyhledání ID z řetězce dotazu – parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-java)
* [Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Aktivační událost HTTP, získání více dokumentů z dat směrování pomocí SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Příklady odkazují na jednoduchý `ToDoItem` Typ:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Trigger HTTP, vyhledání ID z řetězce dotazu – parametr řetězce

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení dokumentu ze zadané databáze a kolekce ve formě řetězce.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBInput` anotaci u parametrů funkce, jejichž hodnota pochází z Cosmos DB.  Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Trigger HTTP, vyhledání ID z řetězce dotazu – parametr POJO

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu použité k načtení dokumentu ze zadané databáze a kolekce. Dokument se pak převede na instanci Pojo, která se ```ToDoItem``` dřív vytvořila, a předal se jako argument funkce.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá parametr směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení dokumentu ze zadané databáze a kolekce a jejich vrácení jako ```Optional<String>``` .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá parametr směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení dokumentu ze zadané databáze a kolekce a převod výsledné sady na ```ToDoItem[]``` , protože v závislosti na kritériích dotazu může být vráceno mnoho dokumentů.

> [!NOTE]
> Pokud potřebujete dotazovat pouze podle ID, doporučuje se použít hledání, podobně jako v [předchozích příkladech](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), protože bude spotřebovávat méně [jednotek žádosti](../cosmos-db/request-units.md). Operace čtení bodu čtení (GET) [jsou efektivnější](../cosmos-db/optimize-cost-reads-writes.md) než dotazy podle ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, získání více dokumentů z dat směrování pomocí SqlQuery

Následující příklad ukazuje funkci jazyka Java, která načte více dokumentů. Funkce se aktivuje požadavkem HTTP, který používá parametr směrování ```desc``` k určení řetězce, který se má v ```description``` poli vyhledat. Hledaný termín slouží k načtení kolekce dokumentů ze zadané databáze a kolekce, převodu výsledku sady výsledků na objekt ```ToDoItem[]``` a jeho předání jako argumentu funkce.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-javascript)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje [funkci JavaScriptu](functions-reference-node.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje [funkci JavaScriptu](functions-reference-node.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí triggeru fronty pro přizpůsobení parametrů dotazu.

Aktivační událost fronty poskytuje parametr `departmentId` . Zpráva fronty `{ "departmentId" : "Finance" }` by vrátila všechny záznamy finančního oddělení.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, input) {
  var documents = context.bindings.documents;
  for (var i = 0; i < documents.length; i++) {
    var document = documents[i];
    // operate on each document
  }
  context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-ps)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-id-query-string-ps)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-id-route-data-ps)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-multiple-docs-sqlquery-ps)

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON

Následující příklad ukazuje, jak číst a aktualizovat jeden Cosmos DB dokument. Jedinečný identifikátor dokumentu se poskytuje prostřednictvím hodnoty JSON ve zprávě fronty.

Vstupní vazba Cosmos DB je uvedena jako první v seznamu vazeb nalezených v konfiguračním souboru funkce (_function.json_).

<a name="queue-trigger-look-up-id-from-json-ps"></a>

```json
{
  "name": "InputDocumentIn",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id": "{queueTrigger_payload_property}",
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "in"
},
{
  "name": "InputDocumentOut",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": false,
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "out"
}
```

_run.ps1_ soubor obsahuje kód prostředí PowerShell, který čte příchozí dokument a výstupy změn.

```powershell
param($QueueItem, $InputDocumentIn, $TriggerMetadata) 

$Document = $InputDocumentIn 
$Document.text = 'This was updated!' 

Push-OutputBinding -Name InputDocumentOut -Value $Document  
```

<a name="http-trigger-id-query-string-ps"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje, jak číst a aktualizovat jeden Cosmos DB dokument z webového rozhraní API. Jedinečný identifikátor dokumentu se poskytuje prostřednictvím parametru QueryString z požadavku HTTP, jak je definováno ve `"Id": "{Query.Id}"` vlastnosti vazby.

Vstupní vazba Cosmos DB je uvedena jako první v seznamu vazeb nalezených v konfiguračním souboru funkce (_function.json_).

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{Query.id}", 
      "PartitionKey": "{Query.partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ] 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    },
  ], 
  "disabled": false 
} 
```
  
Soubor _run.ps1_ obsahuje kód prostředí PowerShell, který čte příchozí dokument a výstupy změn.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 

    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
 
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
}
```

<a name="http-trigger-id-route-data-ps"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje, jak číst a aktualizovat jeden Cosmos DB dokument z webového rozhraní API. Jedinečný identifikátor dokumentu se poskytuje prostřednictvím parametru směrování. Parametr Route je definovaný ve vlastnosti vazby požadavku HTTP `route` a odkazuje na `"Id": "{Id}"` vlastnost Binding Cosmos DB.

Vstupní vazba Cosmos DB je uvedena jako první v seznamu vazeb nalezených v konfiguračním souboru funkce (_function.json_).

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{id}", 
      "PartitionKey": "{partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ], 
      "route": "todoitems/{partitionKeyValue}/{id}" 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    }
  ], 
  "disabled": false 
} 
```

Soubor _run.ps1_ obsahuje kód prostředí PowerShell, který čte příchozí dokument a výstupy změn.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 
    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
  
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
} 
```

<a name="queue-trigger-multiple-docs-sqlquery-ps"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje, jak číst více Cosmos DB dokumentů. Konfigurační soubor funkce (_function.json_) definuje vlastnosti vazby, které zahrnují `sqlQuery` . Příkaz jazyka SQL poskytnutý `sqlQuery` vlastnosti vybere sadu dokumentů poskytnutých funkci.

```json
{ 
  "name": "Documents", 
  "type": "cosmosDB", 
  "direction": "in", 
  "databaseName": "MyDb", 
  "collectionName": "MyCollection", 
  "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}", 
  "connectionStringSetting": "CosmosDBConnection" 
} 
```

Soubor _Run1.PS_ obsahuje kód prostředí PowerShell, který čte příchozí dokumenty.

```powershell
param($QueueItem, $Documents, $TriggerMetadata) 

foreach ($Document in $Documents) { 
    # operate on each document 
} 
```

# <a name="python"></a>[Python](#tab/python)

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-python)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-python)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-python)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód Pythonu:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje funkci jazyka [Python](functions-reference-python.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje funkci jazyka [Python](functions-reference-python.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí triggeru fronty pro přizpůsobení parametrů dotazu.

Aktivační událost fronty poskytuje parametr `departmentId` . Zpráva fronty `{ "departmentId" : "Finance" }` by vrátila všechny záznamy finančního oddělení.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód Pythonu:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

 ---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete nakonfigurovat, najdete v [následující části Konfigurace](#configuration).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

Z [běhové knihovny Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBOutput` anotaci k parametrům, které jsou zapsány do Cosmos DB. Typ parametru anotace by měl být `OutputBinding<T>` , kde `T` je buď nativní typ Java, nebo Pojo.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `CosmosDB` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový**     | Není k dispozici | Musí být nastaven na hodnotu `cosmosDB` .        |
|**směr**     | Není k dispozici | Musí být nastaven na hodnotu `in` .         |
|**Jméno**     | Není k dispozici | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databáze** |**DatabaseName** |Databáze obsahující dokument.        |
|**collectionName** |**Název kolekce** | Název kolekce, která obsahuje dokument. |
|**id**    | **Účet** | ID dokumentu, který se má načíst Tato vlastnost podporuje [výrazy vazby](./functions-bindings-expressions-patterns.md). Nenastavte `id` vlastnosti i **sqlQuery** . Pokud nenastavíte žádné nastavení, načte se celá kolekce. |
|**sqlQuery**  |**SqlQuery**  | Azure Cosmos DB dotaz SQL, který se používá k načítání více dokumentů. Vlastnost podporuje vazby za běhu, jako v tomto příkladu: `SELECT * FROM c where c.departmentId = {departmentId}` . Nenastavte `id` `sqlQuery` vlastnosti a. Pokud nenastavíte žádné nastavení, načte se celá kolekce.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec Azure Cosmos DB. |
|**partitionKey**|**PartitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může zahrnovat parametry vazby. Je vyžadován pro vyhledávání v [dělených](../cosmos-db/partitioning-overview.md#logical-partitions) kolekcích.|
|**preferredLocations**| **PreferredLocations**| Volitelné Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být oddělené čárkami. Například "Východní USA, Střed USA – jih, Severní Evropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Po úspěšném ukončení funkce budou všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Po úspěšném ukončení funkce budou všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

# <a name="java"></a>[Java](#tab/java)

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)tato [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) anotace zpřístupňuje Cosmos DB data do funkce. Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aktualizace nejsou automaticky provedeny při ukončení funkce. Místo toho použijte `context.bindings.<documentName>In` a `context.bindings.<documentName>Out` k provedení aktualizací. Další podrobnosti najdete v [příkladu JavaScriptu](#example) .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aktualizace dokumentů nejsou automaticky provedeny při ukončení funkce. Chcete-li aktualizovat dokumenty ve funkci, použijte [výstupní vazbu](./functions-bindings-cosmosdb-v2-input.md). Další podrobnosti najdete v [příkladu PowerShellu](#example) .

# <a name="python"></a>[Python](#tab/python)

Data jsou k dispozici pro funkci prostřednictvím `DocumentList` parametru. Změny provedené v dokumentu nejsou automaticky uchovány.

---

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření nebo úpravě dokumentu Azure Cosmos DB (aktivační událost)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)