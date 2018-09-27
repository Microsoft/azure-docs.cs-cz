---
title: Vazby Azure Cosmos DB pro službu Functions 2.x
description: Vysvětlení použití služby Azure Cosmos DB triggerů a vazeb ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 0ea2a58e61d781e6ebfb72b860a3b7aed6ce0c7d
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394470"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Vazby Azure Cosmos DB pro službu Azure Functions 2.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Verze 1 – GA](functions-bindings-cosmosdb.md)
> * [Verze 2 – Preview](functions-bindings-cosmosdb-v2.md)

Tento článek vysvětluje, jak pracovat s [služby Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) vazby ve službě Azure Functions 2.x. Azure Functions podporuje aktivaci, vstupní a výstupní vazby pro službu Azure Cosmos DB.

> [!NOTE]
> Tento článek je určený pro [Azure Functions verze 2.x](functions-versions.md).  Informace o tom, jak používat tyto vazby ve funkcích 1.x, najdete v článku [vazby Azure Cosmos DB pro službu Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> Tato vazba se původně nazývala DocumentDB. Funkce verze 2.x, aktivační událost, vazby a balíčků jsou všechny s názvem služby Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Podporované rozhraní API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby Azure Cosmos DB pro verzi Functions jsou součástí 2.x [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) balíčku NuGet, verze 3.x. Zdrojový kód pro vazbu se [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Aktivační událost Azure Cosmos DB používá [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) k naslouchání pro vkládání a aktualizace napříč oddíly. Kanál změn publikuje vkládání a aktualizace, odstranění není.

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

[Příklady přeskočit triggeru](#trigger---attributes)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , které je voláno, když jsou vloží nebo aktualizuje v zadané databázi a kolekci.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
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

[Příklady přeskočit triggeru](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v trigger Cosmos DB *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

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

[Příklady přeskočit triggeru](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v trigger Cosmos DB *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

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

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad ukazuje trigger Cosmos DB vazby v *function.json* souboru a [Java funkce](functions-reference-java.md) , který používá vazba. Funkce se tak zapojí při vložení nebo aktualizace v zadané databázi a kolekci.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Tady je kód Java:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            reateLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@CosmosDBTrigger` poznámku o parametrech, jehož hodnota bude pocházet ze služby Cosmos DB.  Tato poznámka je možné s nativní typy v jazyce Java, objektů Pojo nebo s povolenou hodnotou Null hodnoty pomocí nepovinné<T>. 

## <a name="trigger---c-attributes"></a>Trigger – C# atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atribut.

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastností, které můžete nakonfigurovat, najdete v části [aktivační události – konfigurace](#trigger---configuration). Tady je `CosmosDBTrigger` příklad atributů v podpisu metody:

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

Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).


## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDBTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** || Musí být nastaveno na `cosmosDBTrigger`. |
|**direction** || Musí být nastaveno na `in`. Tento parametr je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** || Název této proměnné v kódu funkce, která představuje seznam dokumentů se změnami. | 
|**connectionStringSetting**|**connectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec použitý pro připojení k účtu Azure Cosmos DB, který je monitorován. |
|**databaseName**|**DatabaseName**  | Název databáze Azure Cosmos DB s kolekcí monitorovány. |
|**collectionName** |**collectionName** | Název kolekce monitorovány. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Volitelné) Název nastavení aplikace, které obsahuje připojovací řetězec pro službu, která obsahuje kolekci zapůjčení. Pokud není nastavená, `connectionStringSetting` hodnota se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Volitelné) Název databáze, která obsahuje kolekci pro ukládání zapůjčení. Pokud není nastavený hodnotu `databaseName` nastavení se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Volitelné) Název kolekce pro ukládání zapůjčení. Pokud není nastavený hodnotu `leases` se používá. |
|**createLeaseCollectionIfNotExists** | **createLeaseCollectionIfNotExists** | (Volitelné) Pokud je nastavena na `true`, kolekci zapůjčení se automaticky vytvoří, pokud ještě neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Volitelné) Definuje počet jednotek požadavků přiřazení, když se vytvoří kolekci zapůjčení. Toto nastavení je pouze použité při `createLeaseCollectionIfNotExists` je nastavena na `true`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Volitelné) Pokud nastavíte, přidá předponu zapůjčení vytvořené v kolekci zapůjčení pro tuto funkci umožňuje efektivně dvě samostatné funkce Azure s použitím různých předpony sdílet stejnou kolekci zapůjčení.
|**feedPollDelay**| **feedPollDelay**| (Volitelné) Pokud sada, definuje, v milisekundách, zpoždění mezi dotazování oddílu pro nové změny na informační kanál, jsou Vyprázdněné všechny aktuální změny. Výchozí je 5 000 milisekund (5 sekund).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval aktivovala úloha Vypočítat, pokud oddíly jsou rovnoměrně mezi známými hostiteli instance. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, pro kterou je zapůjčení pořízené zapůjčení představující oddílu. Pokud v rámci tohoto intervalu nedojde k jeho prodloužení zapůjčení, způsobí vypršení platnosti a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**leaseRenewInterval**| **leaseRenewInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval obnovení pro všechny zapůjčení pro oddíly právě načtený v instanci. Výchozí hodnota je 17000 (17 sekund).
|**checkpointFrequency**| **checkpointFrequency**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval mezi zapůjčení kontrolní body. Výchozí hodnota je vždy po úspěšném volání funkce.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Volitelné) Pokud nastavíte, přizpůsobuje maximální objem přijatých položek pro volání funkce.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

Aktivační událost vyžaduje druhá kolekce, která se používá k ukládání _zapůjčení_ napříč oddíly. Kolekce, monitoruje a kolekci, která obsahuje zapůjčení musí být k dispozici pro aktivační událost pro práci.

>[!IMPORTANT]
> Pokud více funkcí jsou nakonfigurovány pro použití trigger Cosmos DB pro jednu kolekci, každá z těchto funkcí použít kolekci s vyhrazenou zapůjčení nebo zadejte jiný `LeaseCollectionPrefix` pro každou funkci. V opačném případě se aktivuje jenom jedna z funkcí. Informace o předponu, najdete v článku [konfigurační oddíl](#trigger---configuration).

Aktivační událost neukazuje, jestli dokument byl aktualizaci nebo vložení, stačí poskytuje samotný dokument. Pokud budete potřebovat pro zpracování aktualizací a vloží odlišně, můžete to udělat pomocí implementace pole časového razítka pro vložení nebo aktualizace.

## <a name="input"></a>Vstup

Vstupní vazby Azure Cosmos DB pomocí rozhraní SQL API pro načtení jedné nebo více dokumentů Azure Cosmos DB a předává je do vstupní parametr funkce. Parametry ID nebo dotaz, dokument se dá určit podle aktivační událost, která volá funkci. 

## <a name="input---examples"></a>(Vstup) – příklady

Podívejte se na příklady specifické pro jazyk, které čtení jednoho dokumentu tak, že zadáte hodnotu ID:

* [C#](#input---c-examples)
* [C# skript (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)

[Přeskočit vstupní příklady](#input---attributes)

### <a name="input---c-examples"></a>(Vstup) – příklady jazyka C#

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledejte ID z formátu JSON](#queue-trigger-look-up-id-from-json-c)
* [Trigger HTTP, vyhledejte ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c)
* [Trigger HTTP, vyhledejte ID z dat trasy](#http-trigger-look-up-id-from-route-data-c)
* [Trigger HTTP, vyhledejte ID z dat trasy, pomocí SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP aktivovat, více dokumenty pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP aktivovat, více dokumenty pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Příklady najdete jednoduchý `ToDoItem` typu:

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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Aktivační událost fronty, vyhledejte ID z formátu JSON (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Funkce aktivované zpráv fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje ve formátu JSON na objekt s názvem `ToDoItemLookup`, který obsahuje ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

```cs
namespace CosmosDBSamplesV2
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

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Trigger HTTP, vyhledejte ID z řetězce dotazu (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

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
            return new OkResult();
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Trigger HTTP, vyhledejte ID z dat trasy (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, že používá směrování dat k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
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
            return new OkResult();
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Trigger HTTP, vyhledejte ID z dat trasy, pomocí SqlQuery (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, že používá směrování dat k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce. 

Tento příklad ukazuje způsob použití vazbového výrazu v `SqlQuery` parametru. Můžete předat data trasy pro `SqlQuery` parametru, jak je vidět, ale aktuálně [nelze předat hodnoty řetězce dotazu](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP aktivovat, více dokumenty pomocí SqlQuery (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Je funkce aktivována požadavkem HTTP. Dotaz je zadán v `SqlQuery` atribut vlastnosti.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP aktivovat, více dokumenty pomocí DocumentClient (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Je funkce aktivována požadavkem HTTP. Tento kód použije `DocumentClient` instance poskytované vazby Azure Cosmos DB ke čtení seznamu dokumentů. `DocumentClient` Instance možné využít také pro operace zápisu.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.Info($"Searching for: {searchterm}");

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
            return new OkResult();
        }
    }
}
```

[Přeskočit vstupní příklady](#input---attributes)

### <a name="input---c-script-examples"></a>(Vstup) – příklady skriptu jazyka C#

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledejte ID z řetězce](#queue-trigger-look-up-id-from-string-c-script)
* [Aktivační událost fronty, více dokumenty pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Trigger HTTP, vyhledejte ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c-script)
* [Trigger HTTP, vyhledejte ID z dat trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP aktivovat, více dokumenty pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP aktivovat, více dokumenty pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Příklady triggeru HTTP odkazují na jednoduchý `ToDoItem` typu:

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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Aktivační událost fronty, vyhledejte ID z řetězce (skript jazyka C#)

Následující příklad ukazuje vstupní vazby Cosmos DB v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce přečte jednotlivý dokument a aktualizuje hodnotu textového dokumentu.

Zde je vazba dat v *function.json* souboru:

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
[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

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

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Aktivační událost fronty, více dokumenty pomocí SqlQuery (skript jazyka C#)

Následující příklad ukazuje vstupní vazby Azure Cosmos DB, v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Tato funkce načítá více dokumentů vybraných podle dotazu SQL, pomocí aktivační událost fronty přizpůsobit parametry dotazu.

Aktivační událost fronty obsahuje parametr `departmentId`. Zprávy z fronty `{ "departmentId" : "Finance" }` vrátí všechny záznamy z finančního oddělení. 

Zde je vazba dat v *function.json* souboru:

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

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

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Trigger HTTP, vyhledejte ID z řetězce dotazu (skript jazyka C#)

Následující příklad ukazuje [funkce skriptu jazyka C#](functions-reference-csharp.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

Tady je *function.json* souboru:

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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Trigger HTTP, vyhledejte ID z dat trasy (skript jazyka C#)

Následující příklad ukazuje [funkce skriptu jazyka C#](functions-reference-csharp.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, že používá směrování dat k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

Tady je *function.json* souboru:

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP aktivovat, více dokumenty pomocí SqlQuery (skript jazyka C#)

Následující příklad ukazuje [funkce skriptu jazyka C#](functions-reference-csharp.md) , která načte seznam dokumentů. Je funkce aktivována požadavkem HTTP. Dotaz je zadán v `SqlQuery` atribut vlastnosti.

Tady je *function.json* souboru:

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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP aktivovat, více dokumenty pomocí DocumentClient (skript jazyka C#)

Následující příklad ukazuje [funkce skriptu jazyka C#](functions-reference-csharp.md) , která načte seznam dokumentů. Je funkce aktivována požadavkem HTTP. Tento kód použije `DocumentClient` instance poskytované vazby Azure Cosmos DB ke čtení seznamu dokumentů. `DocumentClient` Instance možné využít také pro operace zápisu.

Tady je *function.json* souboru:

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

[Přeskočit vstupní příklady](#input---attributes)

### <a name="input---javascript-examples"></a>(Vstup) – příklady jazyka JavaScript

Tato část obsahuje následující příklady, které čtou jeden dokument tak, že zadáte hodnotu ID z různých zdrojů:

* [Aktivační událost fronty, vyhledejte ID z formátu JSON](#queue-trigger-look-up-id-from-string-javascript)
* [Trigger HTTP, vyhledejte ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-javascript)
* [Trigger HTTP, vyhledejte ID z dat trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Aktivační událost fronty, více dokumenty pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Aktivační událost fronty, vyhledejte ID z formátu JSON (JavaScript)

Následující příklad ukazuje vstupní vazby Cosmos DB v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce přečte jednotlivý dokument a aktualizuje hodnotu textového dokumentu.

Zde je vazba dat v *function.json* souboru:

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
[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

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

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Trigger HTTP, vyhledejte ID z řetězce dotazu (JavaScript)

Následující příklad ukazuje [funkce jazyka JavaScript](functions-reference-node.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

Tady je *function.json* souboru:

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Tady je kód jazyka JavaScript:

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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Trigger HTTP, vyhledejte ID z dat trasy (JavaScript)

Následující příklad ukazuje [funkce jazyka JavaScript](functions-reference-node.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

Tady je *function.json* souboru:

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
      "type": "cosmosDB",
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

Tady je kód jazyka JavaScript:

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

[Přeskočit vstupní příklady](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Aktivační událost fronty, více dokumenty pomocí SqlQuery (JavaScript)

Následující příklad ukazuje vstupní vazby Azure Cosmos DB, v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Tato funkce načítá více dokumentů vybraných podle dotazu SQL, pomocí aktivační událost fronty přizpůsobit parametry dotazu.

Aktivační událost fronty obsahuje parametr `departmentId`. Zprávy z fronty `{ "departmentId" : "Finance" }` vrátí všechny záznamy z finančního oddělení. 

Zde je vazba dat v *function.json* souboru:

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

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

### <a name="input---f-examples"></a>(Vstup) – příklady F #

Následující příklad ukazuje vstupní vazby Cosmos DB v *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce přečte jednotlivý dokument a aktualizuje hodnotu textového dokumentu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Tento příklad vyžaduje `project.json` soubor, který určuje, `FSharp.Interop.Dynamic` a `Dynamitey` závislostí NuGet:

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

Chcete-li přidat `project.json` souborů naleznete v tématu [správy balíčků F #](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Vstup - příkladů v jazyce Java

Následující příklad ukazuje funkci Java, která načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, který používá řetězec dotazu k určení ID se má vyhledat. Toto ID se používá k načtení dokumentu ToDoItem ze zadané databáze a kolekce.

Tady je kód Java:

```java
@FunctionName("getItem")
public String cosmosDbQueryById(
    @HttpTrigger(name = "req",
                  methods = {HttpMethod.GET},
                  authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> dummy,
    @CosmosDBInput(name = "database",
                      databaseName = "ToDoList",
                      collectionName = "Items",
                      leaseCollectionName = "",
                      id = "{Query.id}"
                      connectionStringSetting = "AzureCosmosDBConnection") Optional<String> item,
    final ExecutionContext context
 ) {
    return item.orElse("Not found");
 }
 ```

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@CosmosDBInput` Poznámka k parametrům funkcí, jehož hodnota bude pocházet ze služby Cosmos DB.  Tato poznámka je možné s nativní typy v jazyce Java, objektů Pojo nebo s povolenou hodnotou Null hodnoty pomocí nepovinné<T>. 

## <a name="input---attributes"></a>(Vstup) – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atribut.

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastností, které můžete nakonfigurovat, najdete v části [následující konfigurační oddíl](#input---configuration). 

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || Musí být nastaveno na `cosmosDB`.        |
|**direction**     || Musí být nastaveno na `in`.         |
|**Jméno**     || Název parametru vazby, který představuje dokument ve funkci.  |
|**databaseName** |**DatabaseName** |Databáze obsahující dokumentu.        |
|**collectionName** |**collectionName** | Název, který bude obsahovat dokumentu. |
|**ID**    | **ID** | ID dokumentu má načíst. Tato vlastnost podporuje [výrazy vazeb](functions-triggers-bindings.md#binding-expressions-and-patterns). Obě nemají nastavený **id** a **sqlQuery** vlastnosti. Pokud nemají nastavený buď jeden, načte celou kolekci. |
|**sqlQuery**  |**SqlQuery**  | Dotaz SQL služby Azure Cosmos DB používá k získávání více dokumentů. Vlastnost podporuje vazby modulu runtime, jako v následujícím příkladu: `SELECT * FROM c where c.departmentId = {departmentId}`. Obě nemají nastavený **id** a **sqlQuery** vlastnosti. Pokud nemají nastavený buď jeden, načte celou kolekci.|
|**connectionStringSetting**     |**connectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Azure Cosmos DB.        |
|**partitionKey**|**partitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může obsahovat parametry vazby.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

V jazyce C# a F # funkce když funkce skončí úspěšně, všechny změny provedené vstupní dokument přes pojmenované vstupní parametry jsou automaticky trvalé. 

Do funkce jazyka JavaScript nejsou automaticky provedeny aktualizace při ukončení funkce. Místo toho použijte `context.bindings.<documentName>In` a `context.bindings.<documentName>Out` ke zpřístupnění aktualizací. Zobrazit [příklad v jazyce JavaScript](#input---javascript-example).

## <a name="output"></a>Výstup

Azure Cosmos DB výstupní vazbu umožňuje zapsat nový dokument k databázi Azure Cosmos DB pomocí rozhraní SQL API. 

## <a name="output---examples"></a>Výstup – příklady

Podívejte se na příklady specifické pro jazyk:

* [C#](#output---c-examples)
* [C# skript (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)
* [Java](#output---java-example)

Viz také [vstupní příklad](#input---c-examples) , která používá `DocumentClient`.

[Přeskočit výstup příklady](#output---attributes)

### <a name="ouput---c-examples"></a>Výstup – příklady jazyka C#

Tato část obsahuje následující příklady:

* Aktivační událost fronty, jeden zápis dokumentu
* Aktivační událost fronty, zápis dokumentace pomocí IAsyncCollector

Příklady najdete jednoduchý `ToDoItem` typu:

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

[Přeskočit výstup příklady](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Aktivační událost fronty, zápis jednoho dokumentu (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) dokumentu, který přidá do databáze s využitím dat zadat zprávy z fronty úložiště.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Přeskočit výstup příklady](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Aktivační událost fronty, zápis dokumentace pomocí IAsyncCollector (C#)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) kolekce dokumentů, který přidá do databáze s využitím dat podle zpráv fronty JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

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

[Přeskočit výstup příklady](#output---attributes)

### <a name="output---c-script-examples"></a>Výstup – příklady skriptu jazyka C#

Tato část obsahuje následující příklady:

* Aktivační událost fronty, jeden zápis dokumentu
* Aktivační událost fronty, zápis dokumentace pomocí IAsyncCollector

[Přeskočit výstup příklady](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Aktivační událost fronty, zápis jednoho dokumentu (skript jazyka C#)

Následující příklad ukazuje vazby ve službě Azure Cosmos DB výstup *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce používá fronty vstupní vazby pro fronty, která přijímá JSON v následujícím formátu:

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

Zde je vazba dat v *function.json* souboru:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentace pomocí IAsyncCollector

Pokud chcete vytvořit několik dokumentů, můžete svázat `ICollector<T>` nebo `IAsyncCollector<T>` kde `T` je jedním z podporovaných typů.

V tomto příkladu odkazuje na jednoduchý `ToDoItem` typu:

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

[Přeskočit výstup příklady](#output---attributes)

### <a name="output---javascript-examples"></a>Výstup – příklady jazyka JavaScript

Následující příklad ukazuje vazby ve službě Azure Cosmos DB výstup *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce používá fronty vstupní vazby pro fronty, která přijímá JSON v následujícím formátu:

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

Zde je vazba dat v *function.json* souboru:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

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

[Přeskočit výstup příklady](#output---attributes)

### <a name="output---f-examples"></a>Výstup – příklady F #

Následující příklad ukazuje vazby ve službě Azure Cosmos DB výstup *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce používá fronty vstupní vazby pro fronty, která přijímá JSON v následujícím formátu:

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

Zde je vazba dat v *function.json* souboru:

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
[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

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

Tento příklad vyžaduje `project.json` soubor, který určuje, `FSharp.Interop.Dynamic` a `Dynamitey` závislostí NuGet:

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

Chcete-li přidat `project.json` souborů naleznete v tématu [správy balíčků F #](functions-reference-fsharp.md#package).

## <a name="output---java-examples"></a>Výstup – příkladů v jazyce Java

Následující příklad ukazuje funkci Java, která přidá dokumentu do databáze s daty ze zprávy v rámci Queue storage.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database", databaseName = "ToDoList", collectionName = "Items", connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
     @QueueTrigger(name = "msg", queueName = "myqueue-items", connection = "AzureWebJobsStorage") String message,
     final ExecutionContext context
)  {
     return "{ id: " + System.currentTimeMillis() + ", Description: " + message + " }";
   }
```

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@CosmosDBOutput` poznámku o parametrech, které se zapíšou do služby Cosmos DB.  Typ anotace parametru by měl být OutputBinding<T>, kde T je nativní typ Java nebo objekt POJO.


## <a name="output---attributes"></a>Výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atribut.

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastností, které můžete nakonfigurovat, najdete v části [výstup - konfigurace](#output---configuration). Tady je `CosmosDB` příklad atributů v podpisu metody:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || Musí být nastaveno na `cosmosDB`.        |
|**direction**     || Musí být nastaveno na `out`.         |
|**Jméno**     || Název parametru vazby, který představuje dokument ve funkci.  |
|**databaseName** | **DatabaseName**|Databáze obsahující kolekci, do které se vytvoří dokument.     |
|**collectionName** |**collectionName**  | Název kolekce, ve kterém se vytvoří dokument. |
|**createIfNotExists**  |**createIfNotExists**    | Logická hodnota označující, zda kolekce je vytvořena při neexistuje. Výchozí hodnota je *false* protože nové kolekce se vytvoří s vyhrazenou propustností, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**partitionKey** |Když `CreateIfNotExists` má hodnotu true, určuje cestu ke klíči oddílů pro vytvořenou kolekci.|
|**collectionThroughput**|**collectionThroughput**| Když `CreateIfNotExists` má hodnotu true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**connectionStringSetting**    |**connectionStringSetting** |Název nastavení aplikace, které obsahuje připojovací řetězec služby Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Ve výchozím nastavení při zápisu do výstupního parametru ve funkci, je dokument vytvořené ve vaší databázi. Tento dokument obsahuje automaticky generovaný identifikátor GUID jako identifikátor dokumentu ID dokumentu výstupní dokument můžete zadat tak, že zadáte `id` vlastnost v objektu JSON předaný do parametru výstupu. 

> [!Note]  
> Pokud chcete zadat ID existující dokument, získá přepsána nový výstupní dokument. 

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| CosmosDB | [Kódy chyb služby cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Další postup

* [Další informace o databáze bez serveru, výpočetního prostředí pomocí služby Cosmos DB](..\cosmos-db\serverless-computing-database.md)
* [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
