---
title: Azure Cosmos DB vazby pro Functions 2. x
description: Vysvětlení použití služby Azure Cosmos DB triggerů a vazeb ve službě Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: da05dc7136a75d519660412f2ce176f7530eb392
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547434"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB vazby pro Azure Functions 2. x

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-bindings-cosmosdb.md)
> * [Verze 2](functions-bindings-cosmosdb-v2.md)

Tento článek vysvětluje, jak pracovat s [Azure Cosmos DBmi](../cosmos-db/serverless-computing-database.md) vazbami v Azure Functions 2. x. Azure Functions podporuje aktivaci, vstupní a výstupní vazby pro službu Azure Cosmos DB.

> [!NOTE]
> Tento článek je určen pro [Azure Functions verze 2. x](functions-versions.md).  Informace o tom, jak tyto vazby použít ve funkcích 1. x, najdete v tématu [Azure Cosmos DB vazby pro Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Tato vazba se původně nazývala DocumentDB. Ve funkcích Functions verze 2. x jsou triggery, vazby a balíčky všechny pojmenované Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Podporovaná rozhraní API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby Azure Cosmos DB pro funkce verze 2. x jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , verze 3. x. Zdrojový kód pro vazbu se [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Aktivační událost Azure Cosmos DB používá [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) k naslouchání pro vkládání a aktualizace napříč oddíly. Kanál změn publikuje vkládání a aktualizace, odstranění není.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , které je voláno, když jsou vloží nebo aktualizuje v zadané databázi a kolekci.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje vazby v trigger Cosmos DB *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazby v trigger Cosmos DB *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Tady je kód Pythonu:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="javatabjava"></a>[Java](#tab/java)

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci jazyka Java](functions-reference-java.md) , která používá vazbu. Funkce je vyvolána, když jsou vloženy nebo aktualizovány v zadané databázi a kolekci.

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
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBTrigger` anotaci u parametrů, jejichž hodnota pochází z Cosmos DB.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>`.

---

## <a name="trigger---attributes-and-annotations"></a>Aktivační události – atributy a poznámky

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atribut.

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastností, které můžete nakonfigurovat, najdete v části [aktivační události – konfigurace](#trigger---configuration). Tady je `CosmosDBTrigger` příklad atributů v podpisu metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Úplný příklad najdete v tématu [Trigger](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Z [běhové knihovny Functions jazyka Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)použijte anotaci `@CosmosDBInput` u parametrů, které čtou data z Cosmos DB.

---

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDBTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastaveno na `cosmosDBTrigger`. |
|**direction** | – | Musí být nastaveno na `in`. Tento parametr je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**name** | – | Název této proměnné v kódu funkce, která představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**connectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec použitý pro připojení k účtu Azure Cosmos DB, který je monitorován. |
|**databaseName**|**DatabaseName**  | Název databáze Azure Cosmos DB s kolekcí monitorovány. |
|**collectionName** |**collectionName** | Název kolekce monitorovány. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Volitelné Název nastavení aplikace, které obsahuje připojovací řetězec k účtu Azure Cosmos DB, který obsahuje kolekci zapůjčení. Pokud není nastavená, `connectionStringSetting` hodnota se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Volitelné) Název databáze, která obsahuje kolekci pro ukládání zapůjčení. Pokud není nastavený hodnotu `databaseName` nastavení se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Volitelné) Název kolekce pro ukládání zapůjčení. Pokud není nastavený hodnotu `leases` se používá. |
|**createLeaseCollectionIfNotExists** | **createLeaseCollectionIfNotExists** | (Volitelné) Pokud je nastavena na `true`, kolekci zapůjčení se automaticky vytvoří, pokud ještě neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| Volitelné Definuje počet jednotek žádostí, které se mají přiřadit při vytváření kolekce zapůjčení. Toto nastavení se používá pouze v případě, že je `createLeaseCollectionIfNotExists` nastaveno na hodnotu `true`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| Volitelné Při nastavení se hodnota přidá jako předpona pro zapůjčení vytvořená v kolekci zapůjčení pro tuto funkci. Použití předpony umožňuje dvě samostatné Azure Functions sdílet stejnou kolekci zapůjčení s použitím různých předpon.
|**feedPollDelay**| **feedPollDelay**| Volitelné Čas (v milisekundách) pro prodlevu mezi dotazem na oddíl pro nové změny v informačním kanálu po vyprázdnění všech aktuálních změn. Výchozí hodnota je 5 000 milisekund nebo 5 sekund.
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval aktivovala úloha Vypočítat, pokud oddíly jsou rovnoměrně mezi známými hostiteli instance. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, pro kterou je zapůjčení pořízené zapůjčení představující oddílu. Pokud v rámci tohoto intervalu nedojde k jeho prodloužení zapůjčení, způsobí vypršení platnosti a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**leaseRenewInterval**| **leaseRenewInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval obnovení pro všechny zapůjčení pro oddíly právě načtený v instanci. Výchozí hodnota je 17000 (17 sekund).
|**checkpointFrequency**| **checkpointFrequency**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval mezi zapůjčení kontrolní body. Výchozí hodnota je vždy po volání funkce.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| Volitelné Při nastavení tato vlastnost nastaví maximální počet položek přijatých na volání funkce. Pokud se operace v monitorované kolekci provádějí prostřednictvím uložených procedur, [obor transakce](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) se zachová při čtení položek z kanálu změn. V důsledku toho může být počet přijatých položek vyšší, než je zadaná hodnota, aby byly položky změněné stejnou transakcí vráceny jako součást jedné atomická dávky.
|**startFromBeginning**| **StartFromBeginning**| Volitelné Tato možnost oznamuje triggeru, aby přečetl změny ze začátku historie změn kolekce místo začátku v aktuálním čase. Čtení od začátku funguje jenom při prvním spuštění triggeru, stejně jako v dalších spuštěních, kontrolní body už jsou uložené. Nastavení této možnosti na `true`, pokud jsou již vytvořeny zapůjčené adresy, nemá žádný vliv.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

Aktivační událost vyžaduje druhá kolekce, která se používá k ukládání _zapůjčení_ napříč oddíly. Kolekce, monitoruje a kolekci, která obsahuje zapůjčení musí být k dispozici pro aktivační událost pro práci.

>[!IMPORTANT]
> Pokud více funkcí jsou nakonfigurovány pro použití trigger Cosmos DB pro jednu kolekci, každá z těchto funkcí použít kolekci s vyhrazenou zapůjčení nebo zadejte jiný `LeaseCollectionPrefix` pro každou funkci. V opačném případě se aktivuje jenom jedna z funkcí. Informace o předponu, najdete v článku [konfigurační oddíl](#trigger---configuration).

Aktivační událost neukazuje, jestli dokument byl aktualizaci nebo vložení, stačí poskytuje samotný dokument. Pokud budete potřebovat pro zpracování aktualizací a vloží odlišně, můžete to udělat pomocí implementace pole časového razítka pro vložení nebo aktualizace.

## <a name="input"></a>Vstup

Vstupní vazby Azure Cosmos DB pomocí rozhraní SQL API pro načtení jedné nebo více dokumentů Azure Cosmos DB a předává je do vstupní parametr funkce. Parametry ID nebo dotaz, dokument se dá určit podle aktivační událost, která volá funkci.

> [!NOTE]
> Pokud je kolekce [rozdělená na oddíly](../cosmos-db/partition-data.md#logical-partitions), operace vyhledávání musí také určovat hodnotu klíče oddílu.
>

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON 

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Funkce aktivované zpráv fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON na objekt typu `ToDoItemLookup`, který obsahuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který načte jednotlivý dokument. Je funkce aktivována požadavkem HTTP, že používá směrování dat k určení ID se má vyhledat. Že ID slouží k načtení `ToDoItem` dokument ze zadané databáze a kolekce.

Příklad ukazuje, jak použít výraz vazby v parametru `SqlQuery`. Data trasy můžete předat parametru `SqlQuery`, jak je znázorněno, ale v současné době [nelze předat hodnoty řetězce dotazu](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Pokud potřebujete dotazovat pouze podle ID, doporučuje se použít hledání, podobně jako v [předchozích příkladech](#http-trigger-look-up-id-from-query-string-c), protože bude spotřebovávat méně [jednotek žádosti](../cosmos-db/request-units.md). Operace čtení bodu čtení (GET) [jsou efektivnější](../cosmos-db/optimize-cost-queries.md) než dotazy podle ID.
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

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Je funkce aktivována požadavkem HTTP. Dotaz je zadán v `SqlQuery` atribut vlastnosti.

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

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která načte seznam dokumentů. Je funkce aktivována požadavkem HTTP. Tento kód použije `DocumentClient` instance poskytované vazby Azure Cosmos DB ke čtení seznamu dokumentů. `DocumentClient` Instance možné využít také pro operace zápisu.

> [!NOTE]
> K usnadnění testování můžete také použít rozhraní [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) .

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

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

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

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Aktivační událost fronty, vyhledání ID z řetězce

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

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí SqlQuery

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

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje [funkce skriptu jazyka C#](functions-reference-csharp.md) , který načte jednotlivý dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje [funkce skriptu jazyka C#](functions-reference-csharp.md) , který načte jednotlivý dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledejte ID z formátu JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Trigger HTTP, vyhledejte ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-javascript)
* [Trigger HTTP, vyhledejte ID z dat trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Aktivační událost fronty, více dokumenty pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON

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

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu

Následující příklad ukazuje [funkce jazyka JavaScript](functions-reference-node.md) , který načte jednotlivý dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
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

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Aktivační událost HTTP, vyhledání ID z dat trasy

Následující příklad ukazuje [funkce jazyka JavaScript](functions-reference-node.md) , který načte jednotlivý dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Aktivační událost fronty, získání více dokumentů pomocí SqlQuery

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledejte ID z formátu JSON](#queue-trigger-look-up-id-from-json-python)
* [Trigger HTTP, vyhledejte ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-python)
* [Trigger HTTP, vyhledejte ID z dat trasy](#http-trigger-look-up-id-from-route-data-python)
* [Aktivační událost fronty, více dokumenty pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Aktivační událost fronty, vyhledání ID z formátu JSON

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce přečte jednotlivý dokument a aktualizuje hodnotu textového dokumentu.

Zde je vazba dat v *function.json* souboru:

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

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

Následující příklad ukazuje funkci jazyka [Python](functions-reference-python.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje funkci jazyka [Python](functions-reference-python.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Tato funkce načítá více dokumentů vybraných podle dotazu SQL, pomocí aktivační událost fronty přizpůsobit parametry dotazu.

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

Tady je kód Pythonu:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="javatabjava"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Trigger HTTP, vyhledání ID z řetězce dotazu – parametr řetězce](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Trigger HTTP, vyhledání ID z řetězce dotazu – parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Trigger HTTP, vyhledejte ID z dat trasy](#http-trigger-look-up-id-from-route-data-java)
* [Trigger HTTP, vyhledejte ID z dat trasy, pomocí SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Aktivační událost HTTP, získání více dokumentů z dat směrování pomocí SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Příklady najdete jednoduchý `ToDoItem` typu:

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

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte anotaci `@CosmosDBInput` u parametrů funkce, jejichž hodnota pochází z Cosmos DB.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Trigger HTTP, vyhledání ID z řetězce dotazu – parametr POJO

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu použité k načtení dokumentu ze zadané databáze a kolekce. Dokument se pak převede na instanci ```ToDoItem``` POJO dříve vytvořenou a předal jako argument funkci.

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

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá parametr směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu se používají k načtení dokumentu ze zadané databáze a kolekce a jejich vrácení jako ```Optional<String>```.

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

Následující příklad ukazuje funkci jazyka Java, která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá parametr směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení dokumentu ze zadané databáze a kolekce, převod sady výsledků na ```ToDoItem[]```, protože v závislosti na kritériích dotazu může být vráceno mnoho dokumentů.

> [!NOTE]
> Pokud potřebujete dotazovat pouze podle ID, doporučuje se použít hledání, podobně jako v [předchozích příkladech](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), protože bude spotřebovávat méně [jednotek žádosti](../cosmos-db/request-units.md). Operace čtení bodu čtení (GET) [jsou efektivnější](../cosmos-db/optimize-cost-queries.md) než dotazy podle ID.
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

Následující příklad ukazuje funkci jazyka Java, která načte více dokumentů. Funkce se aktivuje požadavkem HTTP, který používá parametr trasy ```desc``` k určení řetězce, který se má vyhledat v poli ```description```. Hledaný termín se používá k načtení kolekce dokumentů ze zadané databáze a kolekce, převodu sady výsledků na ```ToDoItem[]``` a jejím předání jako argumentu funkce.

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

## <a name="input---attributes-and-annotations"></a>Vstupní atributy a poznámky

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastností, které můžete nakonfigurovat, najdete v části [následující konfigurační oddíl](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Z [běhové knihovny Functions jazyka Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)použijte `@CosmosDBOutput` anotaci u parametrů, které zapisují do Cosmos DB. Typ parametru anotace by měl být `OutputBinding<T>`, kde `T` je nativní typ Java nebo POJO.

---

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     | – | Musí být nastaveno na `cosmosDB`.        |
|**direction**     | – | Musí být nastaveno na `in`.         |
|**name**     | – | Název parametru vazby, který představuje dokument ve funkci.  |
|**databaseName** |**DatabaseName** |Databáze obsahující dokumentu.        |
|**collectionName** |**collectionName** | Název, který bude obsahovat dokumentu. |
|**id**    | **ID** | ID dokumentu má načíst. Tato vlastnost podporuje [výrazy vazeb](./functions-bindings-expressions-patterns.md). Nenastavte `id` i vlastnosti **sqlQuery** . Pokud nemají nastavený buď jeden, načte celou kolekci. |
|**sqlQuery**  |**SqlQuery**  | Dotaz SQL služby Azure Cosmos DB používá k získávání více dokumentů. Vlastnost podporuje vazby modulu runtime, jako v následujícím příkladu: `SELECT * FROM c where c.departmentId = {departmentId}`. Nenastavte jak vlastnosti `id`, tak `sqlQuery`. Pokud nemají nastavený buď jeden, načte celou kolekci.|
|**connectionStringSetting**     |**connectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Azure Cosmos DB.        |
|**partitionKey**|**partitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může obsahovat parametry vazby. Je vyžadován pro vyhledávání v [dělených](../cosmos-db/partition-data.md#logical-partitions) kolekcích.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Po úspěšném ukončení funkce budou všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Po úspěšném ukončení funkce budou všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualizace nejsou automaticky provedeny při ukončení funkce. Místo toho použijte `context.bindings.<documentName>In` a `context.bindings.<documentName>Out` ke zpřístupnění aktualizací. Podívejte se na příklad JavaScriptu.

# <a name="pythontabpython"></a>[Python](#tab/python)

Data jsou k dispozici pro funkci prostřednictvím parametru `DocumentList`. Změny provedené v dokumentu nejsou automaticky uchovány.

# <a name="javatabjava"></a>[Java](#tab/java)

Z [knihovny runtime Functions jazyka Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)zpřístupňuje [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) anotace Cosmos DB data. Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>`.

---

## <a name="output"></a>Výstup

Azure Cosmos DB výstupní vazbu umožňuje zapsat nový dokument k databázi Azure Cosmos DB pomocí rozhraní SQL API.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, zápis jednoho dokumentu](#queue-trigger-write-one-doc-c)
* [Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, jeden zápis dokumentu

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) dokumentu, který přidá do databáze s využitím dat zadat zprávy z fronty úložiště.

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

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) kolekce dokumentů, který přidá do databáze s využitím dat podle zpráv fronty JSON.

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

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, zápis jednoho dokumentu](#queue-trigger-write-one-doc-c-script)
* [Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Aktivační událost fronty, jeden zápis dokumentu

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="output---attributes-and-annotations"></a>Výstup – atributy a poznámky

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

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

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Poznámka `CosmosDBOutput` je k dispozici pro zápis dat do Cosmos DB. Můžete použít poznámku na funkci nebo na konkrétní parametr funkce. Při použití v metodě Function je vrácená hodnota funkce ta, která je zapsána do Cosmos DB. Použijete-li anotaci s parametrem, typ parametru musí být deklarován jako `OutputBinding<T>`, kde `T` nativní typ Java nebo POJO.

---

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     | – | Musí být nastaveno na `cosmosDB`.        |
|**direction**     | – | Musí být nastaveno na `out`.         |
|**name**     | – | Název parametru vazby, který představuje dokument ve funkci.  |
|**databaseName** | **DatabaseName**|Databáze obsahující kolekci, do které se vytvoří dokument.     |
|**collectionName** |**collectionName**  | Název kolekce, ve kterém se vytvoří dokument. |
|**createIfNotExists**  |**createIfNotExists**    | Logická hodnota označující, zda kolekce je vytvořena při neexistuje. Výchozí hodnota je *false* protože nové kolekce se vytvoří s vyhrazenou propustností, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**partitionKey** |Pokud je `CreateIfNotExists` true, definuje cestu ke klíči oddílu pro vytvořenou kolekci.|
|**collectionThroughput**|**collectionThroughput**| Pokud je `CreateIfNotExists` true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
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

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální konfiguraci nastavení k dispozici pro tuto vazbu ve verzi 2.x. Další informace o globální nastavení konfigurace ve verzi 2.x, naleznete v tématu [referenční materiály k host.json pro Azure Functions verze 2.x](functions-host-json.md).

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
|GatewayMode|brána|Režim připojení, který funkce používá při připojování ke službě Azure Cosmos DB. Možnosti jsou `Direct` a `Gateway`|
|Protocol (Protokol)|Https|Protokol připojení, který funkce používá při připojení ke službě Azure Cosmos DB.  Přečtěte si [zde pro vysvětlení obou režimů](../cosmos-db/performance-tips.md#networking) .|
|leasePrefix|–|Předpona zapůjčení pro použití ve všech funkcích aplikace|

## <a name="next-steps"></a>Další kroky

* [Další informace o databáze bez serveru, výpočetního prostředí pomocí služby Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
