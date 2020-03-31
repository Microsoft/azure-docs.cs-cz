---
title: Aktivační událost Azure Cosmos DB pro funkce 2.x
description: Naučte se používat aktivační událost Azure Cosmos DB ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277567"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Aktivační událost Azure Cosmos DB pro funkce Azure 2.x

Azure Cosmos DB Trigger používá [kanál změn Azure Cosmos DB](../cosmos-db/change-feed.md) k naslouchání vkládání a aktualizacím napříč oddíly. Kanál změn publikuje vložení a aktualizace, nikoli odstranění.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která je vyvolána, když jsou vloží nebo aktualizace v zadané databázi a kolekci.

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

Následující příklad ukazuje vazby aktivační události Cosmos DB v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazby aktivační události Cosmos DB v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která používá vazbu. Funkce zapisuje zprávy protokolu při přidání nebo úpravě záznamů Cosmos DB.

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vazby aktivační události Cosmos DB v souboru *function.json* a [funkci Pythonu,](functions-reference-python.md) která používá vazbu. Funkce zapisuje zprávy protokolu při změně záznamů Cosmos DB.

Zde jsou data vazby v souboru *function.json:*

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

Zde je kód Pythonu:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Tato funkce je vyvolána, pokud jsou v zadané databázi a kolekci vloženy nebo aktualizace.

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


V [knihovně runtime funkcí Javy](/java/api/overview/azure/functions/runtime)použijte poznámku `@CosmosDBTrigger` k parametrům, jejichž hodnota by pocházela z Cosmos DB.  Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Konstruktor atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a dalších vlastnostech, které můžete konfigurovat, naleznete v tématu [Trigger - configuration](#configuration). Zde je `CosmosDBTrigger` příklad atributu v podpisu metody:

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

Úplný příklad naleznete v tématu [Trigger](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Z [knihovny runtime funkcí](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) `@CosmosDBInput` Java použijte poznámku k parametrům, které čtou data z Cosmos DB.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `CosmosDBTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `cosmosDBTrigger`nastavena na . |
|**direction** | neuvedeno | Musí být `in`nastavena na . Tento parametr se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje seznam dokumentů se změnami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, která obsahuje připojovací řetězec používaný k připojení k účtu Azure Cosmos DB, který se monitoruje. |
|**Databasename**|**DatabaseName**  | Název databáze Azure Cosmos DB s kolekcí, která je monitorována. |
|**název kolekce** |**CollectionName** | Název kolekce, která je sledována. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Nepovinné) Název nastavení aplikace, která obsahuje připojovací řetězec k účtu Azure Cosmos DB, který obsahuje kolekci zapůjčení. Není-li nastavena, použije se `connectionStringSetting` hodnota. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**Název_databáze Lease** | (Nepovinné) Název databáze, která obsahuje kolekci slouží k ukládání zapůjčení. Pokud není nastavena, `databaseName` použije se hodnota nastavení. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **Název kolekce lease** | (Nepovinné) Název kolekce slouží k ukládání zapůjčení. Není-li nastavena, použije se hodnota. `leases` |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Nepovinné) Pokud je `true`nastavena na , kolekce zapůjčení je automaticky vytvořena, pokud ještě neexistuje. Výchozí hodnota je `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Nepovinné) Definuje počet jednotek požadavků, které mají být přiřazeny při vytvoření kolekce zapůjčení. Toto nastavení se `createLeaseCollectionIfNotExists` používá `true`pouze v případě, že je nastaveno na . Tento parametr je automaticky nastaven při vytvoření vazby pomocí portálu.
|**leaseCollectionPrefix**| **Předpona kolekce zapůjčení**| (Nepovinné) Při nastavení je hodnota přidána jako předpona zapůjčených zapůjčených vytvořených v kolekci zapůjčení pro tuto funkci. Použití předpony umožňuje dvě samostatné funkce Azure sdílet stejnou kolekci Zapůjčení pomocí různých předponami.
|**feedPollDelay**| **FeedPollDelay**| (Nepovinné) Čas (v milisekundách) pro zpoždění mezi dotazování oddíl pro nové změny v kanálu, po vyčerpání všech aktuálních změn. Výchozí hodnota je 5 000 milisekund nebo 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nepovinné) Při nastavení definuje v milisekundách interval pro zahájení úlohy pro výpočet, pokud jsou oddíly rovnoměrně rozloženy mezi známé instance hostitele. Výchozí hodnota je 13000 (13 sekund).
|**leaseExpirationInterval**| **Interval vypršení platnosti zapůjčení**| (Nepovinné) Při nastavení definuje v milisekundách interval, pro který je zapůjčení přijato na zapůjčení představující oddíl. Pokud zapůjčení není obnovena v rámci tohoto intervalu, způsobí, že vyprší jeho platnost a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**leaseRenewInterval**| **Obnovit interval**| (Nepovinné) Při nastavení definuje v milisekundách interval obnovení pro všechny zapůjčené pro oddíly, které jsou aktuálně v držení instance. Výchozí hodnota je 17000 (17 sekund).
|**kontrolní bodFrekvence**| **Kontrolní frekvence**| (Nepovinné) Při nastavení definuje v milisekundách interval mezi kontrolními body zapůjčení. Výchozí hodnota je vždy po každém volání funkce.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Nepovinné) Pokud je tato vlastnost nastavena, nastaví maximální počet položek přijatých na volání funkce. Pokud operace ve sledované kolekci jsou prováděny prostřednictvím uložených procedur, [rozsah transakce](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) je zachována při čtení položek z kanálu změn. V důsledku toho počet přijatých položek může být vyšší než zadaná hodnota tak, aby položky změněné stejnou transakcí byly vráceny jako součást jedné atomické dávky.
|**startFromBeginning**| **StartFromBeginning**| (Nepovinné) Tato možnost sděluje Trigger číst změny od začátku historie změn kolekce namísto spuštění v aktuálním čase. Čtení od začátku funguje pouze při prvním spuštění triggeru, jako v následných spuštěních jsou kontrolní body již uloženy. Nastavení této `true` možnosti na v případě, že již byly vytvořeny zapůjčení, nemá žádný vliv. |
|**preferredLocations**| **Preferované umístění**| (Nepovinné) Definuje upřednostňovaná umístění (oblasti) pro geograficky replikované databázové účty ve službě Azure Cosmos DB. Hodnoty by měly být odděleny čárkami. Například "Východní USA, jižní střed USA, severní Evropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

Aktivační událost vyžaduje druhou kolekci, která používá k ukládání _zapůjčení_ přes oddíly. Kolekce, která je sledována, a kolekce, která obsahuje zapůjčení, musí být k dispozici pro aktivační událost.

>[!IMPORTANT]
> Pokud více funkcí jsou nakonfigurovány pro použití aktivační události Cosmos DB pro stejnou kolekci, každá z funkcí by měla používat vyhrazenou kolekci zapůjčení nebo zadat jinou `LeaseCollectionPrefix` pro každou funkci. V opačném případě bude spuštěna pouze jedna z funkcí. Informace o předponě naleznete v [části Konfigurace](#configuration).

Aktivační událost neoznačuje, zda byl dokument aktualizován nebo vložen, pouze poskytuje samotný dokument. Pokud potřebujete zpracovávat aktualizace a vloží jinak, můžete to provést implementací polí časového razítka pro vložení nebo aktualizaci.

## <a name="next-steps"></a>Další kroky

- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)