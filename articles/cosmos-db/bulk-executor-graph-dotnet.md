---
title: Použití knihovny .NET hromadně prováděcího modulu graphu s rozhraním API Azure Cosmos DB Gremlin
description: Naučte se používat knihovnu hromadných prováděcích modulů k hromadnému importu dat grafu do Azure Cosmos DB kontejneru rozhraní API Gremlin.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 15e94dac02770bf28aae4cbfc4e337cb68b8be40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425318"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Použití knihovny .NET Bulk prováděče k provádění hromadných operací v rozhraní Azure Cosmos DB API Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

V tomto kurzu se dozvíte, jak pomocí knihovny .NET CosmosDB Bulk prováděcích modulů Azure importovat a aktualizovat objekty grafu do kontejneru rozhraní API pro Azure Cosmos DB Gremlin. Tento proces využívá třídu Graph v [knihovně hromadného prováděcího modulu](./bulk-executor-overview.md) k vytvoření objektů vrcholů a hran programově pro následné vložení více z nich na požadavek na síť. Toto chování lze konfigurovat prostřednictvím knihovny hromadného prováděcího modulu, aby bylo možné optimální využití prostředků databáze i místní paměti.

Na rozdíl od odeslání dotazů Gremlin do databáze, kde je příkaz vyhodnocen a následně proveden po jednom, bude místo toho nutné pomocí knihovny hromadného prováděcího modulu vytvořit a ověřit objekty místně. Po vytvoření objektů vám knihovna umožní postupně odeslat objekty grafu do databázové služby. Pomocí této metody je možné až 100krát zvýšit rychlost příjmu dat, což z ní dělá ideální metodu pro počáteční migrace dat nebo pravidelné operace přesunu dat. Další informace najdete na stránce GitHub [ukázkové aplikace hromadného prováděcího modulu Azure Cosmos DB Graph](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Hromadné operace s daty grafu

[Knihovna hromadného prováděcího modulu](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) obsahuje `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` obor názvů pro poskytování funkcionality pro vytváření a importování objektů grafu. 

Následující proces popisuje možnosti využití migrace dat pro kontejner rozhraní Gremlin API:
1. Načtení záznamů ze zdroje dat.
2. Vytvoření objektů `GremlinVertex` a `GremlinEdge` ze získaných záznamů a jejich přidání do datové struktury `IEnumerable`. V této části aplikace by se měla implementovat logika pro zjišťování a přidávání relací pro případ, že zdrojem dat není databáze grafu.
3. Vložení objektů grafu do kolekce pomocí metody [Graph BulkImportAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync).

Tento mechanismus zlepší efektivitu migrace dat v porovnání s použitím klienta Gremlin. K tomuto zlepšení dochází, protože vkládání dat pomocí Gremlin vyžaduje, aby aplikace odesílala dotazy jeden po druhém, a k vytvoření dat je potřeba tyto dotazy ověřit, vyhodnotit a pak spustit. Knihovna hromadného prováděcího modulu zpracuje ověřování v aplikaci a pošle více objektů grafu současně pro jednotlivé požadavky sítě.

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

Další informace o parametrech knihovny hromadných prováděcích modulů naleznete v [tématu BulkImportData to Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

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
> Nástroj hromadného prováděcího modulu automaticky před přidáním okrajů nekontroluje existující vrcholy. Toto je potřeba ověřit v aplikaci před spuštěním úloh BulkImport.

## <a name="sample-application"></a>Ukázková aplikace

### <a name="prerequisites"></a>Předpoklady
* Visual Studio 2019 s úlohou vývoje Azure. Můžete začít pracovat se sadou [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) zdarma.
* Předplatné Azure. [Tady si můžete vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Případně můžete vytvořit účet databáze Cosmos s možností vyzkoušet si [Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
* Databáze rozhraní Gremlin API služby Azure Cosmos DB s **neomezenou kolekcí**. Tato příručka ukazuje, jak začít s rozhraním [Gremlin API služby Azure Cosmos DB v .NET](./create-graph-dotnet.md).
* Git. Další informace najdete na [stránce pro stažení Gitu](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace
V tomto kurzu budeme postupovat podle kroků, jak začít s využitím [ukázkového vykonavatele Azure Cosmos DB graphu](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) , který je hostovaný na GitHubu. Tato aplikace se skládá z řešení .NET, které náhodně generuje objekty vrcholů a hran a pak je hromadně vkládá do zadaného účtu databáze grafu. Aplikaci získáte spuštěním následujícího příkazu `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Toto úložiště obsahuje ukázku GraphBulkExecutor s následujícími soubory:

Soubor|Description
---|---
`App.config`|V tomto souboru se zadávají parametry specifické pro aplikaci a databázi. Tento soubor by se měl upravit jako první, aby bylo možné se připojit k cílové databázi a kolekcím.
`Program.cs`| Tento soubor obsahuje logiku za vytvoření `DocumentClient` kolekce, zpracování čištění a odesílání požadavků hromadného prováděcího modulu.
`Util.cs`| Tento soubor obsahuje pomocnou třídu, která obsahuje logiku generování testovacích dat a kontroluje existenci databáze a kolekcí.

Soubor `App.config` obsahuje následující hodnoty konfigurace, které můžete zadat:

Nastavení|Popis
---|---
`EndPointUrl`|Toto je **váš koncový bod sady .NET SDK**, který najdete v okně Přehled vašeho účtu databáze rozhraní Gremlin API služby Azure Cosmos DB. Tato hodnota má formát `https://your-graph-database-account.documents.azure.com:443/`.
`AuthorizationKey`|Toto je primární nebo sekundární klíč uvedený ve vašem účtu služby Azure Cosmos DB. Další informace o [Zabezpečení přístupu k datům ve službě Azure Cosmos DB](./secure-access-to-data.md#primary-keys)
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

* Další informace o podrobnostech balíčku NuGet a poznámkách k verzi hromadné prováděcí knihovny .NET najdete v tématu [hromadné prováděcí informace sady SDK](sql-api-sdk-bulk-executor-dot-net.md). 
* Podívejte se na [tipy ke zvýšení výkonu](./bulk-executor-dot-net.md#performance-tips) , abyste mohli dále optimalizovat využití hromadného prováděcího modulu.
* V [článku o oboru názvů BulkExecutor.Graph](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) najdete další podrobnosti o třídách a metodách definovaných v tomto oboru názvů.