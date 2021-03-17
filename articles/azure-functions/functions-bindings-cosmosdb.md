---
title: Vazby Azure Cosmos DB pro funkce 1. x
description: Vysvětlení použití aktivačních událostí a vazeb Azure Cosmos DB v Azure Functions 1. x.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002102"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB vazby pro Azure Functions 1. x

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-bindings-cosmosdb.md)
> * [Verze 2](functions-bindings-cosmosdb-v2.md)

Tento článek vysvětluje, jak pracovat s [Azure Cosmos DBmi](../cosmos-db/serverless-computing-database.md) vazbami v Azure Functions. Azure Functions podporuje vazby triggeru, vstupu a výstupu pro Azure Cosmos DB.

> [!NOTE]
> Tento článek je určen pro Azure Functions 1. x. Informace o tom, jak tyto vazby použít ve funkcích 2. x a vyšší, najdete v tématu [Azure Cosmos DB vazby pro Azure Functions 2. x](functions-bindings-cosmosdb-v2.md).
>
>Tato vazba se původně jmenovala jako DocumentDB. Ve funkcích Functions verze 1. x byla přejmenována pouze aktivační událost Cosmos DB; název DocumentDB si ponechá vstupní vazba, výstupní vazba a balíček NuGet.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Vazby Azure Cosmos DB se podporují jenom pro použití s rozhraním SQL API. U všech ostatních Azure Cosmos DB rozhraní API byste měli k databázi přistupovat z vaší funkce pomocí statického klienta pro vaše rozhraní API, včetně [rozhraní api Azure Cosmos DB pro MongoDB](../cosmos-db/mongodb-introduction.md), [rozhraní API CASSANDRA](../cosmos-db/cassandra-introduction.md), [Gremlin API](../cosmos-db/graph-introduction.md)a [rozhraní API pro tabulky](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Vazby Azure Cosmos DB pro funkce verze 1. x jsou k dispozici v balíčku NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) verze 1. x. Zdrojový kód pro vazby je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Trigger

Aktivační událost Azure Cosmos DB používá [Azure Cosmos DB změnu kanálu](../cosmos-db/change-feed.md) k naslouchání vkládání a aktualizací v různých oddílech. Kanál změn publikuje vkládání a aktualizace, nikoli odstranění.

## <a name="trigger---example"></a>Aktivační procedura – příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která je vyvolána při vložení nebo aktualizaci v zadané databázi a kolekci.

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

Následující příklad ukazuje Cosmos DB aktivační vazby v *function.js* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně Cosmos DBch záznamů.

Tady jsou data vazby v *function.js* souboru:

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
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Cosmos DB aktivační vazby v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně Cosmos DBch záznamů.

Tady jsou data vazby v *function.js* souboru:

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

