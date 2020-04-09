---
title: Použití knihovny hromadného prováděcího modulu .NET grafu s rozhraním API Azure Cosmos DB Gremlin
description: Zjistěte, jak pomocí knihovny hromadného prováděcího modulu masivně importovat data grafu do kontejneru rozhraní API Azure Cosmos DB Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: adf512fc521ef553f0bbd6ef6dd8ee19e398b37b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982699"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Použití knihovny hromadného prováděcího modulu .NET grafu k provádění hromadných operací v rozhraní API Azure Cosmos DB Gremlin

Tento kurz obsahuje pokyny o použití hromadné vykonavatel knihovny .NET Azure CosmosDB k importu a aktualizaci objektů grafu do kontejneru rozhraní API Azure Cosmos DB Gremlin. Tento proces využívá třídu Graph v [knihovně hromadného vykonavatele](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) k vytvoření objektů Vrchol a Edge programově a pak vložte více z nich na požadavek na síť. Toto chování je konfigurovatelné prostřednictvím knihovny hromadného vykonavatele, aby bylo možné optimálně využívat prostředky databáze i místní paměti.

Na rozdíl od odesílání Gremlin dotazy do databáze, kde je příkaz vyhodnocena a pak provedeny jeden po druhém, pomocí hromadné vykonavatel knihovny bude místo toho vyžadovat k vytvoření a ověření objektů místně. Po vytvoření objektů vám knihovna umožní postupně odeslat objekty grafu do databázové služby. Pomocí této metody je možné až 100krát zvýšit rychlost příjmu dat, což z ní dělá ideální metodu pro počáteční migrace dat nebo pravidelné operace přesunu dat. Další informace najdete na stránce GitHub [u hromadné hovado aplikace aplikace Azure Cosmos DB Graph hromadné vykořiscení](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Hromadné operace s daty grafu

[Knihovna hromadného prováděcího modulu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` obsahuje obor názvů, který poskytuje funkce pro vytváření a import objektů grafu. 

Následující proces popisuje možnosti využití migrace dat pro kontejner rozhraní Gremlin API:
1. Načtení záznamů ze zdroje dat.
2. Vytvoření objektů `GremlinVertex` a `GremlinEdge` ze získaných záznamů a jejich přidání do datové struktury `IEnumerable`. V této části aplikace by se měla implementovat logika pro zjišťování a přidávání relací pro případ, že zdrojem dat není databáze grafu.
3. Vložení objektů grafu do kolekce pomocí metody [Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet).

Tento mechanismus zlepší efektivitu migrace dat v porovnání s použitím klienta Gremlin. K tomuto zlepšení dochází, protože vkládání dat pomocí Gremlin vyžaduje, aby aplikace odesílala dotazy jeden po druhém, a k vytvoření dat je potřeba tyto dotazy ověřit, vyhodnotit a pak spustit. Knihovna hromadného prováděcího modulu bude zpracovávat ověření v aplikaci a odesílat více objektů grafu najednou pro každý síťový požadavek.

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

Další informace o parametrech knihovny hromadného vykonavatele naleznete v [tématu BulkImportData to Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

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
> Nástroj hromadného vykonavatele automaticky nekontroluje existující vrcholy před přidáním hran. Toto je potřeba ověřit v aplikaci před spuštěním úloh BulkImport.

## <a name="sample-application"></a>Ukázková aplikace

### <a name="prerequisites"></a>Požadavky
* Visual Studio 2019 s úlohou vývoje Azure. S edicí [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) můžete začít zdarma.
* Předplatné Azure. [Tady si můžete vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Případně můžete vytvořit databázový účet Cosmos s [Try Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
* Databáze rozhraní Gremlin API služby Azure Cosmos DB s **neomezenou kolekcí**. Tato příručka ukazuje, jak začít s rozhraním [Gremlin API služby Azure Cosmos DB v .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Další informace najdete na [stránce pro stažení Gitu](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace
V tomto kurzu budeme postupovat podle kroků, jak začít pomocí [ukázky hromadného vykonavatele aplikace Azure Cosmos DB Graph](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) hostované na GitHubu. Tato aplikace se skládá z řešení .NET, které náhodně generuje objekty vrcholů a hran a pak je hromadně vkládá do zadaného účtu databáze grafu. Aplikaci získáte spuštěním následujícího příkazu `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Toto úložiště obsahuje ukázku GraphBulkExecutor s následujícími soubory:

File|Popis
---|---
`App.config`|V tomto souboru se zadávají parametry specifické pro aplikaci a databázi. Tento soubor by se měl upravit jako první, aby bylo možné se připojit k cílové databázi a kolekcím.
`Program.cs`| Tento soubor obsahuje logiku `DocumentClient` vytváření kolekce, zpracování vyčištění a odesílání hromadných požadavků vykonavatele.
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

## <a name="next-steps"></a>Další kroky
* Informace o podrobnostech balíčku Nuget a poznámkách k verzi knihovny .NET hromadného prováděcího modulu naleznete v [podrobnostech sady SDK hromadného vykonavatele](sql-api-sdk-bulk-executor-dot-net.md). 
* Podívejte se na [tipy pro výkon,](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) které dále optimalizují využití hromadného vykonavatele.
* V [článku o oboru názvů BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) najdete další podrobnosti o třídách a metodách definovaných v tomto oboru názvů.
