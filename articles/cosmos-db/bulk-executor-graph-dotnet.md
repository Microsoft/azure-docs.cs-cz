---
title: Použití knihovny .NET Bulk prováděče k provádění hromadných operací v rozhraní Azure Cosmos DB API Gremlin
description: Naučte se používat knihovnu hromadných prováděcích modulů k hromadnému importu dat grafu do Azure Cosmos DB kontejneru rozhraní API Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: 51bd14c536e46291c8720e6c22e2e03a30243ddf
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827275"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Použití knihovny .NET Bulk prováděče k provádění hromadných operací v rozhraní Azure Cosmos DB API Gremlin

V tomto kurzu se dozvíte, jak pomocí knihovny .NET CosmosDB Bulk prováděcích modulů Azure importovat a aktualizovat objekty grafu do kontejneru rozhraní API pro Azure Cosmos DB Gremlin. Tento proces využívá třídu Graph v [knihovně hromadného prováděcího modulu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) k vytvoření objektů vrcholů a hran programově pro následné vložení více z nich na požadavek na síť. Toto chování lze konfigurovat prostřednictvím knihovny hromadného prováděcího modulu, aby bylo možné optimální využití prostředků databáze i místní paměti.

Na rozdíl od odeslání dotazů Gremlin do databáze, kde je příkaz vyhodnocen a následně proveden po jednom, bude místo toho nutné pomocí knihovny hromadného prováděcího modulu vytvořit a ověřit objekty místně. Po vytvoření objektů knihovna umožňuje postupně odesílat objekty grafu do databázové služby. Pomocí této metody lze zrychlit přijímání dat zvýšit až na ni, což umožňuje ideální metodu pro počáteční migrace dat nebo periodické operace přesunu dat. Další informace najdete na stránce GitHub [ukázkové aplikace hromadného prováděcího modulu Azure Cosmos DB Graph](https://aka.ms/graph-bulkexecutor-sample).

## <a name="bulk-operations-with-graph-data"></a>Hromadné operace s daty grafu

[Knihovna hromadného prováděcího modulu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) obsahuje obor názvů `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` pro poskytování funkcionality pro vytváření a importování objektů grafu. 

Následující postup popisuje, jak se dá migrace dat použít pro kontejner rozhraní Gremlin API:
1. Načte záznamy ze zdroje dat.
2. Sestavte objekty `GremlinVertex` a `GremlinEdge` ze získaných záznamů a přidejte je do struktury dat `IEnumerable`. V této části aplikace by měla být implementována logika pro detekci a přidání relací, pro případ, že zdroj dat není databáze grafu.
3. K vložení objektů grafu do kolekce použijte [metodu Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) .

Tento mechanismus zvýší efektivitu migrace dat ve srovnání s použitím klienta Gremlin. K tomuto vylepšení dochází, protože vkládání dat pomocí Gremlin bude vyžadovat, aby aplikace odeslala dotaz v čase, který bude nutné ověřit, vyhodnotit a následně provést a vytvořit data. Knihovna hromadného prováděcího modulu zpracuje ověřování v aplikaci a pošle více objektů grafu současně pro jednotlivé požadavky sítě.

### <a name="creating-vertices-and-edges"></a>Vytváření vrcholů a hran

`GraphBulkExecutor` poskytuje metodu `BulkImportAsync`, která vyžaduje seznam `IEnumerable` objektů `GremlinVertex` nebo `GremlinEdge` definované v oboru názvů `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. V ukázce jsme rozdělili hrany a vrcholy na dvě BulkExecutor úlohy importu. Podívejte se na následující příklad:

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

Je nutné vytvořit instanci datové části do objektů `GremlinVertex` a `GremlinEdge`. Tady je postup, jak lze tyto objekty vytvořit:

**Vrcholy**:
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

**Hrany**:
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
> Nástroj hromadného prováděcího modulu automaticky před přidáním okrajů nekontroluje existující vrcholy. To je nutné ověřit v aplikaci před spuštěním úloh BulkImport.

## <a name="sample-application"></a>Ukázková aplikace

### <a name="prerequisites"></a>Požadavky
* Visual Studio 2019 s úlohou vývoje Azure. Můžete začít pracovat se sadou [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) zdarma.
* Předplatné Azure. [Bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db)si můžete vytvořit tady. Případně můžete vytvořit účet databáze Cosmos s možností vyzkoušet si [Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
* Azure Cosmos DB databáze rozhraní API Gremlin s **neomezenou kolekcí**. Tato příručka ukazuje, jak začít s [rozhraním API Azure Cosmos DB Gremlin v .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Další informace najdete na [stránce stažení Gitu](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace
V tomto kurzu budeme postupovat podle kroků, jak začít s využitím [ukázkového vykonavatele Azure Cosmos DB graphu](https://aka.ms/graph-bulkexecutor-sample) , který je hostovaný na GitHubu. Tato aplikace se skládá z řešení .NET, které náhodně generuje objekty vrcholů a hran a následně provede hromadné vložení do zadaného účtu databáze grafu. Chcete-li získat aplikaci, spusťte níže uvedený příkaz `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Toto úložiště obsahuje ukázku GraphBulkExecutor s následujícími soubory:

Soubor|Popis
---|---
`App.config`|V tomto umístění jsou zadány specifické parametry aplikace a databáze. Tento soubor by měl být nejprve upraven pro připojení k cílové databázi a kolekcím.
`Program.cs`| Tento soubor obsahuje logiku za vytvoření kolekce `DocumentClient`, zpracování čištění a odesílání požadavků hromadného prováděcího modulu.
`Util.cs`| Tento soubor obsahuje pomocnou třídu, která obsahuje logiku za generování testovacích dat a kontrolu existence databáze a kolekcí.

V souboru `App.config` jsou následující konfigurační hodnoty, které lze zadat:

Nastavením|Popis
---|---
`EndPointUrl`|Toto je **Váš koncový bod .NET SDK** v okně Přehled účtu databáze rozhraní API Azure Cosmos DB Gremlin. Má formát `https://your-graph-database-account.documents.azure.com:443/`.
`AuthorizationKey`|Toto je primární nebo sekundární klíč uvedený pod účtem Azure Cosmos DB. Další informace o [zabezpečení přístupu k Azure Cosmos DB dat](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`, `CollectionName`|Jedná se o **název cílové databáze a kolekce**. Pokud je `ShouldCleanupOnStart` nastavená na `true` tyto hodnoty, společně s `CollectionThroughput`, budou použity k jejich vyřazení a vytvoření nové databáze a kolekce. Podobně pokud je `ShouldCleanupOnFinish` nastaveno na `true`, budou použity k odstranění databáze ihned po převzetí příjmu. Všimněte si, že cílová kolekce musí být **neomezená kolekce**.
`CollectionThroughput`|Tato možnost slouží k vytvoření nové kolekce, pokud je parametr `ShouldCleanupOnStart` nastaven na hodnotu `true`.
`ShouldCleanupOnStart`|Tím se odstraní účet databáze a kolekce před spuštěním programu a pak se vytvoří nové s hodnotami `DatabaseName`, `CollectionName` a `CollectionThroughput`.
`ShouldCleanupOnFinish`|Tím dojde k vyřazení účtu databáze a kolekcí se zadaným `DatabaseName` a `CollectionName` po spuštění programu.
`NumberOfDocumentsToImport`|Tím se určí počet vrcholů testů a hran, které budou vygenerovány v ukázce. Toto číslo se bude vztahovat na vrcholy i hrany.
`NumberOfBatches`|Tím se určí počet vrcholů testů a hran, které budou vygenerovány v ukázce. Toto číslo se bude vztahovat na vrcholy i hrany.
`CollectionPartitionKey`|Tato vlastnost se použije k vytvoření vrcholů a hran testu, kde bude tato vlastnost automaticky přiřazena. Tato možnost se použije taky při opětovném vytváření databáze a kolekcí, pokud je možnost `ShouldCleanupOnStart` nastavená na `true`.

### <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

1. Do `App.config` přidejte konkrétní konfigurační parametry databáze. Tato akce se použije k vytvoření instance DocumentClient. Pokud se databáze a kontejner ještě nevytvořily, vytvoří se automaticky.
2. Spusťte aplikaci. Tato akce zavolá `BulkImportAsync` dvakrát, jednu pro import vrcholů a jednu pro import hran. Pokud některý z objektů vygeneruje chybu při jejich vložení, budou přidány do `.\BadVertices.txt` nebo `.\BadEdges.txt`.
3. Vyhodnoťte výsledky dotazem na databázi grafu. Pokud je možnost `ShouldCleanupOnFinish` nastavená na hodnotu true, databáze se automaticky odstraní.

## <a name="next-steps"></a>Další kroky
* Další informace o podrobnostech balíčku NuGet a poznámkách k verzi hromadné prováděcí knihovny .NET najdete v tématu [hromadné prováděcí informace sady SDK](sql-api-sdk-bulk-executor-dot-net.md). 
* Podívejte se na [tipy ke zvýšení výkonu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) , abyste mohli dále optimalizovat využití hromadného prováděcího modulu.
* Další podrobnosti o třídách a metodách definovaných v tomto oboru názvů najdete v [referenčním článku BulkExecutor. Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) .
