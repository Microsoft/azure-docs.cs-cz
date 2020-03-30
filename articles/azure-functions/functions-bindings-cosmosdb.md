---
title: Vazby Azure Cosmos DB pro funkce 1.x
description: Zjistěte, jak používat aktivační události a vazby Azure Cosmos DB ve funkcích Azure.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277541"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Vazby Azure Cosmos DB pro funkce Azure 1.x

> [!div class="op_single_selector" title1="Vyberte verzi runtime Azure Functions, kterou používáte: "]
> * [Verze 1](functions-bindings-cosmosdb.md)
> * [Verze 2](functions-bindings-cosmosdb-v2.md)

Tento článek vysvětluje, jak pracovat s vazby [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) v Azure Functions. Funkce Azure podporuje aktivační, vstupní a výstupní vazby pro Azure Cosmos DB.

> [!NOTE]
> Tento článek je pro Azure Functions 1.x. Informace o tom, jak používat tyto vazby ve funkcích 2.x a vyšší, najdete v [tématu Azure Cosmos DB vazby pro Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>Tato vazba byla původně pojmenována DocumentDB. Ve functions verze 1.x byla pouze aktivační událost přejmenována na Cosmos DB; vstupní vazby, výstupní vazby a NuGet balíček zachovat DocumentDB název.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Vazby Azure Cosmos DB se podporují jenom pro použití s rozhraním SQL API. Pro všechny ostatní rozhraní API Db Služby Azure Cosmos byste měli přistupovat k databázi z vaší funkce pomocí statického klienta pro vaše rozhraní API, včetně [rozhraní API Služby Azure Cosmos DB pro MongoDB](../cosmos-db/mongodb-introduction.md), [rozhraní API Cassandra](../cosmos-db/cassandra-introduction.md), rozhraní [GREMLIN API](../cosmos-db/graph-introduction.md)a rozhraní TABLE [API](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Balíčky - Funkce 1.x

Vazby Azure Cosmos DB pro funkce verze 1.x jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet verze 1.x. Zdrojový kód pro vazby je v [azure-webjobs-sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub úložiště.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Trigger

Azure Cosmos DB Trigger používá [kanál změn Azure Cosmos DB](../cosmos-db/change-feed.md) k naslouchání vkládání a aktualizacím napříč oddíly. Kanál změn publikuje vložení a aktualizace, nikoli odstranění.

## <a name="trigger---example"></a>Aktivační událost – příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která je vyvolána, když jsou vloží nebo aktualizace v zadané databázi a kolekci.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazby aktivační události Cosmos DB v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

Zde jsou data vazby v souboru *function.json:*

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

Zde je kód skriptu C#:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazby aktivační události Cosmos DB v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která používá vazbu. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

Zde jsou data vazby v souboru *function.json:*

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

Zde je kód JavaScript:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Aktivační událost - atributy

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, naleznete v tématu [Trigger - configuration](#trigger---configuration). Zde je `CosmosDBTrigger` příklad atributu v podpisu metody:

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

Úplný příklad naleznete v tématu [Trigger - C# příklad](#trigger).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

---

## <a name="trigger---configuration"></a>Trigger - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `CosmosDBTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `cosmosDBTrigger`nastavena na . |
|**direction** | neuvedeno | Musí být `in`nastavena na . Tento parametr se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, která obsahuje připojovací řetězec používaný k připojení k účtu Azure Cosmos DB, který se monitoruje. |
|**Databasename**|**DatabaseName**  | Název databáze Azure Cosmos DB s kolekcí, která je monitorována. |
|**název kolekce** |**CollectionName** | Název kolekce, která je sledována. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Nepovinné) Název nastavení aplikace, která obsahuje připojovací řetězec ke službě, která obsahuje kolekci zapůjčení. Není-li nastavena, použije se `connectionStringSetting` hodnota. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**Název_databáze Lease** | (Nepovinné) Název databáze, která obsahuje kolekci slouží k ukládání zapůjčení. Pokud není nastavena, `databaseName` použije se hodnota nastavení. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **Název kolekce lease** | (Nepovinné) Název kolekce slouží k ukládání zapůjčení. Není-li nastavena, použije se hodnota. `leases` |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Nepovinné) Pokud je `true`nastavena na , kolekce zapůjčení je automaticky vytvořena, pokud ještě neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Nepovinné) Definuje množství jednotek požadavků, které mají být přiřazeny při vytvoření kolekce zapůjčení. Toto nastavení se `createLeaseCollectionIfNotExists` používá `true`pouze při nastavení na . Tento parametr je automaticky nastaven při vytvoření vazby pomocí portálu.
|**leaseCollectionPrefix**| **Předpona kolekce zapůjčení**| (Nepovinné) Když nastavit, přidá předponu zapůjčení vytvořené v kolekci zapůjčení pro tuto funkci, efektivně umožňuje dvě samostatné funkce Azure sdílet stejnou kolekci Lease pomocí různých předpon.
|**feedPollDelay**| **FeedPollDelay**| (Nepovinné) Při nastavení definuje v milisekundách prodlevu mezi dotazováním oddílu pro nové změny v informačním kanálu po vyčerpání všech aktuálních změn. Výchozí hodnota je 5000 (5 sekund).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nepovinné) Při nastavení definuje v milisekundách interval pro zahájení úlohy pro výpočet, pokud jsou oddíly rovnoměrně rozloženy mezi známé instance hostitele. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **Interval vypršení platnosti zapůjčení**| (Nepovinné) Při nastavení definuje v milisekundách interval, pro který je zapůjčení přijato na zapůjčení představující oddíl. Pokud zapůjčení není obnovena v rámci tohoto intervalu, způsobí, že vyprší jeho platnost a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**leaseRenewInterval**| **Obnovit interval**| (Nepovinné) Při nastavení definuje v milisekundách interval obnovení pro všechny zapůjčené pro oddíly, které jsou aktuálně v držení instance. Výchozí hodnota je 17000 (17 sekund).
|**kontrolní bodFrekvence**| **Kontrolní frekvence**| (Nepovinné) Při nastavení definuje v milisekundách interval mezi kontrolními body zapůjčení. Výchozí hodnota je vždy po každém volání funkce.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Nepovinné) Při nastavení přizpůsobí maximální množství položek přijatých na volání funkce.
|**startFromBeginning**| **StartFromBeginning**| (Nepovinné) Při nastavení sděluje Trigger začít číst změny od začátku historie kolekce namísto aktuálního času. To funguje pouze při prvním spuštění triggeru, jako v následných spuštěních jsou kontrolní body již uloženy. Nastavení na `true` v případě, že jsou již vytvořeny zapůjčení nemá žádný vliv.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost - využití

