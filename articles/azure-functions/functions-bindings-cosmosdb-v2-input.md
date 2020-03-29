---
title: Vstupní vazba Azure Cosmos DB pro funkce 2.x
description: Naučte se používat vstupní vazbu Azure Cosmos DB ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943427"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Vstupní vazba Azure Cosmos DB pro Funkce Azure 2.x

Vstupní vazba služby Azure Cosmos DB načítá pomocí rozhraní SQL API jeden nebo více dokumentů Azure Cosmos DB a předává je do vstupního parametru funkce. Parametry ID dokumentu nebo dotaz se dají určit podle aktivační události, která funkci volá.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Pokud je kolekce [rozdělena na oddíly](../cosmos-db/partition-data.md#logical-partitions), je třeba zadat také hodnotu klíče oddílu .
>

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledat ID z JSON](#queue-trigger-look-up-id-from-json-c)
* [Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c)
* [Aktivační událost HTTP, vyhledat ID z dat trasy](#http-trigger-look-up-id-from-route-data-c)
* [Aktivační událost HTTP, vyhledat ID z dat trasy pomocí sqlquery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Aktivační událost HTTP, získání více dokumentů pomocí sqlquery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Aktivační událost HTTP, získání více dokumentů pomocí documentclientu](#http-trigger-get-multiple-docs-using-documentclient-c)

Příklady odkazují na `ToDoItem` jednoduchý typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledat ID z JSON 

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna zprávou fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON do objektu typu `ToDoItemLookup`, který obsahuje hodnotu ID a klíče oddílu, která má být vyhledána. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá řetězec dotazu k určení hodnoty id a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

>[!NOTE]
>Parametr řetězce dotazu HTTP rozlišuje malá a velká písmena.
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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení Hodnoty ID a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, vyhledat ID z dat trasy pomocí sqlquery

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení ID pro vyvyhledávání. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

Příklad ukazuje, jak použít výraz `SqlQuery` vazby v parametru. Data trasy můžete předat `SqlQuery` do zobrazeného parametru, ale v současné době [nelze předat hodnoty řetězce dotazu](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Pokud potřebujete dotaz pouze podle ID, doporučujeme použít vyhledat, stejně jako [v předchozích příkladech](#http-trigger-look-up-id-from-query-string-c), protože bude spotřebovávat méně [jednotek požadavku](../cosmos-db/request-units.md). Operace čtení bodů (GET) jsou [efektivnější](../cosmos-db/optimize-cost-queries.md) než dotazy podle ID.
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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost HTTP, získání více dokumentů pomocí sqlquery

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte seznam dokumentů. Funkce je spuštěna požadavkem HTTP. Dotaz je určen `SqlQuery` ve vlastnosti atributu.

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Aktivační událost HTTP, získání více dokumentů pomocí documentclientu

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte seznam dokumentů. Funkce je spuštěna požadavkem HTTP. Kód používá `DocumentClient` instanci poskytovanou vazbou Azure Cosmos DB ke čtení seznamu dokumentů. Instance `DocumentClient` může být také použita pro operace zápisu.

> [!NOTE]
> Můžete také použít rozhraní [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) usnadnit testování.

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

* [Aktivační událost fronty, vyhledat ID z řetězce](#queue-trigger-look-up-id-from-string-c-script)
* [Aktivační událost fronty, získání více dokumentů pomocí sqlquery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c-script)
* [Aktivační událost HTTP, vyhledat ID z dat trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Aktivační událost HTTP, získání více dokumentů pomocí sqlquery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Aktivační událost HTTP, získání více dokumentů pomocí documentclientu](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Příklady aktivačních událostí PROTOKOLU `ToDoItem` HTTP odkazují na jednoduchý typ:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Aktivační událost fronty, vyhledat ID z řetězce

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Zde jsou data vazby v souboru *function.json:*

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
[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí sqlquery

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí aktivační události fronty k přizpůsobení parametrů dotazu.

Aktivační událost fronty `departmentId`poskytuje parametr . Zpráva fronty `{ "departmentId" : "Finance" }` vrátí všechny záznamy pro finanční oddělení.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu

Následující příklad ukazuje [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá řetězec dotazu k určení hodnoty id a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

Zde je *soubor function.json:*

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

Zde je kód skriptu C#:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení Hodnoty ID a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

Zde je *soubor function.json:*

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

Zde je kód skriptu C#:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost HTTP, získání více dokumentů pomocí sqlquery

Následující příklad ukazuje [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která načítá seznam dokumentů. Funkce je spuštěna požadavkem HTTP. Dotaz je určen `SqlQuery` ve vlastnosti atributu.

Zde je *soubor function.json:*

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

Zde je kód skriptu C#:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Aktivační událost HTTP, získání více dokumentů pomocí documentclientu

Následující příklad ukazuje [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která načítá seznam dokumentů. Funkce je spuštěna požadavkem HTTP. Kód používá `DocumentClient` instanci poskytovanou vazbou Azure Cosmos DB ke čtení seznamu dokumentů. Instance `DocumentClient` může být také použita pro operace zápisu.

Zde je *soubor function.json:*

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

Zde je kód skriptu C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledat ID z JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-javascript)
* [Aktivační událost HTTP, vyhledat ID z dat trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Aktivační událost fronty, získání více dokumentů pomocí sqlquery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledat ID z JSON

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *function.json* a [funkci JavaScript,](functions-reference-node.md) která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu

Následující příklad ukazuje [funkci JavaScriptu,](functions-reference-node.md) která načítá jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá řetězec dotazu k určení hodnoty id a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

Zde je *soubor function.json:*

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

Zde je kód JavaScript:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje [funkci JavaScriptu,](functions-reference-node.md) která načítá jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení Hodnoty ID a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

Zde je *soubor function.json:*

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
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Zde je kód JavaScript:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí sqlquery

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí aktivační události fronty k přizpůsobení parametrů dotazu.

Aktivační událost fronty `departmentId`poskytuje parametr . Zpráva fronty `{ "departmentId" : "Finance" }` vrátí všechny záznamy pro finanční oddělení.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledat ID z JSON](#queue-trigger-look-up-id-from-json-python)
* [Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-python)
* [Aktivační událost HTTP, vyhledat ID z dat trasy](#http-trigger-look-up-id-from-route-data-python)
* [Aktivační událost fronty, získání více dokumentů pomocí sqlquery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledat ID z JSON

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *function.json* a [funkci Pythonu,](functions-reference-python.md) která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu

Následující příklad ukazuje [funkci Pythonu,](functions-reference-python.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá řetězec dotazu k určení hodnoty id a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

Zde je *soubor function.json:*

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
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Zde je kód Pythonu:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje [funkci Pythonu,](functions-reference-python.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení Hodnoty ID a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče `ToDoItem` oddílu se používají k načtení dokumentu ze zadané databáze a kolekce.

Zde je *soubor function.json:*

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

Zde je kód Pythonu:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí sqlquery

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *function.json* a [funkci Pythonu,](functions-reference-python.md) která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí aktivační události fronty k přizpůsobení parametrů dotazu.

Aktivační událost fronty `departmentId`poskytuje parametr . Zpráva fronty `{ "departmentId" : "Finance" }` vrátí všechny záznamy pro finanční oddělení.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu – parametr String](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Aktivační událost HTTP, vyhledávání ID z řetězce dotazu - parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Aktivační událost HTTP, vyhledat ID z dat trasy](#http-trigger-look-up-id-from-route-data-java)
* [Aktivační událost HTTP, vyhledat ID z dat trasy pomocí sqlquery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Aktivační událost HTTP, získání více dokumentů z dat trasy pomocí sqlquery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Příklady odkazují na `ToDoItem` jednoduchý typ:

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

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu – parametr String

Následující příklad ukazuje funkci Java, která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá řetězec dotazu k určení hodnoty id a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče oddílu se používají k načtení dokumentu ze zadané databáze a kolekce ve formuláři String.

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

V [knihovně runtime funkcí Javy](/java/api/overview/azure/functions/runtime)použijte poznámku `@CosmosDBInput` o parametrech funkce, jejichž hodnota by pocházela z Cosmos DB.  Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Aktivační událost HTTP, vyhledávání ID z řetězce dotazu - parametr POJO

Následující příklad ukazuje funkci Java, která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá řetězec dotazu k určení hodnoty id a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče oddílu použitá k načtení dokumentu ze zadané databáze a kolekce. Dokument je pak převeden na instanci ```ToDoItem``` POJO dříve vytvořené a předán jako argument funkce.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje funkci Java, která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá parametr trasy k určení hodnoty ID a klíče oddílu, kterou chcete vyhledat. Toto ID a hodnota klíče oddílu ```Optional<String>```se používají k načtení dokumentu ze zadané databáze a kolekce a vrátí jej jako .

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, vyhledat ID z dat trasy pomocí sqlquery

Následující příklad ukazuje funkci Java, která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá parametr trasy k určení ID pro vyvyhledávání. Toto ID se používá k načtení dokumentu ze zadané databáze ```ToDoItem[]```a kolekce a převod sady výsledků na aplikaci , protože v závislosti na kritériích dotazu může být vráceno mnoho dokumentů.

> [!NOTE]
> Pokud potřebujete dotaz pouze podle ID, doporučujeme použít vyhledat, stejně jako [v předchozích příkladech](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), protože bude spotřebovávat méně [jednotek požadavku](../cosmos-db/request-units.md). Operace čtení bodů (GET) jsou [efektivnější](../cosmos-db/optimize-cost-queries.md) než dotazy podle ID.
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

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, získání více dokumentů z dat trasy pomocí sqlquery

Následující příklad ukazuje funkci Java, která načítá více dokumentů. Funkce je spuštěna požadavkem HTTP, ```desc``` který používá parametr trasy ```description``` k určení řetězce, který má být v poli vyhledán. Hledaný termín se používá k načtení kolekce dokumentů ze zadané databáze a ```ToDoItem[]``` kolekce, převod sady výsledků na a a předání jako argument funkce.

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

 ---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, naleznete [v následující části konfigurace](#configuration).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Z [knihovny runtime funkcí](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) `@CosmosDBOutput` Jazyka Java použijte poznámku o parametrech, které zapisují do Cosmos DB. Typ parametru poznámky `OutputBinding<T>`by `T` měl být , kde je buď nativní typ Java nebo POJO.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `CosmosDB` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ**     | neuvedeno | Musí být `cosmosDB`nastavena na .        |
|**direction**     | neuvedeno | Musí být `in`nastavena na .         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databasename** |**DatabaseName** |Databáze obsahující dokument.        |
|**název kolekce** |**CollectionName** | Název kolekce, která obsahuje dokument. |
|**Id**    | **ID** | ID dokumentu načíst. Tato vlastnost podporuje [výrazy vazby](./functions-bindings-expressions-patterns.md). Nenastavovat vlastnosti `id` **sqlQuery.** Pokud nenastavíte jeden z nich, načte se celá kolekce. |
|**sqlQuery**  |**Dotaz SqlQuery**  | Dotaz SQL Azure Cosmos DB, který se používá k načtení více dokumentů. Vlastnost podporuje vazby za běhu, jako `SELECT * FROM c where c.departmentId = {departmentId}`v tomto příkladu: . Nenastavuj `id` te `sqlQuery` vlastnosti i vlastnosti. Pokud nenastavíte jeden z nich, načte se celá kolekce.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Název nastavení aplikace obsahující připojovací řetězec Azure Cosmos DB. |
|**partitionKey**|**PartitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může obsahovat parametry vazby. Je vyžadováno pro vyhledávání v [rozdělených](../cosmos-db/partition-data.md#logical-partitions) kolekcích.|
|**preferredLocations**| **Preferované umístění**| (Nepovinné) Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být odděleny čárkami. Například "Východní USA, jižní střed USA, severní Evropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Při úspěšném ukončení funkce jsou automaticky zachovány všechny změny provedené ve vstupním dokumentu pomocí pojmenovaných vstupních parametrů.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Při úspěšném ukončení funkce jsou automaticky zachovány všechny změny provedené ve vstupním dokumentu pomocí pojmenovaných vstupních parametrů.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Aktualizace nejsou prováděny automaticky při ukončení funkce. Místo toho `context.bindings.<documentName>In` `context.bindings.<documentName>Out` použijte a proveďte aktualizace. Podívejte se na příklad JavaScriptu.

# <a name="python"></a>[Python](#tab/python)

Data jsou k dispozici funkci `DocumentList` prostřednictvím parametru. Změny provedené v dokumentu nejsou automaticky zachovány.

# <a name="java"></a>[Java](#tab/java)

Z [knihovny runtime](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)funkcí [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) Java zpřístupňuje anotace data Cosmos DB do funkce. Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

---

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při vytvoření nebo úpravě dokumentu Azure Cosmos DB (aktivační událost)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)