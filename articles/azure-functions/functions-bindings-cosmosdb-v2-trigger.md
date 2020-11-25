---
title: Aktivační událost Azure Cosmos DB pro Functions 2. x a vyšší
description: Naučte se používat aktivační událost Azure Cosmos DB v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: e845efa2c1df47c80fcc10e7fb758f05af9fbecc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002132"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Aktivační událost Azure Cosmos DB pro Azure Functions 2. x a vyšší

Aktivační událost Azure Cosmos DB používá [Azure Cosmos DB změnu kanálu](../cosmos-db/change-feed.md) k naslouchání vkládání a aktualizací v různých oddílech. Kanál změn publikuje vkládání a aktualizace, nikoli odstranění.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která je vyvolána při vložení nebo aktualizaci v zadané databázi a kolekci.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje Cosmos DB aktivační vazby v *function.js* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

Následující příklad ukazuje Cosmos DB aktivační vazby v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje Cosmos DB aktivační vazby v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce zapisuje zprávy protokolu při změně Cosmos DBch záznamů.

Tady jsou data vazby v *function.js* souboru:

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


V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBTrigger` anotaci pro parametry, jejichž hodnota by pocházela z Cosmos DB.  Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, najdete v tématu [Trigger-Configuration](#configuration). Tady je `CosmosDBTrigger` příklad atributu v signatuře metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Úplný příklad najdete v tématu [Trigger](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Z [běhové knihovny Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@CosmosDBInput` anotaci pro parametry, které čtou data z Cosmos DB.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `CosmosDBTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | neuvedeno | Musí být nastaven na hodnotu `cosmosDBTrigger` . |
|**směr** | neuvedeno | Musí být nastaven na hodnotu `in` . Tento parametr se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec, který se používá pro připojení k monitorovanému účtu Azure Cosmos DB. |
|**Databáze**|**DatabaseName**  | Název databáze Azure Cosmos DB s monitorovanou kolekcí. |
|**collectionName** |**Název kolekce** | Název monitorované kolekce. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Volitelné Název nastavení aplikace, které obsahuje připojovací řetězec k účtu Azure Cosmos DB, který obsahuje kolekci zapůjčení. Pokud není nastavena, `connectionStringSetting` je použita hodnota. Tento parametr se automaticky nastaví při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení adres musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Volitelné Název databáze, která obsahuje kolekci používanou k uložení zapůjčení. Pokud není nastavená, použije se hodnota `databaseName` nastavení. Tento parametr se automaticky nastaví při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | Volitelné Název kolekce, která se používá k uložení zapůjčení Pokud není nastavena, `leases` je použita hodnota. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Volitelné Když se nastaví na `true` , kolekce zapůjčení se automaticky vytvoří, když už neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Volitelné Definuje počet jednotek žádostí, které se mají přiřadit při vytváření kolekce zapůjčení. Toto nastavení se používá pouze v případě `createLeaseCollectionIfNotExists` , že je parametr nastaven na hodnotu `true` . Tento parametr se automaticky nastaví, když se vytvoří vazba pomocí portálu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Volitelné Při nastavení se hodnota přidá jako předpona pro zapůjčení vytvořená v kolekci zapůjčení pro tuto funkci. Použití předpony umožňuje dvě samostatné Azure Functions sdílet stejnou kolekci zapůjčení s použitím různých předpon.
|**feedPollDelay**| **FeedPollDelay**| Volitelné Doba (v milisekundách), která se má zpozdit při cyklickém dotazování oddílu na nové změny v informačním kanálu po vyprázdnění všech aktuálních změn. Výchozí hodnota je 5 000 milisekund nebo 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Volitelné Když je tato možnost nastavená, definuje v milisekundách interval, po který se aktivuje úloha k výpočtu, pokud se oddíly rozdělují rovnoměrně mezi známé instance hostitele. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Volitelné Když je nastaveno, definuje v milisekundách interval, po který se zapůjčení zapůjčení zastupuje jako oddíl. Pokud se zapůjčení v rámci tohoto intervalu prodlouží, způsobí to vypršení platnosti a vlastnictví oddílu se přesune na jinou instanci. Výchozí hodnota je 60000 (60 s).
|**leaseRenewInterval**| **LeaseRenewInterval**| Volitelné Při nastavení definuje interval obnovování všech zapůjčení pro oddíly aktuálně držené instancí v milisekundách. Výchozí hodnota je 17000 (17 sekund).
|**checkpointFrequency**| **CheckpointFrequency**| Volitelné Při nastavení definuje interval mezi kontrolními body zapůjčení (v milisekundách). Výchozí hodnota je vždy po volání funkce.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Volitelné Při nastavení tato vlastnost nastaví maximální počet položek přijatých na volání funkce. Pokud se operace v monitorované kolekci provádějí prostřednictvím uložených procedur, [obor transakce](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) se zachová při čtení položek z kanálu změn. V důsledku toho může být počet přijatých položek vyšší, než je zadaná hodnota, aby byly položky změněné stejnou transakcí vráceny jako součást jedné atomická dávky.
|**startFromBeginning**| **StartFromBeginning**| Volitelné Tato možnost oznamuje triggeru, aby přečetl změny ze začátku historie změn kolekce místo začátku v aktuálním čase. Čtení od začátku funguje jenom při prvním spuštění triggeru, stejně jako v dalších spuštěních, kontrolní body už jsou uložené. Nastavení této možnosti na, `true` Pokud jsou již vytvořené zapůjčené adresy nijak ovlivněny. |
|**preferredLocations**| **PreferredLocations**| Volitelné Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být oddělené čárkami. Například "Východní USA, Střed USA – jih, Severní Evropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

Trigger vyžaduje druhou kolekci, kterou používá k ukládání _zapůjčení_ do oddílů. Aby aktivační událost fungovala, musí být kolekce monitorovaná a kolekce obsahující zapůjčení.

>[!IMPORTANT]
> Pokud je pro stejnou kolekci nakonfigurované víc funkcí Cosmos DB triggeru, každá z těchto funkcí by měla používat vyhrazenou kolekci zapůjčení nebo `LeaseCollectionPrefix` pro každou funkci zadat jinou. V opačném případě se aktivuje jenom jedna z funkcí. Informace o předponě najdete v [části věnované konfiguraci](#configuration).

Aktivační událost neindikuje, jestli se dokument aktualizoval nebo vložil, jenom samotný dokument poskytuje. Pokud potřebujete zpracovávat aktualizace a vkládat je odlišně, můžete to provést implementací polí časového razítka pro vložení nebo aktualizaci.

## <a name="next-steps"></a>Další kroky

- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)