Aktivační událost vyžaduje druhou kolekci, která používá k ukládání _zapůjčení_ přes oddíly. Kolekce, která je sledována, a kolekce, která obsahuje zapůjčení, musí být k dispozici pro aktivační událost.

>[!IMPORTANT]
> Pokud více funkcí jsou nakonfigurovány pro použití aktivační události Cosmos DB pro stejnou kolekci, každá z funkcí by měla používat vyhrazenou kolekci zapůjčení nebo zadat jinou `LeaseCollectionPrefix` pro každou funkci. V opačném případě bude spuštěna pouze jedna z funkcí. Informace o předponě naleznete v [části Konfigurace](#trigger---configuration).

Aktivační událost neoznačuje, zda byl dokument aktualizován nebo vložen, pouze poskytuje samotný dokument. Pokud potřebujete zpracovávat aktualizace a vloží jinak, můžete to provést implementací polí časového razítka pro vložení nebo aktualizaci.

## <a name="input"></a>Vstup

Vstupní vazba služby Azure Cosmos DB načítá pomocí rozhraní SQL API jeden nebo více dokumentů Azure Cosmos DB a předává je do vstupního parametru funkce. Parametry ID dokumentu nebo dotaz se dají určit podle aktivační události, která funkci volá.

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledat ID z JSON

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna zprávou fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON do `ToDoItemLookup`objektu s názvem , který obsahuje ID vyhledat. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost PROTOKOLU HTTP, vyhledávání ID z řetězce dotazu

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení ID pro vyvyhledávání. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, vyhledat ID z dat trasy pomocí sqlquery

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení ID pro vyvyhledávání. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost HTTP, získání více dokumentů pomocí sqlquery

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte seznam dokumentů. Funkce je spuštěna požadavkem HTTP. Dotaz je určen `SqlQuery` ve vlastnosti atributu.

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

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Aktivační událost HTTP, získání více dokumentů pomocí documentclientu (C#)

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která načte seznam dokumentů. Funkce je spuštěna požadavkem HTTP. Kód používá `DocumentClient` instanci poskytovanou vazbou Azure Cosmos DB ke čtení seznamu dokumentů. Instance `DocumentClient` může být také použita pro operace zápisu.

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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

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

Následující příklad ukazuje [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Zde je kód skriptu C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
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
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledat ID z dat trasy

Následující příklad ukazuje [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která načte jeden dokument. Funkce je spuštěna požadavkem HTTP, který používá data trasy k určení ID pro vyvyhledávání. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Zde je kód skriptu C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
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
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Tato část obsahuje následující příklady:

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

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

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

Následující příklad ukazuje [funkci JavaScriptu,](functions-reference-node.md) která načítá jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

Následující příklad ukazuje [funkci JavaScriptu,](functions-reference-node.md) která načítá jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se `ToDoItem` používá k načtení dokumentu ze zadané databáze a kolekce.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

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

---

## <a name="input---attributes"></a>Vstup - atributy

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, naleznete [v následující části konfigurace](#input---configuration).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

---

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `DocumentDB` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ**     | neuvedeno | Musí být `documentdb`nastavena na .        |
|**direction**     | neuvedeno | Musí být `in`nastavena na .         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databasename** |**DatabaseName** |Databáze obsahující dokument.        |
|**název kolekce** |**CollectionName** | Název kolekce, která obsahuje dokument. |
|**Id**    | **ID** | ID dokumentu načíst. Tato vlastnost podporuje [výrazy vazby](./functions-bindings-expressions-patterns.md). Nenastavovat **id** a **sqlQuery** vlastnosti. Pokud nenastavíte jeden z nich, načte se celá kolekce. |
|**sqlQuery**  |**Dotaz SqlQuery**  | Dotaz SQL Azure Cosmos DB, který se používá k načtení více dokumentů. Vlastnost podporuje vazby za běhu, jako `SELECT * FROM c where c.departmentId = {departmentId}`v tomto příkladu: . Nenastavovat **id** a **sqlQuery** vlastnosti. Pokud nenastavíte jeden z nich, načte se celá kolekce.|
|**Připojení**     |**ConnectionStringSetting**|Název nastavení aplikace obsahující připojovací řetězec Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může obsahovat parametry vazby.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Vstup - využití

# <a name="c"></a>[C #](#tab/csharp)

Při úspěšném ukončení funkce jsou automaticky zachovány všechny změny provedené ve vstupním dokumentu pomocí pojmenovaných vstupních parametrů.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Při úspěšném ukončení funkce jsou automaticky zachovány všechny změny provedené ve vstupním dokumentu pomocí pojmenovaných vstupních parametrů.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Aktualizace nejsou prováděny automaticky při ukončení funkce. Místo toho `context.bindings.<documentName>In` `context.bindings.<documentName>Out` použijte a proveďte aktualizace. Viz [vstupní příklad](#input).

---

## <a name="output"></a>Výstup

Výstupní vazba Azure Cosmos DB umožňuje zapsat nový dokument do databáze Azure Cosmos DB pomocí rozhraní SQL API.

# <a name="c"></a>[C #](#tab/csharp)

Tato část obsahuje následující příklady:

* Aktivační událost fronty, zápis jednoho dokumentu
* Aktivační událost fronty, zápis dokumentů pomocí`IAsyncCollector`

Příklady odkazují na `ToDoItem` jednoduchý typ:

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

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, zápis jednoho dokumentu

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která přidá dokument do databáze, pomocí dat poskytnutých ve zprávě z úložiště fronty.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí nástroje IAsyncCollector

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která přidá kolekci dokumentů do databáze, pomocí dat uvedených ve zprávě fronty JSON.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Tato část obsahuje následující příklady:

* Aktivační událost fronty, zápis jednoho dokumentu
* Aktivační událost fronty, zápis dokumentů pomocí`IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurační](#output---configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí nástroje IAsyncCollector

Chcete-li vytvořit více dokumentů, `IAsyncCollector<T>` `T` můžete vytvořit vazbu na `ICollector<T>` nebo kde je jeden z podporovaných typů.

Tento příklad odkazuje na `ToDoItem` jednoduchý typ:

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

Zde je soubor function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Zde je kód skriptu C#:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurační](#output---configuration) část vysvětluje tyto vlastnosti.

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

---

## <a name="output---attributes"></a>Výstup - atributy

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, naleznete v [tématu Output - configuration](#output---configuration). Zde je `DocumentDB` příklad atributu v podpisu metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Úplný příklad naleznete v tématu [Výstup](#output).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

---

## <a name="output---configuration"></a>Výstup - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `DocumentDB` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ**     | neuvedeno | Musí být `documentdb`nastavena na .        |
|**direction**     | neuvedeno | Musí být `out`nastavena na .         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databasename** | **DatabaseName**|Databáze obsahující kolekci, kde je dokument vytvořen.     |
|**název kolekce** |**CollectionName**  | Název kolekce, kde je dokument vytvořen. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logická hodnota označující, zda je kolekce vytvořena, když neexistuje. Výchozí hodnota je *false,* protože nové kolekce jsou vytvořeny s vyhrazenou propustnost, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Když `CreateIfNotExists` je true, definuje cestu klíče oddílu pro vytvořenou kolekci.|
|**collectionThroughput**|**CollectionThroughput**| Když `CreateIfNotExists` je true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**Připojení**    |**ConnectionStringSetting** |Název nastavení aplikace obsahující připojovací řetězec Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup - využití

Ve výchozím nastavení je při zápisu do výstupního parametru ve funkci vytvořen dokument v databázi. Tento dokument má jako ID dokumentu automaticky vygenerovaný identifikátor GUID. ID dokumentu výstupního dokumentu můžete určit zadáním `id` vlastnosti v objektu JSON předané výstupnímu parametru.

> [!Note]
> Když zadáte ID existujícího dokumentu, přepíše se nový výstupní dokument.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Odkaz |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Další kroky

* [Další informace o výpočetní technice bez serveru s Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
