---
title: Azure Cosmos DB vazby pro funkce 1.x
description: Pochopit, jak používat Azure Cosmos DB triggerů a vazeb v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: 97943bc17c9722ffcd8dc815ec67d033e8aa4fee
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234899"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB vazby pro Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Verze 1 – GA](functions-bindings-cosmosdb.md)
> * [Verze 2 – Preview](functions-bindings-cosmosdb-v2.md)

Tento článek vysvětluje, jak pracovat s [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) vazeb v Azure Functions. Azure Functions podporuje aktivaci, vstup a výstup vazby pro Azure Cosmos DB.

> [!NOTE]
> Tento článek je pro Azure Functions 1.x.  Informace o tom, jak používat tyto vazby funkcí 2.x, najdete v části [Azure Cosmos DB vazby pro Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>Tato vazba byl původně pojmenován DocumentDB. Ve verzi funkce 1.x, pouze aktivační událost byla přejmenována Cosmos DB; Vstupní vazby, výstup vazby a balíček NuGet zachovat název DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby Azure Cosmos DB verze funkcí jsou součástí 1.x [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) balíček NuGet verze 1.x. Zdrojový kód pro vazby je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Trigger

Aktivační událost DB Cosmos Azure používá [Azure Cosmos DB změnu kanálu](../cosmos-db/change-feed.md) naslouchat vložení a aktualizace napříč oddíly. Informační kanál změnu publikuje vložení a aktualizace, není odstranění.

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Přeskočit příklady aktivační události](#trigger---attributes)

### <a name="trigger---c-example"></a>Aktivační událost – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) která je volána, když jsou vložení nebo aktualizace v zadané databázi a kolekci.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents, 
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Přeskočit příklady aktivační události](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje, aktivační událost Cosmos DB vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zapíše zprávy protokolu při změně záznamy Cosmos DB.

Zde je vazba dat v *function.json* souboru:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Tady je kód skriptu jazyka C#:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

[Přeskočit příklady aktivační události](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje, aktivační událost Cosmos DB vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zapíše zprávy protokolu při změně záznamy Cosmos DB.

Zde je vazba dat v *function.json* souboru:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Tady je kód jazyka JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Aktivační událost – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atribut.

Konstruktoru atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a další vlastnosti, které můžete konfigurovat, najdete v článku [aktivační událost - konfigurace](#trigger---configuration). Tady je `CosmosDBTrigger` atribut příkladu podpis metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Úplný příklad najdete v tématu [aktivační událost - C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDBTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** || musí být nastavena na `cosmosDBTrigger`. |
|**direction** || musí být nastavena na `in`. Tento parametr je nastaven automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** || Název proměnné používá v kódu funkce, která představuje seznam dokumentů se změnami. | 
|**ConnectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, který obsahuje připojovací řetězec použitý pro připojení k účtu Azure Cosmos DB monitorovány. |
|**databaseName**|**DatabaseName**  | Název databáze Azure Cosmos DB s kolekcí monitorovány. |
|**Název_kolekce** |**Název_kolekce** | Název kolekce, který je monitorován. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Volitelné) Název nastavení aplikace, který obsahuje připojovací řetězec k službě, která kolekci zapůjčení. Pokud není nastavena, `connectionStringSetting` hodnota se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Volitelné) Název databáze, který obsahuje kolekci používá k ukládání zapůjčení. Pokud není nastavena hodnota `databaseName` nastavení se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Volitelné) Název kolekce používá k ukládání zapůjčení. Pokud není nastavena hodnota `leases` se používá. |
|**CreateLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Volitelné) Pokud nastavíte hodnotu `true`, kolekci zapůjčení se automaticky vytvoří, pokud ještě neexistuje. Výchozí hodnota je `false`. |
|**LeasesCollectionThroughput**| **LeasesCollectionThroughput**| (Volitelné) Definuje množství jednotky žádosti přiřadit při vytvoření kolekce zapůjčení. Toto nastavení je pouze použité při `createLeaseCollectionIfNotExists` je nastaven na `true`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu.
|**LeaseCollectionPrefix**| **LeaseCollectionPrefix**| (Volitelné) Pokud nastavíte, přidá předponu zapůjčení vytvořené v kolekci zapůjčení pro tuto funkci umožňuje efektivně dvě samostatné funkce Azure sdílet stejné zapůjčení kolekci pomocí jiné předpony.
|**FeedPollDelay**| **FeedPollDelay**| (Volitelné) Pokud sadu, definuje, v milisekundách, zpoždění mezi dotazování oddíl pro nové změny v informačním kanálu se nečekaně po všechny aktuální změny. Výchozí hodnota je 5000 (5 sekund).
|**LeaseAcquireInterval**| **LeaseAcquireInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, který ji úlohu Vypočítat, pokud oddíly jsou rovnoměrně rozdělené mezi instancí známé hostitele. Výchozí hodnota je 13000 (13 sekund).
|**LeaseExpirationInterval**| **LeaseExpirationInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, pro kterou je zapůjčení pořízené zapůjčení představující oddílu. Pokud během tohoto intervalu neobnovíte zapůjčení, může to způsobit vypršení platnosti, a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**LeaseRenewInterval**| **LeaseRenewInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval obnovení pro všechny zapůjčení pro oddíly, které jsou aktuálně uchovávat instance. Výchozí hodnota je 17000 (17 sekund).
|**CheckpointFrequency**| **CheckpointFrequency**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval mezi body obnovení zapůjčení. Výchozí hodnota je vždy po úspěšném volání funkce.
|**MaxItemsPerInvocation**| **MaxItemsPerInvocation**| (Volitelné) Pokud nastavíte, přizpůsobuje maximální množství přijatých položek pro volání funkce.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost - využití

