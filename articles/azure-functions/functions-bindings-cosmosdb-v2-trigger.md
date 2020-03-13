---
title: Aktivační událost Azure Cosmos DB pro Functions 2. x
description: Naučte se používat aktivační událost Azure Cosmos DB v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277567"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Aktivační událost Azure Cosmos DB pro Azure Functions 2. x

Aktivační událost Azure Cosmos DB používá [Azure Cosmos DB změnu kanálu](../cosmos-db/change-feed.md) k naslouchání vkládání a aktualizací v různých oddílech. Kanál změn publikuje vkládání a aktualizace, odstranění není.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

Tady je kód jazyka JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje Cosmos DB aktivační vazby v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

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

# <a name="java"></a>[Java](#tab/java)

Tato funkce je vyvolána, když jsou vloženy nebo aktualizovány v zadané databázi a kolekci.

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

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atributu má název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, najdete v tématu [Trigger-Configuration](#configuration). Tady je příklad atributu `CosmosDBTrigger` v signatuře metody:

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

Úplný příklad najdete v tématu [Trigger](#example).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Z [běhové knihovny Functions jazyka Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)použijte anotaci `@CosmosDBInput` u parametrů, které čtou data z Cosmos DB.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `CosmosDBTrigger`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavené na `cosmosDBTrigger`. |
|**direction** | neuvedeno | Musí být nastavené na `in`. Tento parametr je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název této proměnné v kódu funkce, která představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec použitý pro připojení k účtu Azure Cosmos DB, který je monitorován. |
|**Databáze**|**Databáze**  | Název databáze Azure Cosmos DB s kolekcí monitorovány. |
|**collectionName** |**CollectionName** | Název kolekce monitorovány. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Volitelné Název nastavení aplikace, které obsahuje připojovací řetězec k účtu Azure Cosmos DB, který obsahuje kolekci zapůjčení. Pokud není nastavena, je použita hodnota `connectionStringSetting`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Volitelné) Název databáze, která obsahuje kolekci pro ukládání zapůjčení. Pokud není nastavená, použije se hodnota nastavení `databaseName`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Volitelné) Název kolekce pro ukládání zapůjčení. Pokud není nastavena, je použita hodnota `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Volitelné Když se nastaví na `true`, kolekce zapůjčení se automaticky vytvoří, když už neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Volitelné Definuje počet jednotek žádostí, které se mají přiřadit při vytváření kolekce zapůjčení. Toto nastavení se používá pouze v případě, že je `createLeaseCollectionIfNotExists` nastaveno na hodnotu `true`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Volitelné Při nastavení se hodnota přidá jako předpona pro zapůjčení vytvořená v kolekci zapůjčení pro tuto funkci. Použití předpony umožňuje dvě samostatné Azure Functions sdílet stejnou kolekci zapůjčení s použitím různých předpon.
|**feedPollDelay**| **FeedPollDelay**| Volitelné Doba (v milisekundách), která se má zpozdit při cyklickém dotazování oddílu na nové změny v informačním kanálu po vyprázdnění všech aktuálních změn. Výchozí hodnota je 5 000 milisekund nebo 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval aktivovala úloha Vypočítat, pokud oddíly jsou rovnoměrně mezi známými hostiteli instance. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, pro kterou je zapůjčení pořízené zapůjčení představující oddílu. Pokud v rámci tohoto intervalu nedojde k jeho prodloužení zapůjčení, způsobí vypršení platnosti a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval obnovení pro všechny zapůjčení pro oddíly právě načtený v instanci. Výchozí hodnota je 17000 (17 sekund).
|**checkpointFrequency**| **CheckpointFrequency**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval mezi zapůjčení kontrolní body. Výchozí hodnota je vždy po volání funkce.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Volitelné Při nastavení tato vlastnost nastaví maximální počet položek přijatých na volání funkce. Pokud se operace v monitorované kolekci provádějí prostřednictvím uložených procedur, [obor transakce](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) se zachová při čtení položek z kanálu změn. V důsledku toho může být počet přijatých položek vyšší, než je zadaná hodnota, aby byly položky změněné stejnou transakcí vráceny jako součást jedné atomická dávky.
|**startFromBeginning**| **StartFromBeginning**| Volitelné Tato možnost oznamuje triggeru, aby přečetl změny ze začátku historie změn kolekce místo začátku v aktuálním čase. Čtení od začátku funguje jenom při prvním spuštění triggeru, stejně jako v dalších spuštěních, kontrolní body už jsou uložené. Nastavení této možnosti na `true`, pokud jsou již vytvořeny zapůjčené adresy, nemá žádný vliv. |
|**preferredLocations**| **PreferredLocations**| Volitelné Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být oddělené čárkami. Například "Východní USA, Střed USA – jih, Severní Evropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

Trigger vyžaduje druhou kolekci, kterou používá k ukládání _zapůjčení_ do oddílů. Kolekce, monitoruje a kolekci, která obsahuje zapůjčení musí být k dispozici pro aktivační událost pro práci.

>[!IMPORTANT]
> Pokud je pro stejnou kolekci nakonfigurované víc funkcí Cosmos DB triggeru, každá z těchto funkcí by měla používat vyhrazenou kolekci zapůjčení nebo pro každou funkci zadat jiný `LeaseCollectionPrefix`. V opačném případě se aktivuje jenom jedna z funkcí. Informace o předponě najdete v [části věnované konfiguraci](#configuration).

Aktivační událost neukazuje, jestli dokument byl aktualizaci nebo vložení, stačí poskytuje samotný dokument. Pokud budete potřebovat pro zpracování aktualizací a vloží odlišně, můžete to udělat pomocí implementace pole časového razítka pro vložení nebo aktualizace.

## <a name="next-steps"></a>Další kroky

- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)