---
title: Azure Cosmos DB vazby pro Functions 2. x
description: Naučte se používat Azure Cosmos DB triggery a vazby v Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funkce Azure Functions, Functions, zpracování událostí, dynamická výpočetní funkce a architektura bez serveru
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 081a0e9ac165fdee2426780be6d1440cf8d4fcc0
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904003"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB vazby pro Azure Functions 2. x

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-bindings-cosmosdb.md)
> * [Verze 2](functions-bindings-cosmosdb-v2.md)

Tento článek vysvětluje, jak pracovat s [Azure Cosmos DBmi](../cosmos-db/serverless-computing-database.md) vazbami v Azure Functions 2. x. Azure Functions podporuje vazby triggeru, vstupu a výstupu pro Azure Cosmos DB.

> [!NOTE]
> Tento článek je určen pro [Azure Functions verze 2. x](functions-versions.md).  Informace o tom, jak tyto vazby použít ve funkcích 1. x, najdete v tématu [Azure Cosmos DB vazby pro Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Tato vazba se původně jmenovala jako DocumentDB. Ve funkcích Functions verze 2. x jsou triggery, vazby a balíčky všechny pojmenované Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Podporovaná rozhraní API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2. x

Vazby Azure Cosmos DB pro funkce verze 2. x jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , verze 3. x. Zdrojový kód pro vazby je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Aktivační událost Azure Cosmos DB používá [Azure Cosmos DB změnu kanálu](../cosmos-db/change-feed.md) k naslouchání vkládání a aktualizací v různých oddílech. Kanál změn publikuje vkládání a aktualizace, nikoli odstranění.

## <a name="trigger---example"></a>Aktivační procedura – příklad

Podívejte se na příklad konkrétního jazyka:

* [C#](#trigger---c-example)
* [C#skript (. csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Přeskočit příklady triggerů

### <a name="trigger---c-example"></a>Aktivační procedura C# – příklad

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která je vyvolána při vložení nebo aktualizaci v zadané databázi a kolekci.

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

Přeskočit příklady triggerů

### <a name="trigger---c-script-example"></a>Aktivační procedura C# – příklad skriptu

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně Cosmos DBch záznamů.

Tady jsou data vazby v souboru *Function. JSON* :

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

Tady je kód C# skriptu:

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

Přeskočit příklady triggerů

### <a name="trigger---javascript-example"></a>Trigger – příklad JavaScriptu

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně Cosmos DBch záznamů.

Tady jsou data vazby v souboru *Function. JSON* :

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

### <a name="trigger---java-example"></a>Trigger – příklad Java

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci jazyka Java](functions-reference-java.md) , která používá vazbu. Funkce je zahrnuta v případě, že se v zadané databázi a kolekci nacházejí vložené nebo aktualizované databáze.

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


V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBTrigger` anotaci u parametrů, jejichž hodnota pochází z Cosmos DB.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí volitelných >\<T.


Přeskočit příklady triggerů

### <a name="trigger---python-example"></a>Trigger – příklad Pythonu

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně Cosmos DBch záznamů.

Tady jsou data vazby v souboru *Function. JSON* :

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

## <a name="trigger---c-attributes"></a>Aktivační C# atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, najdete v tématu [Trigger-Configuration](#trigger---configuration). Tady je příklad atributu `CosmosDBTrigger` v signatuře metody:

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

Úplný příklad najdete v tématu [Trigger – C# příklad](#trigger---c-example).


## <a name="trigger---configuration"></a>Aktivační událost – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `CosmosDBTrigger`.

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** || Musí být nastavené na `cosmosDBTrigger`. |
|**direction** || Musí být nastavené na `in`. Tento parametr se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** || Název proměnné použitý v kódu funkce, který představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec, který se používá pro připojení k monitorovanému účtu Azure Cosmos DB. |
|**Databáze**|**Databáze**  | Název databáze Azure Cosmos DB s monitorovanou kolekcí. |
|**collectionName** |**CollectionName** | Název monitorované kolekce. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Volitelné Název nastavení aplikace, které obsahuje připojovací řetězec ke službě, která obsahuje kolekci zapůjčení. Pokud není nastavena, je použita hodnota `connectionStringSetting`. Tento parametr se automaticky nastaví při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení adres musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Volitelné Název databáze, která obsahuje kolekci používanou k uložení zapůjčení. Pokud není nastavená, použije se hodnota nastavení `databaseName`. Tento parametr se automaticky nastaví při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | Volitelné Název kolekce, která se používá k uložení zapůjčení Pokud není nastavena, je použita hodnota `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Volitelné Když se nastaví na `true`, kolekce zapůjčení se automaticky vytvoří, když už neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Volitelné Definuje množství jednotek požadavků, které se mají přiřadit při vytváření kolekce zapůjčení. Toto nastavení se používá pouze v případě, že je `createLeaseCollectionIfNotExists` nastaveno na hodnotu `true`. Tento parametr se automaticky nastaví, když se vytvoří vazba pomocí portálu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Volitelné Pokud je tato funkce nastavena, přidá předponu k zapůjčením vytvořeným v kolekci zapůjčení pro tuto funkci a efektivně umožní dvěma samostatným Azure Functions sdílet stejnou kolekci zapůjčení s použitím různých předpon.
|**feedPollDelay**| **FeedPollDelay**| Volitelné Při nastavení definuje v milisekundách zpoždění při cyklickém dotazování oddílu na nové změny v informačním kanálu po vyprázdnění všech aktuálních změn. Výchozí hodnota je 5000 (5 sekund).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Volitelné Když je tato možnost nastavená, definuje v milisekundách interval, po který se aktivuje úloha k výpočtu, pokud se oddíly rozdělují rovnoměrně mezi známé instance hostitele. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Volitelné Když je nastaveno, definuje v milisekundách interval, po který se zapůjčení zapůjčení zastupuje jako oddíl. Pokud se zapůjčení v rámci tohoto intervalu prodlouží, způsobí to vypršení platnosti a vlastnictví oddílu se přesune na jinou instanci. Výchozí hodnota je 60000 (60 s).
|**leaseRenewInterval**| **LeaseRenewInterval**| Volitelné Při nastavení definuje interval obnovování všech zapůjčení pro oddíly aktuálně držené instancí v milisekundách. Výchozí hodnota je 17000 (17 sekund).
|**checkpointFrequency**| **CheckpointFrequency**| Volitelné Při nastavení definuje interval mezi kontrolními body zapůjčení (v milisekundách). Výchozí hodnota je vždy po volání funkce.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Volitelné Při nastavení tato vlastnost nastaví maximální množství položek přijatých na volání funkce. Pokud se operace v monitorované kolekci provádějí prostřednictvím uložených procedur, [obor transakce](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) se zachová při čtení položek z kanálu změn. V důsledku toho je možné, že množství položek, které byly přijaty, musí být vyšší než zadaná hodnota, aby byly položky změněné stejnou transakcí vráceny jako součást jedné atomická dávky.
|**startFromBeginning**| **StartFromBeginning**| Volitelné Když se nastaví, sdělí triggeru, aby začal číst změny od začátku historie kolekce namísto aktuálního času. To funguje jenom při prvním spuštění triggeru, stejně jako v dalších spuštěních, kontrolní body už jsou uložené. Tato možnost se nastaví na `true`, pokud jsou již vytvořené zapůjčené adresy nijak ovlivněny.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost – využití

Trigger vyžaduje druhou kolekci, kterou používá k ukládání _zapůjčení_ do oddílů. Aby aktivační událost fungovala, musí být kolekce monitorovaná a kolekce obsahující zapůjčení.

>[!IMPORTANT]
> Pokud je pro stejnou kolekci nakonfigurované víc funkcí Cosmos DB triggeru, každá z těchto funkcí by měla používat vyhrazenou kolekci zapůjčení nebo pro každou funkci zadat jiný `LeaseCollectionPrefix`. V opačném případě se aktivuje jenom jedna z funkcí. Informace o předponě najdete v [části věnované konfiguraci](#trigger---configuration).

Aktivační událost neindikuje, jestli se dokument aktualizoval nebo vložil, jenom samotný dokument poskytuje. Pokud potřebujete zpracovávat aktualizace a vkládat je odlišně, můžete to provést implementací polí časového razítka pro vložení nebo aktualizaci.

## <a name="input"></a>Vstup

Vstupní vazba Azure Cosmos DB používá rozhraní SQL API k načtení jednoho nebo více dokumentů Azure Cosmos DB a předá je vstupnímu parametru funkce. V závislosti na triggeru, který funkci vyvolá, lze určit ID dokumentu nebo parametry dotazu.

> [!NOTE]
> Pokud je kolekce [rozdělená na oddíly](../cosmos-db/partition-data.md#logical-partitions), operace vyhledávání musí také určovat hodnotu klíče oddílu.
>

## <a name="input---examples"></a>Vstupní příklady

V příkladech specifických pro konkrétní jazyk si přečtěte jeden dokument zadáním hodnoty ID:

* [C#](#input---c-examples)
* [C#skript (. csx)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Přeskočit příklady vstupu](#input---attributes)

### <a name="input---c-examples"></a>Vstupní C# příklady

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-c)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-c)
* [Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Trigger HTTP, získání více dokumentů pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Trigger HTTP, získání více dokumentů pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Příklady odkazují na jednoduchý `ToDoItem` typ:

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Aktivační událost fronty, vyhledání ID z formátu JSONC#()

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována zprávou fronty, která obsahuje objekt JSON. Aktivační událost fronty analyzuje JSON na objekt s názvem `ToDoItemLookup`, který obsahuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu (C#)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Aktivační událost HTTP, vyhledání ID z dat trasy (C#)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery (C#)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení ID, které se má vyhledat. Toto ID se používá k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>Trigger HTTP, získání více dokumentů s použitím SqlQuery (C#)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Dotaz je zadán ve vlastnosti atributu `SqlQuery`.

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Trigger HTTP, získání více dokumentů s použitím DocumentClient (C#)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Kód používá instanci `DocumentClient` poskytnutou vazbou Azure Cosmos DB pro čtení seznamu dokumentů. Instanci `DocumentClient` lze také použít pro operace zápisu.

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

[Přeskočit příklady vstupu](#input---attributes)

### <a name="input---c-script-examples"></a>Příklady C# skriptu Input

Tato část obsahuje následující příklady:

* [Aktivační událost fronty, vyhledání ID z řetězce](#queue-trigger-look-up-id-from-string-c-script)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-c-script)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Trigger HTTP, získání více dokumentů pomocí SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Trigger HTTP, získání více dokumentů pomocí DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Příklady triggeru HTTP odkazují na jednoduchý typ `ToDoItem`:

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Aktivační událost fronty, vyhledání ID z řetězce (C# skript)

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v souboru *Function. JSON* :

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
Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód C# skriptu:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Aktivační událost fronty, získání více dokumentů s použitím SqlQueryC# (skript)

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí triggeru fronty pro přizpůsobení parametrů dotazu.

Aktivační událost fronty poskytuje parametr `departmentId`. Zpráva fronty `{ "departmentId" : "Finance" }` by vrátila všechny záznamy finančního oddělení.

Tady jsou data vazby v souboru *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód C# skriptu:

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Aktivační událost HTTP, vyhledání ID z řetězce dotazu (C# skript)

Následující příklad ukazuje [ C# funkci skriptu](functions-reference-csharp.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je soubor *Function. JSON* :

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

Tady je kód C# skriptu:

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Aktivační událost HTTP, vyhledání ID z dat trasy (C# skript)

Následující příklad ukazuje [ C# funkci skriptu](functions-reference-csharp.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který používá data směrování k určení hodnoty ID a klíče oddílu k vyhledání. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je soubor *Function. JSON* :

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

Tady je kód C# skriptu:

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Trigger HTTP, získání více dokumentů s použitím SqlQuery (C# skript)

Následující příklad ukazuje [ C# funkci skriptu](functions-reference-csharp.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Dotaz je zadán ve vlastnosti atributu `SqlQuery`.

Tady je soubor *Function. JSON* :

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

Tady je kód C# skriptu:

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>Trigger HTTP, získání více dokumentů s použitím DocumentClient (C# skript)

Následující příklad ukazuje [ C# funkci skriptu](functions-reference-csharp.md) , která načte seznam dokumentů. Funkce je aktivována požadavkem HTTP. Kód používá instanci `DocumentClient` poskytnutou vazbou Azure Cosmos DB pro čtení seznamu dokumentů. Instanci `DocumentClient` lze také použít pro operace zápisu.

Tady je soubor *Function. JSON* :

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

Tady je kód C# skriptu:

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

[Přeskočit příklady vstupu](#input---attributes)

### <a name="input---javascript-examples"></a>Input – příklady JavaScriptu

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-javascript)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Aktivační událost fronty, vyhledání ID z formátu JSON (JavaScript)

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v souboru *Function. JSON* :

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Trigger HTTP, vyhledání ID z řetězce dotazu (JavaScript)

Následující příklad ukazuje [funkci JavaScriptu](functions-reference-node.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je soubor *Function. JSON* :

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Aktivační událost HTTP, vyhledání ID z dat trasy (JavaScript)

Následující příklad ukazuje [funkci JavaScriptu](functions-reference-node.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je soubor *Function. JSON* :

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Aktivační událost fronty, získání více dokumentů s použitím SqlQuery (JavaScript)

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí triggeru fronty pro přizpůsobení parametrů dotazu.

Aktivační událost fronty poskytuje parametr `departmentId`. Zpráva fronty `{ "departmentId" : "Finance" }` by vrátila všechny záznamy finančního oddělení.

Tady jsou data vazby v souboru *Function. JSON* :

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

[Přeskočit příklady vstupu](#input---attributes)

### <a name="input---python-examples"></a>Input – příklady Pythonu

Tato část obsahuje následující příklady, které čtou jeden dokument zadáním hodnoty ID z různých zdrojů:

* [Aktivační událost fronty, vyhledání ID z formátu JSON](#queue-trigger-look-up-id-from-json-python)
* [Aktivační událost HTTP, vyhledání ID z řetězce dotazu](#http-trigger-look-up-id-from-query-string-python)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-python)
* [Aktivační událost fronty, získání více dokumentů pomocí SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Aktivační událost fronty, vyhledání ID z formátu JSON (Python)

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v souboru *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód Pythonu:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>Trigger HTTP, vyhledání ID z řetězce dotazu (Python)

Následující příklad ukazuje funkci jazyka [Python](functions-reference-python.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je soubor *Function. JSON* :

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>Trigger HTTP, vyhledání ID z dat trasy (Python)

Následující příklad ukazuje funkci jazyka [Python](functions-reference-python.md) , která načte jeden dokument. Funkce je aktivována požadavkem HTTP, který pomocí řetězce dotazu určuje ID a hodnotu klíče oddílu, které chcete vyhledat. IDENTIFIKÁTOR a hodnota klíče oddílu slouží k načtení `ToDoItem` dokumentu ze zadané databáze a kolekce.

Tady je soubor *Function. JSON* :

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

[Přeskočit příklady vstupu](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Aktivační událost fronty, získání více dokumentů s použitím SqlQuery (Python)

Následující příklad ukazuje vstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce načte více dokumentů určených dotazem SQL pomocí triggeru fronty pro přizpůsobení parametrů dotazu.

Aktivační událost fronty poskytuje parametr `departmentId`. Zpráva fronty `{ "departmentId" : "Finance" }` by vrátila všechny záznamy finančního oddělení.

Tady jsou data vazby v souboru *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód Pythonu:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Přeskočit příklady vstupu](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Vstupní F# příklady

Následující příklad ukazuje vstupní vazbu Cosmos DB v souboru *Function. JSON* a [ F# funkci](functions-reference-fsharp.md) , která používá vazbu. Funkce přečte jeden dokument a aktualizuje textovou hodnotu dokumentu.

Tady jsou data vazby v souboru *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Zde je F# kód:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Tento příklad vyžaduje soubor `project.json`, který určuje `FSharp.Interop.Dynamic` a `Dynamitey` závislosti NuGet:

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

Chcete-li přidat soubor `project.json`, přečtěte si téma [ F# Správa balíčků](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Input – příklady jazyka Java

Tato část obsahuje následující příklady:

* [Trigger HTTP, vyhledání ID z řetězce dotazu – parametr řetězce](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Trigger HTTP, vyhledání ID z řetězce dotazu – parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Aktivační událost HTTP, vyhledání ID z dat trasy](#http-trigger-look-up-id-from-route-data-java)
* [Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Aktivační událost HTTP, získání více dokumentů z dat směrování pomocí SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Příklady odkazují na jednoduchý `ToDoItem` typ:

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>Trigger HTTP, vyhledání ID z řetězce dotazu – parametr řetězce (Java)

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

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte anotaci `@CosmosDBInput` u parametrů funkce, jejichž hodnota pochází z Cosmos DB.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí volitelných >\<T.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>Trigger HTTP, vyhledání ID z řetězce dotazu – parametr POJO (Java)

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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>Aktivační událost HTTP, vyhledání ID z dat trasy (Java)

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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>Aktivační událost HTTP, vyhledání ID z dat trasy, použití SqlQuery (Java)

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>Aktivační událost HTTP, získání více dokumentů z dat směrování pomocí SqlQuery (Java)

Následující příklad ukazuje funkci jazyka Java, která obsahuje více dokumentů. Funkce se aktivuje požadavkem HTTP, který používá parametr trasy ```desc``` k určení řetězce, který se má vyhledat v poli ```description```. Hledaný termín se používá k načtení kolekce dokumentů ze zadané databáze a kolekce, převodu sady výsledků na ```ToDoItem[]``` a jejím předání jako argumentu funkce.

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

## <a name="input---attributes"></a>Vstupní atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete nakonfigurovat, najdete v [následující části Konfigurace](#input---configuration).

## <a name="input---configuration"></a>Vstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `CosmosDB`.

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || Musí být nastavené na `cosmosDB`.        |
|**direction**     || Musí být nastavené na `in`.         |
|**Jméno**     || Název parametru vazby, který představuje dokument ve funkci.  |
|**Databáze** |**Databáze** |Databáze obsahující dokument.        |
|**collectionName** |**CollectionName** | Název kolekce, která obsahuje dokument. |
|**účet**    | **ID** | ID dokumentu, který se má načíst Tato vlastnost podporuje [výrazy vazby](./functions-bindings-expressions-patterns.md). Nenastavte vlastnosti **ID** i **sqlQuery** . Pokud nenastavíte žádné nastavení, načte se celá kolekce. |
|**sqlQuery**  |**SqlQuery**  | Azure Cosmos DB dotaz SQL, který se používá k načítání více dokumentů. Vlastnost podporuje vazby za běhu, jako v tomto příkladu: `SELECT * FROM c where c.departmentId = {departmentId}`. Nenastavte vlastnosti **ID** i **sqlQuery** . Pokud nenastavíte žádné nastavení, načte se celá kolekce.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Určuje hodnotu klíče oddílu pro vyhledávání. Může zahrnovat parametry vazby. Je vyžadován pro vyhledávání v [dělených](../cosmos-db/partition-data.md#logical-partitions) kolekcích.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Vstup – použití

V C# funkce F# a jsou po úspěšném ukončení funkce všechny změny provedené v vstupním dokumentu pomocí pojmenovaných vstupních parametrů automaticky trvalé.

Ve funkcích JavaScriptu nejsou aktualizace provedeny automaticky při ukončení funkce. Místo toho proveďte aktualizace pomocí `context.bindings.<documentName>In` a `context.bindings.<documentName>Out`. Podívejte se na příklad JavaScriptu.

## <a name="output"></a>Výstup

Azure Cosmos DB výstupní vazba umožňuje napsat nový dokument do databáze Azure Cosmos DB pomocí rozhraní SQL API.

## <a name="output---examples"></a>Výstup – příklady

Podívejte se na příklady specifické pro jazyk:

* [C#](#output---c-examples)
* [C#skript (. csx)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Viz také [vstupní příklad](#input---c-examples) , který používá `DocumentClient`.

[Příklady přeskočení výstupu](#output---attributes)

### <a name="output---c-examples"></a>Výstup – C# příklady

Tato část obsahuje následující příklady:

* Aktivační událost fronty, zápis jednoho dokumentu
* Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector

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

[Příklady přeskočení výstupu](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Aktivační událost fronty, zápis jednoho dokumentuC#()

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

[Příklady přeskočení výstupu](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollectorC#()

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

[Příklady přeskočení výstupu](#output---attributes)

### <a name="output---c-script-examples"></a>Příklady výstupních C# skriptů

Tato část obsahuje následující příklady:

* Aktivační událost fronty, zápis jednoho dokumentu
* Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector

[Příklady přeskočení výstupu](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Aktivační událost fronty, zápis jednoho dokumentuC# (skriptu)

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód C# skriptu:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Aktivační událost fronty, zápis dokumentů pomocí IAsyncCollector

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

Tady je soubor Function. JSON:

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

Tady je kód C# skriptu:

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

[Příklady přeskočení výstupu](#output---attributes)

### <a name="output---javascript-examples"></a>Výstup – příklady JavaScriptu

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

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

[Příklady přeskočení výstupu](#output---attributes)

### <a name="output---f-examples"></a>Výstup – F# příklady

Následující příklad ukazuje výstupní vazbu Azure Cosmos DB v souboru *Function. JSON* a [ F# funkci](functions-reference-fsharp.md) , která používá vazbu. Funkce používá vstupní vazbu fronty pro frontu, která přijímá JSON v následujícím formátu:

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
Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Zde je F# kód:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Tento příklad vyžaduje soubor `project.json`, který určuje `FSharp.Interop.Dynamic` a `Dynamitey` závislosti NuGet:

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

Chcete-li přidat soubor `project.json`, přečtěte si téma [ F# Správa balíčků](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Výstup – příklady Java

* [Aktivační událost fronty, uložení zprávy do databáze prostřednictvím návratové hodnoty](#queue-trigger-save-message-to-database-via-return-value-java)
* [Aktivační událost HTTP – uložte jeden dokument do databáze prostřednictvím návratové hodnoty.](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Aktivační procedura HTTP – uložení jednoho dokumentu do databáze prostřednictvím OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Aktivační událost HTTP, uložení více dokumentů do databáze prostřednictvím OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Aktivační událost fronty – uložení zprávy do databáze prostřednictvím návratové hodnoty (Java)

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>Trigger HTTP – uložení jednoho dokumentu do databáze prostřednictvím návratové hodnoty (Java)

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

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>Trigger HTTP – uložení jednoho dokumentu do databáze prostřednictvím OutputBinding (Java)

Následující příklad ukazuje funkci jazyka Java, která zapisuje dokument do CosmosDB prostřednictvím výstupní parametr ```OutputBinding<T>```. Všimněte si, že v této instalaci se jedná o parametr ```outputItem```, který je nutné opatřit poznámkou ```@CosmosDBOutput```, nikoli signaturou funkce. Použití ```OutputBinding<T>``` umožňuje funkci využít vazby k zápisu dokumentu do CosmosDB a současně umožňuje vracet jinou hodnotu volajícímu funkci, jako je JSON nebo dokument XML.

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

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>Aktivační událost HTTP, uložení více dokumentů do databáze prostřednictvím OutputBinding (Java)

Následující příklad ukazuje funkci jazyka Java, která zapisuje více dokumentů do CosmosDB prostřednictvím výstupní parametr ```OutputBinding<T>```. Všimněte si, že v této instalaci se jedná o parametr ```outputItem```, který je nutné opatřit poznámkou ```@CosmosDBOutput```, nikoli signaturou funkce. Výstupní parametr ```outputItem``` obsahuje seznam ```ToDoItem``` objektů jako typ parametru šablony. Použití ```OutputBinding<T>``` umožňuje funkci využít vazby k zápisu dokumentů do CosmosDB a zároveň umožňuje vracet jiné hodnoty volajícímu funkci, jako je JSON nebo dokument XML.

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

### <a name="output---python-examples"></a>Výstup – příklady Pythonu

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

## <a name="output---attributes"></a>Výstupní atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které lze konfigurovat, naleznete v tématu [Output-Configuration](#output---configuration). Tady je příklad atributu `CosmosDB` v signatuře metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Úplný příklad naleznete v tématu Output- C# example.

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `CosmosDB`.

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || Musí být nastavené na `cosmosDB`.        |
|**direction**     || Musí být nastavené na `out`.         |
|**Jméno**     || Název parametru vazby, který představuje dokument ve funkci.  |
|**Databáze** | **Databáze**|Databáze obsahující kolekci, ve které je dokument vytvořen.     |
|**collectionName** |**CollectionName**  | Název kolekce, ve které je dokument vytvořen. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logická hodnota určující, zda je kolekce vytvořena, pokud neexistuje. Výchozí hodnota je *false* , protože nové kolekce jsou vytvořeny s rezervovanou propustností, která má vliv na náklady. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Pokud je `CreateIfNotExists` true, definuje cestu ke klíči oddílu pro vytvořenou kolekci.|
|**collectionThroughput**|**CollectionThroughput**| Pokud je `CreateIfNotExists` true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Název nastavení aplikace, které obsahuje připojovací řetězec Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Ve výchozím nastavení platí, že při zápisu do výstupního parametru ve funkci je v databázi vytvořen dokument. Tento dokument má jako ID dokumentu automaticky generovaný identifikátor GUID. Můžete zadat ID dokumentu výstupního dokumentu zadáním vlastnosti `id` do objektu JSON předaného do výstupního parametru.

> [!Note]
> Když zadáte ID existujícího dokumentu, přepíše ho nový výstupní dokument.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Ovládacího | Referenční informace |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení Host. JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzi 2. x. Další informace o globálních nastaveních konfigurace verze 2. x naleznete v tématu [reference Host. JSON pro Azure Functions verze 2. x](functions-host-json.md).

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
|Protocol (Protokol)|Https|Protokol připojení, který funkce používá při připojení ke službě Azure Cosmos DB.  Přečtěte si [zde pro vysvětlení obou režimů](../cosmos-db/performance-tips.md#networking) .|
|leasePrefix|neuvedeno|Předpona zapůjčení pro použití ve všech funkcích aplikace|

## <a name="next-steps"></a>Další kroky

* [Další informace o výpočetních databázích bez serveru s Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