Aktivační událost vyžaduje druhé kolekci, která se používá k ukládání _zapůjčení_ přes oddíly. Kolekce, který je monitorován a kolekce, která obsahuje zapůjčení musí být k dispozici pro aktivační událost pro práci.

>[!IMPORTANT]
> Pokud víc funkcí nakonfigurovány pro používání Cosmos DB aktivační událost pro stejné kolekci, každá z těchto funkcí použít kolekci s vyhrazenou zapůjčení nebo zadejte jinou `LeaseCollectionPrefix` pro každou funkci. Jinak se spustí jenom jedna z funkcí. Informace o předponu najdete v tématu [konfigurační oddíl](#trigger---configuration).

Aktivační událost není označuje, že zda dokument byl aktualizaci nebo vložení, pouze poskytuje samotného dokumentu. Pokud potřebujete pro zpracování aktualizace a vloží odlišně, můžete to udělat implementací pole časového razítka pro vložení nebo aktualizace.

## <a name="input"></a>Vstup

Načte jeden nebo více dokumentů Azure Cosmos DB a předává je do vstupní parametr funkce vstupní vazeb Azure Cosmos DB. Parametry dotazu nebo ID dokumentu lze určit podle aktivační událost, která volá funkci. 

>[!NOTE]
> Nechcete používat Azure Cosmos DB vstup nebo výstup vazby, pokud používáte rozhraní API MongoDB na účet Cosmos DB. Poškození dat je možné.

## <a name="input---examples"></a>Vstup - příklady

Podívejte se na konkrétní jazyk příklady, které čtou jednotlivý dokument tak, že zadáte hodnotu ID:

* [C#](#input---c-examples)
* [C# skript (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Přeskočit vstupní příklady](#input---attributes)

### <a name="input---c-examples"></a>Vstup - příklady jazyka C#

Tato část obsahuje následující příklady:

* [Aktivace fronty, vyhledejte ID z formátu JSON](#queue-trigger-look-up-id-from-json-c)
* [Aktivace protokolu HTTP, vyhledejte ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c)
* [Aktivace protokolu HTTP, vyhledávání ID z dat trasy](#http-trigger-look-up-id-from-route-data-c)
* [Aktivace protokolu HTTP, vyhledávání ID z dat trasy, pomocí SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP aktivovat, získat více dokumentů, pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP aktivovat, získat více dokumentů, pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Příklady najdete jednoduchou `ToDoItem` typu:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Aktivace fronty, vyhledejte ID z formátu JSON (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , načte jednotlivý dokument. Funkce se aktivuje zprávu fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON na objekt s názvem `ToDoItemLookup`, který obsahuje ID k vyhledání. Aby se ID slouží k načtení `ToDoItem` dokumentu z zadaná databáze a kolekce.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Aktivace protokolu HTTP, vyhledejte ID z řetězce dotazu (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , načte jednotlivý dokument. Funkce se aktivuje požadavek HTTP, který řetězec dotazu určuje ID k vyhledání. Aby se ID slouží k načtení `ToDoItem` dokumentu z zadaná databáze a kolekce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Aktivace protokolu HTTP, vyhledejte ID z dat trasy (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , načte jednotlivý dokument. Funkce se aktivuje požadavkem HTTP, používá směrování dat určete ID k vyhledání. Aby se ID slouží k načtení `ToDoItem` dokumentu z zadaná databáze a kolekce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Aktivace protokolu HTTP, vyhledejte ID z dat trasy, pomocí SqlQuery (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , načte jednotlivý dokument. Funkce se aktivuje požadavkem HTTP, používá směrování dat určete ID k vyhledání. Aby se ID slouží k načtení `ToDoItem` dokumentu z zadaná databáze a kolekce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP aktivovat, získat více dokumentů, pomocí SqlQuery (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , načte seznam dokumenty. Funkce se aktivuje požadavkem HTTP. Dotaz je uveden v `SqlQuery` vlastností atributu.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP aktivovat, získat více dokumentů, pomocí DocumentClient (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , načte seznam dokumenty. Funkce se aktivuje požadavkem HTTP. Kód používá `DocumentClient` zadaná v Azure Cosmos DB vazbě číst seznam dokumenty instance. `DocumentClient` Instance by mohly být použity také operace zápisu.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

### <a name="input---c-script-examples"></a>Vstup - příklady skriptů jazyka C#

Tato část obsahuje následující příklady, které čtou jednotlivý dokument tak, že zadáte hodnotu ID z různých zdrojů:

* Aktivace fronty, vyhledejte ID z fronty zpráv
* Aktivace fronty, vyhledejte ID z zprávu fronty pomocí SqlQuery

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-c-script"></a>Aktivace fronty, vyhledejte ID z zprávu fronty (C# skript)

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce přečte jednotlivý dokument a aktualizuje dokumentu textovou hodnotu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-c-script"></a>Aktivace fronty, vyhledejte ID z zprávu fronty pomocí SqlQuery (C# skript)

Následující příklad ukazuje vazbu vstupní v Azure Cosmos DB *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce načte více dokumentů určeného dotaz SQL, chcete-li přizpůsobit parametry dotazu pomocí aktivační procedury fronty.

Aktivační událost fronty poskytuje parametr `departmentId`. Fronty zpráv z `{ "departmentId" : "Finance" }` by vrátit všechny záznamy pro finančního oddělení. 

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

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

[Přeskočit vstupní příklady](#input---attributes)

### <a name="input---javascript-examples"></a>Vstup - příklady JavaScript

Tato část obsahuje následující příklady, které čtou jednotlivý dokument tak, že zadáte hodnotu ID z různých zdrojů:

* Aktivace fronty, vyhledejte ID z fronty zpráv
* Aktivace fronty, vyhledejte ID z zprávu fronty pomocí Sqlquery

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-javascript"></a>Aktivace fronty, vyhledejte ID z zprávu fronty (JavaScript)

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce přečte jednotlivý dokument a aktualizuje dokumentu textovou hodnotu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-javascript"></a>Aktivace fronty, vyhledejte ID z zprávu fronty pomocí SqlQuery (JavaScript)

Následující příklad ukazuje vazbu vstupní v Azure Cosmos DB *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce načte více dokumentů určeného dotaz SQL, chcete-li přizpůsobit parametry dotazu pomocí aktivační procedury fronty.

Aktivační událost fronty poskytuje parametr `departmentId`. Fronty zpráv z `{ "departmentId" : "Finance" }` by vrátit všechny záznamy pro finančního oddělení. 

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

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

[Přeskočit vstupní příklady](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Vstup - F # příklady

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.json* souboru a [F # funkce](functions-reference-fsharp.md) používající vazby. Funkce přečte jednotlivý dokument a aktualizuje dokumentu textovou hodnotu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Tento příklad vyžaduje, `project.json` soubor, který určuje `FSharp.Interop.Dynamic` a `Dynamitey` NuGet závislosti:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Chcete-li přidat `project.json` souborů najdete v tématu [správy balíčků F #](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>(Vstup) – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atribut.

Konstruktoru atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a další vlastnosti, které můžete konfigurovat, najdete v článku [následující konfigurační oddíl](#input---configuration). 

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `DocumentDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || musí být nastavena na `documentdb`.        |
|**direction**     || musí být nastavena na `in`.         |
|**Jméno**     || Název parametru vazby, který představuje dokumentu ve funkci.  |
|**databaseName** |**DatabaseName** |Databáze obsahující dokumentu.        |
|**Název_kolekce** |**Název_kolekce** | Název kolekce, která obsahuje dokument. |
|**ID**    | **ID** | ID dokumentu pro načtení. Tato vlastnost podporuje [vazby výrazy](functions-triggers-bindings.md#binding-expressions-and-patterns). Obě není nastavený **id** a **sqlQuery** vlastnosti. Pokud není nastavený buď jednu, je načíst celou kolekci. |
|**sqlQuery**  |**SqlQuery**  | Dotaz služby Azure Cosmos DB SQL použitý k načtení více dokumentů. Vlastnost podporuje runtime vazby, jako v následujícím příkladě: `SELECT * FROM c where c.departmentId = {departmentId}`. Obě není nastavený **id** a **sqlQuery** vlastnosti. Pokud není nastavený buď jednu, je načíst celou kolekci.|
|**Připojení**     |**ConnectionStringSetting**|Název nastavení aplikace obsahující připojovacího řetězce Azure Cosmos DB.        |
|**Klíč oddílu**|**Klíč oddílu**|Určuje hodnotu klíče oddílu pro vyhledávání. Může zahrnovat vázané parametry.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – použití

V jazyce C# a F # funkce při ukončení funkce úspěšně, některé změny provedené vstupní dokument prostřednictvím pojmenované vstupní parametry jsou automaticky nastavené jako trvalé. 

V funkce jazyka JavaScript nejsou automaticky provedeny aktualizace po ukončení funkce. Místo toho použijte `context.bindings.<documentName>In` a `context.bindings.<documentName>Out` pro nastavení aktualizací. Najdete v článku [příklad v jazyce JavaScript](#input---javascript-example).

## <a name="output"></a>Výstup

Azure Cosmos DB výstup vazby umožňuje zapsat nový textový dokument k databázi Azure Cosmos DB. 

>[!NOTE]
> Nechcete používat Azure Cosmos DB vstup nebo výstup vazby, pokud používáte rozhraní API MongoDB na účet Cosmos DB. Poškození dat je možné.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-examples)
* [C# skript (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

Viz také [vstupní příklad](#input---c-examples) používající `DocumentClient`.

[Přeskočit příklady výstup](#output---attributes)

### <a name="ouput---c-examples"></a>Výstup - příklady jazyka C#

Tato část obsahuje následující příklady:

* Aktivace fronty, jeden doc zápisu
* Aktivace fronty, dokumentace zápisu pomocí IAsyncCollector

Příklady najdete jednoduchou `ToDoItem` typu:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Přeskočit příklady výstup](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Aktivace fronty, jeden doc zápisu (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) doplňuje dokument k databázi, pomocí data zadaná v zpráv z fronty úložiště.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Přeskočit příklady výstup](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Aktivace fronty, dokumentace zápisu pomocí IAsyncCollector (C#)

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) doplňuje kolekci dokumentů k databázi, pomocí data zadaná v zprávu fronty JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Přeskočit příklady výstup](#output---attributes)

### <a name="output---c-script-examples"></a>Výstup – příklady skriptů jazyka C#

Následující příklad ukazuje výstup Azure Cosmos DB vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce, která používá vazbu vstupní fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří Azure Cosmos DB dokumenty ve formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

Pokud chcete vytvořit více dokumentů, můžete vázat na `ICollector<T>` nebo `IAsyncCollector<T>` kde `T` je jedním z podporovaných typů.

[Přeskočit příklady výstup](#output---attributes)

### <a name="output---javascript-examples"></a>Výstup – příklady JavaScript

Následující příklad ukazuje výstup Azure Cosmos DB vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce, která používá vazbu vstupní fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří Azure Cosmos DB dokumenty ve formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

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

[Přeskočit příklady výstup](#output---attributes)

### <a name="output---f-examples"></a>Výstup - F # příklady

Následující příklad ukazuje výstup Azure Cosmos DB vazby ve *function.json* souboru a [F # funkce](functions-reference-fsharp.md) používající vazby. Funkce, která používá vazbu vstupní fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří Azure Cosmos DB dokumenty ve formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Tento příklad vyžaduje, `project.json` soubor, který určuje `FSharp.Interop.Dynamic` a `Dynamitey` NuGet závislosti:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Chcete-li přidat `project.json` souborů najdete v tématu [správy balíčků F #](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Výstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atribut.

Konstruktoru atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a další vlastnosti, které můžete konfigurovat, najdete v článku [výstup - konfigurace](#output---configuration). Tady je `DocumentDB` atribut příkladu podpis metody:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `DocumentDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || musí být nastavena na `documentdb`.        |
|**direction**     || musí být nastavena na `out`.         |
|**Jméno**     || Název parametru vazby, který představuje dokumentu ve funkci.  |
|**databaseName** | **DatabaseName**|Databáze obsahující kolekci, kde se má vytvořit dokumentu.     |
|**Název_kolekce** |**Název_kolekce**  | Název kolekce, kde se má vytvořit dokumentu. |
|**CreateIfNotExists**  |**CreateIfNotExists**    | Logická hodnota označující, zda kolekce se vytvoří při neexistuje. Výchozí hodnota je *false* s vyhrazenou propustností, který obsahuje náklady důsledky se vytváří nové kolekce. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**Klíč oddílu**|**Klíč oddílu** |Když `CreateIfNotExists` hodnotu true, definuje cestu ke klíči oddílu pro vytvořenou kolekci.|
|**CollectionThroughput**|**CollectionThroughput**| Když `CreateIfNotExists` hodnotu true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**Připojení**    |**ConnectionStringSetting** |Název nastavení aplikace obsahující připojovacího řetězce Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – použití

Ve výchozím nastavení když zapíšete do výstupního parametru ve funkci, se dokumentu vytvoří v databázi. Tento dokument má automaticky vytvářenému identifikátoru GUID jako ID dokumentu. Můžete zadat ID dokumentu výstupního dokumentu zadáním `id` vlastnost v objektu JSON předaný výstupní parametr. 

> [!Note]  
> Pokud zadáte ID stávající dokument, získá přepsány nový dokument výstup. 

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejděte na rychlé spuštění, používající aktivační událost Cosmos DB](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Další informace o výpočetní s Cosmos DB bez serveru databáze](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
