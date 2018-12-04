---
title: Použití knihovny BulkExecutor .NET pro grafy k provádění hromadných operací v rozhraní Gremlin API služby Azure Cosmos DB
description: Zjistěte, jak pomocí knihovny BulkExecutor hromadně importovat data grafu do kontejneru rozhraní Gremlin API služby Azure Cosmos DB.
services: cosmos-db
keywords: graph, gremlin, bulk, bulkexecutor, migration, data, cosmosdb, cosmos, database, import
author: luisbosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: d5a25bd7cab68f77a37b14ba41bf3cc832c2125f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836708"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Použití knihovny BulkExecutor .NET pro grafy k provádění hromadných operací v rozhraní Gremlin API služby Azure Cosmos DB

Tento kurz obsahuje pokyny k používání knihovny BulkExecutor .NET služby Azure Cosmos DB k importu objektů grafu do kontejneru rozhraní Gremlin API služby Azure Cosmos DB a jejich aktualizaci. Tento proces využívá třídu Graph v [knihovně BulkExecutor](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) k programovému vytváření objektů vrcholů a hran a následnému vložení několika z nich na žádost sítě. Toto chování je možné prostřednictvím knihovny BulkExecutor nakonfigurovat pro optimální využití prostředků databáze i místní paměti.

Na rozdíl od odesílání dotazů Gremlin do databáze, kde se příkazy vyhodnocují a následně spouštějí jeden po druhém, bude při použití knihovny BulkExecutor potřeba vytvářet a ověřovat objekty místně. Po vytvoření objektů vám knihovna umožní postupně odeslat objekty grafu do databázové služby. Pomocí této metody je možné až 100krát zvýšit rychlost příjmu dat, což z ní dělá ideální metodu pro počáteční migrace dat nebo pravidelné operace přesunu dat. Další informace najdete na stránce [ukázkové aplikace Azure Cosmos DB Graph BulkExecutor](https://aka.ms/graph-bulkexecutor-sample) na GitHubu.

## <a name="bulk-operations-with-graph-data"></a>Hromadné operace s daty grafu

[Knihovna BulkExecutor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) obsahuje obor názvů `Microsoft.Azure.CosmosDB.BulkExecutor.Graph`, prostřednictvím kterého poskytuje funkce pro vytváření a import objektů grafu. 

Následující proces popisuje možnosti využití migrace dat pro kontejner rozhraní Gremlin API:
1. Načtení záznamů ze zdroje dat.
2. Vytvoření objektů `GremlinVertex` a `GremlinEdge` ze získaných záznamů a jejich přidání do datové struktury `IEnumerable`. V této části aplikace by se měla implementovat logika pro zjišťování a přidávání relací pro případ, že zdrojem dat není databáze grafu.
3. Vložení objektů grafu do kolekce pomocí metody [Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet).

Tento mechanismus zlepší efektivitu migrace dat v porovnání s použitím klienta Gremlin. K tomuto zlepšení dochází, protože vkládání dat pomocí Gremlin vyžaduje, aby aplikace odesílala dotazy jeden po druhém, a k vytvoření dat je potřeba tyto dotazy ověřit, vyhodnotit a pak spustit. Knihovna BulkExecutor bude zpracovávat ověřování v aplikaci a pro každou žádost sítě odešle několik objektů grafu najednou.

### <a name="creating-vertices-and-edges"></a>Vytváření vrcholů a hran

`GraphBulkExecutor` poskytuje metodu `BulkImportAsync`, která vyžaduje seznam `IEnumerable` objektů `GremlinVertex` nebo `GremlinEdge` definovaných v oboru názvu `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. V ukázce jsme hrany a vrcholy rozdělili do dvou úloh importu knihovny BulkExecutor. Viz následující příklad:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Další informace o parametrech knihovny BulkExecutor najdete v [tématu popisujícím hromadný import dat do služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db).

V objektech `GremlinVertex` a `GremlinEdge` je potřeba vytvořit instanci datové části. Tyto objekty můžete vytvořit následovně:

**Vrcholy:**
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Hrany:**
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Nástroj BulkExecutor před přidáním hran neprovádí automatickou kontrolu existujících vrcholů. Toto je potřeba ověřit v aplikaci před spuštěním úloh BulkImport.

## <a name="sample-application"></a>Ukázková aplikace

### <a name="prerequisites"></a>Požadavky
* Sada Visual Studio 2017 se sadou funkcí Vývoj pro Azure. Můžete začít zdarma se sadou [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/).
* Předplatné Azure. [Tady si můžete vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Alternativně si můžete na stránce [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) vytvořit účet databáze Cosmos DB bez předplatného Azure.
* Databáze rozhraní Gremlin API služby Azure Cosmos DB s **neomezenou kolekcí**. Tato příručka ukazuje, jak začít s rozhraním [Gremlin API služby Azure Cosmos DB v .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Další informace najdete na [stránce pro stažení Gitu](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace
V tomto kurzu provedeme úvodní kroky s použitím [ukázky Azure Cosmos DB Graph BulkExecutor](https://aka.ms/graph-bulkexecutor-sample) hostované na GitHubu. Tato aplikace se skládá z řešení .NET, které náhodně generuje objekty vrcholů a hran a pak je hromadně vkládá do zadaného účtu databáze grafu. Aplikaci získáte spuštěním následujícího příkazu `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Toto úložiště obsahuje ukázku GraphBulkExecutor s následujícími soubory:

File|Popis
---|---
`App.config`|V tomto souboru se zadávají parametry specifické pro aplikaci a databázi. Tento soubor by se měl upravit jako první, aby bylo možné se připojit k cílové databázi a kolekcím.
`Program.cs`| Tento soubor obsahuje logiku vytvoření kolekce `DocumentClient`, která zpracovává čištění a odesílání požadavků knihovny BulkExecutor.
`Util.cs`| Tento soubor obsahuje pomocnou třídu, která obsahuje logiku generování testovacích dat a kontroluje existenci databáze a kolekcí.

Soubor `App.config` obsahuje následující hodnoty konfigurace, které můžete zadat:

Nastavení|Popis
---|---
`EndPointUrl`|Toto je **váš koncový bod sady .NET SDK**, který najdete v okně Přehled vašeho účtu databáze rozhraní Gremlin API služby Azure Cosmos DB. Tato hodnota má formát `https://your-graph-database-account.documents.azure.com:443/`.
`AuthorizationKey`|Toto je primární nebo sekundární klíč uvedený ve vašem účtu služby Azure Cosmos DB. Další informace o [Zabezpečení přístupu k datům ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`, `CollectionName`|Toto jsou **názvy cílové databáze a kolekce**. Pokud je možnost `ShouldCleanupOnStart` nastavená na hodnotu `true`, použijí se tyto hodnoty společně s hodnotou `CollectionThroughput` k odstranění cílové databáze a kolekce a vytvoření nové databáze a kolekce. Podobně pokud je možnost `ShouldCleanupOnFinish` nastavená na hodnotu `true`, použijí se k odstranění databáze okamžitě po skončení příjmu dat. Nezapomeňte, že cílová kolekce musí být **neomezená kolekce**.
`CollectionThroughput`|Toto nastavení slouží k vytvoření nové kolekce v případě, že je možnost `ShouldCleanupOnStart` nastavená na hodnotu `true`.
`ShouldCleanupOnStart`|Toto nastavení před spuštěním programu odstraní účet databáze a kolekce a pak vytvoří novou databázi a kolekce s použitím hodnot `DatabaseName`, `CollectionName` a `CollectionThroughput`.
`ShouldCleanupOnFinish`|Toto nastavení po spuštění programu odstraní účet databáze a kolekce se zadanými hodnotami `DatabaseName` a `CollectionName`.
`NumberOfDocumentsToImport`|Toto nastavení určí počet testovacích vrcholů a hran, které se v ukázce vygenerují. Toto číslo bude platit jak pro vrcholy, tak pro hrany.
`NumberOfBatches`|Toto nastavení určí počet testovacích vrcholů a hran, které se v ukázce vygenerují. Toto číslo bude platit jak pro vrcholy, tak pro hrany.
`CollectionPartitionKey`|Toto nastavení se použije k vytvoření testovacích vrcholů a hran, při kterém se tato vlastnost automaticky přiřadí. Toto nastavení se také použije při opětovném vytváření databáze a kolekcí v případě, že je možnost `ShouldCleanupOnStart` nastavená na hodnotu `true`.

### <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

1. Do souboru `App.config` přidejte parametry konfigurace specifické pro vaši databázi. Tento soubor se použije k vytvoření instance DocumentClient. Pokud se databáze a kontejner ještě nevytvořily, vytvoří se automaticky.
2. Spusťte aplikaci. Tím se dvakrát zavolá `BulkImportAsync`, jednou kvůli importu vrcholů a jednou kvůli importu hran. Pokud některý z objektů při vkládání vygeneruje chybu, přidá se do souboru `.\BadVertices.txt` nebo `.\BadEdges.txt`.
3. Odešlete dotaz na databázi grafu a vyhodnoťte výsledky. Pokud je možnost `ShouldCleanupOnFinish` nastavená na hodnotu true, databáze se automaticky odstraní.

## <a name="next-steps"></a>Další postup
* Podrobné informace o balíčku NuGet a poznámky k verzi knihovny BulkExecutor .NET najdete v [podrobnostech o sadě BulkExecutor SDK](sql-api-sdk-bulk-executor-dot-net.md). 
* Pokud chcete ještě více optimalizovat využití knihovny BulkExecutor, přečtěte si [Tipy pro zvýšení výkonu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips).
* V [článku o oboru názvů BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) najdete další podrobnosti o třídách a metodách definovaných v tomto oboru názvů.