Tady je kód JavaScriptu:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Aktivační atributy

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, najdete v tématu [Trigger-Configuration](#trigger---configuration). Tady je `CosmosDBTrigger` příklad atributu v signatuře metody:

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

Úplný příklad najdete v tématu [Trigger-C# – příklad](#trigger).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

---

## <a name="trigger---configuration"></a>Aktivační událost – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `CosmosDBTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | neuvedeno | Musí být nastaven na hodnotu `cosmosDBTrigger` . |
|**směr** | neuvedeno | Musí být nastaven na hodnotu `in` . Tento parametr se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec, který se používá pro připojení k monitorovanému účtu Azure Cosmos DB. |
|**Databáze**|**DatabaseName**  | Název databáze Azure Cosmos DB s monitorovanou kolekcí. |
|**collectionName** |**Název kolekce** | Název monitorované kolekce. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Volitelné Název nastavení aplikace, které obsahuje připojovací řetězec ke službě, která obsahuje kolekci zapůjčení. Pokud není nastavena, `connectionStringSetting` je použita hodnota. Tento parametr se automaticky nastaví při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení adres musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Volitelné Název databáze, která obsahuje kolekci používanou k uložení zapůjčení. Pokud není nastavená, použije se hodnota `databaseName` nastavení. Tento parametr se automaticky nastaví při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | Volitelné Název kolekce, která se používá k uložení zapůjčení Pokud není nastavena, `leases` je použita hodnota. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Volitelné Když se nastaví na `true` , kolekce zapůjčení se automaticky vytvoří, když už neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Volitelné Definuje množství jednotek požadavků, které se mají přiřadit při vytváření kolekce zapůjčení. Toto nastavení se používá pouze v případě `createLeaseCollectionIfNotExists` , že je parametr nastaven na hodnotu `true` . Tento parametr se automaticky nastaví, když se vytvoří vazba pomocí portálu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Volitelné Pokud je tato funkce nastavena, přidá předponu k zapůjčením vytvořeným v kolekci zapůjčení pro tuto funkci a efektivně umožní dvěma samostatným Azure Functions sdílet stejnou kolekci zapůjčení s použitím různých předpon.
|**feedPollDelay**| **FeedPollDelay**| Volitelné Při nastavení definuje v milisekundách zpoždění při cyklickém dotazování oddílu na nové změny v informačním kanálu po vyprázdnění všech aktuálních změn. Výchozí hodnota je 5000 (5 sekund).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Volitelné Když je tato možnost nastavená, definuje v milisekundách interval, po který se aktivuje úloha k výpočtu, pokud se oddíly rozdělují rovnoměrně mezi známé instance hostitele. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Volitelné Když je nastaveno, definuje v milisekundách interval, po který se zapůjčení zapůjčení zastupuje jako oddíl. Pokud se zapůjčení v rámci tohoto intervalu prodlouží, způsobí to vypršení platnosti a vlastnictví oddílu se přesune na jinou instanci. Výchozí hodnota je 60000 (60 s).
|**leaseRenewInterval**| **LeaseRenewInterval**| Volitelné Při nastavení definuje interval obnovování všech zapůjčení pro oddíly aktuálně držené instancí v milisekundách. Výchozí hodnota je 17000 (17 sekund).
|**checkpointFrequency**| **CheckpointFrequency**| Volitelné Při nastavení definuje interval mezi kontrolními body zapůjčení (v milisekundách). Výchozí hodnota je vždy po volání funkce.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Volitelné Při nastavení přizpůsobuje maximální množství položek přijatých na volání funkce.
|**startFromBeginning**| **StartFromBeginning**| Volitelné Když se nastaví, sdělí triggeru, aby začal číst změny od začátku historie kolekce namísto aktuálního času. To funguje jenom při prvním spuštění triggeru, stejně jako v dalších spuštěních, kontrolní body už jsou uložené. Toto nastavení na, `true` Pokud jsou již vytvořeny zapůjčené adresy, nemá žádný vliv.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost – využití

Trigger vyžaduje druhou kolekci, kterou používá k ukládání _zapůjčení_ do oddílů. Aby aktivační událost fungovala, musí být kolekce monitorovaná a kolekce obsahující zapůjčení.

>[!IMPORTANT]
> Pokud je pro stejnou kolekci nakonfigurované víc funkcí Cosmos DB triggeru, každá z těchto funkcí by měla používat vyhrazenou kolekci zapůjčení nebo `LeaseCollectionPrefix` pro každou funkci zadat jinou. V opačném případě se aktivuje jenom jedna z funkcí. Informace o předponě najdete v [části věnované konfiguraci](#trigger---configuration).

Aktivační událost neindikuje, jestli se dokument aktualizoval nebo vložil, jenom samotný dokument poskytuje. Pokud potřebujete zpracovávat aktualizace a vkládat je odlišně, můžete to provést implementací polí časového razítka pro vložení nebo aktualizaci.

## <a name="input"></a>Vstup

Vstupní vazba služby Azure Cosmos DB načítá pomocí rozhraní SQL API jeden nebo více dokumentů Azure Cosmos DB a předává je do vstupního parametru funkce. Parametry ID dokumentu nebo dotaz se dají určit podle aktivační události, která funkci volá.

## <a name="input---example"></a>Vstupní příklad

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

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována zprávou fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON na objekt s názvem `ToDoItemLookup` , který obsahuje ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

[Přeskočit příklady vstupu](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

[Přeskočit příklady vstupu](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Trigger HTTP, získání více dokumentů pomocí SqlQuery

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Dotaz je zadán ve `SqlQuery` vlastnosti atributu.

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

[Přeskočit příklady vstupu](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Trigger HTTP, získání více dokumentů s použitím DocumentClient (C#)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Kód používá `DocumentClient` instanci poskytnutou Azure Cosmos DB vazbou ke čtení seznamu dokumentů. `DocumentClient`Instanci lze také použít pro operace zápisu.

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

* [Aktivační událost fronty, vyhledání ID z řetězce](#queue-trigger-look-up-id-from-string-c-script)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c-script)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Trigger HTTP, získání více dokumentů pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Trigger HTTP, získání více dokumentů pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Příklady triggeru HTTP odkazují na jednoduchý `ToDoItem` Typ:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Aktivační událost fronty, vyhledání ID z řetězce

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

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

Následující příklad ukazuje [funkci skriptu jazyka C#](functions-reference-csharp.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Tady je kód skriptu jazyka C#:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje [funkci skriptu jazyka C#](functions-reference-csharp.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Tady je kód skriptu jazyka C#:

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

Tady je kód skriptu jazyka C#:

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

Tady je kód skriptu jazyka C#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Tato část obsahuje následující příklady:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

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

Následující příklad ukazuje [funkci JavaScriptu](functions-reference-node.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje [funkci JavaScriptu](functions-reference-node.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

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

---

## <a name="input---attributes"></a>Vstupní atributy

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete nakonfigurovat, najdete v [následující části Konfigurace](#input---configuration).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

---

## <a name="input---configuration"></a>Vstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `DocumentDB` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový**     | neuvedeno | Musí být nastaven na hodnotu `documentdb` .        |
|**směr**     | neuvedeno | Musí být nastaven na hodnotu `in` .         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databáze** |**DatabaseName** |Databáze obsahující dokument.        |
|**collectionName** |**Název kolekce** | Název kolekce, která obsahuje dokument. |
|**id**    | **Účet** | ID dokumentu, který se má načíst Tato vlastnost podporuje [výrazy vazby](./functions-bindings-expressions-patterns.md). Nenastavte vlastnosti **ID** i **sqlQuery** . Pokud nenastavíte žádné nastavení, načte se celá kolekce. |
|**sqlQuery**  |**SqlQuery**  | Azure Cosmos DB dotaz SQL, který se používá k načítání více dokumentů. Vlastnost podporuje vazby za běhu, jako v tomto příkladu: `SELECT * FROM c where c.departmentId = {departmentId}` . Nenastavte vlastnosti **ID** i **sqlQuery** . Pokud nenastavíte žádné nastavení, načte se celá kolekce.|
|**vázán**     |**ConnectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může zahrnovat parametry vazby.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Vstup – použití

# <a name="c"></a>[C#](#tab/csharp)

Po úspěšném ukončení funkce budou všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Po úspěšném ukončení funkce budou všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aktualizace nejsou automaticky provedeny při ukončení funkce. Místo toho použijte `context.bindings.<documentName>In` a `context.bindings.<documentName>Out` k provedení aktualizací. Podívejte se na [příklad vstupu](#input).

---

## <a name="output"></a>Výstup

Azure Cosmos DB výstupní vazba umožňuje napsat nový dokument do databáze Azure Cosmos DB pomocí rozhraní SQL API.

## <a name="output---example"></a>Výstup – příklad

# <a name="c"></a>[C#](#tab/csharp)

Tato část obsahuje následující příklady:

* Aktivační událost fronty, zápis jednoho dokumentu
* Aktivační událost fronty, zápis dokumentů pomocí `IAsyncCollector`

Příklady odkazují na jednoduchý `ToDoItem` Typ:

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

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která přidá dokument do databáze nástroje pomocí dat poskytovaných ve zprávě z úložiště Queue.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která do databáze přidává kolekci dokumentů pomocí dat poskytnutých ve formátu JSON zprávy ve frontě.

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
* Aktivační událost fronty, zápis dokumentů pomocí `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, zápis jednoho dokumentu

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří pro každý záznam Azure Cosmos DB dokumenty v následujícím formátu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Tady jsou data vazby v *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector

Chcete-li vytvořit více dokumentů, můžete vytvořit vazby k `ICollector<T>` nebo, `IAsyncCollector<T>` kde `T` je jeden z podporovaných typů.

Tento příklad odkazuje na jednoduchý `ToDoItem` Typ:

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

Tady je function.jssouboru:

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

Tady je kód skriptu jazyka C#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří pro každý záznam Azure Cosmos DB dokumenty v následujícím formátu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Tady jsou data vazby v *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód JavaScriptu:

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

## <a name="output---attributes"></a>Výstupní atributy

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které lze konfigurovat, naleznete v tématu [Output-Configuration](#output---configuration). Tady je `DocumentDB` příklad atributu v signatuře metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Úplný příklad naleznete v tématu [Output](#output).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

---

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `DocumentDB` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový**     | neuvedeno | Musí být nastaven na hodnotu `documentdb` .        |
|**směr**     | neuvedeno | Musí být nastaven na hodnotu `out` .         |
|**Jméno**     | neuvedeno | Název parametru vazby, který představuje dokument ve funkci.  |
|**Databáze** | **DatabaseName**|Databáze obsahující kolekci, ve které je dokument vytvořen.     |
|**collectionName** |**Název kolekce**  | Název kolekce, ve které je dokument vytvořen. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logická hodnota určující, zda je kolekce vytvořena, pokud neexistuje. Výchozí hodnota je *false* , protože nové kolekce jsou vytvořeny s rezervovanou propustností, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Když `CreateIfNotExists` je hodnota true, definuje cestu klíče oddílu pro vytvořenou kolekci.|
|**collectionThroughput**|**CollectionThroughput**| Pokud `CreateIfNotExists` je hodnota true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**vázán**    |**ConnectionStringSetting** |Název nastavení aplikace, které obsahuje připojovací řetězec Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Ve výchozím nastavení platí, že při zápisu do výstupního parametru ve funkci je v databázi vytvořen dokument. Tento dokument má jako ID dokumentu automaticky generovaný identifikátor GUID. Můžete zadat ID dokumentu výstupního dokumentu zadáním `id` vlastnosti do objektu JSON předaného výstupnímu parametru.

> [!Note]
> Když zadáte ID existujícího dokumentu, přepíše ho nový výstupní dokument.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Reference |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Další kroky

* [Další informace o výpočetních databázích bez serveru s Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
