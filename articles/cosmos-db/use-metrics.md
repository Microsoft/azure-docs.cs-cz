---
title: Monitorování a ladění metrik v Azure Cosmos DB
description: Pomocí metrik v Azure Cosmos DB můžete ladit běžné problémy a monitorovat databázi.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065936"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitorování a ladění metrik v Azure Cosmos DB

Azure Cosmos DB poskytuje metriky pro propustnost, úložiště, konzistenci, dostupnost a latenci. Azure Portal nabízí agregované zobrazení těchto metrik. Metriky služby Azure Cosmos DB můžete zobrazit také s využitím rozhraní API služby Azure Monitor. Další informace o tom, jak zobrazit metriky z Azure Monitor, najdete v článku [Získat metriky z Azure Monitoru.](cosmos-db-azure-monitor-metrics.md) 

Tento článek vás provede běžnými případy použití a tím, jak lze metriky Azure Cosmos DB použít k analýze a ladění těchto problémů. Metriky jsou shromažďovány každých pět minut a jsou uchovávány po dobu sedmi dnů.

## <a name="view-metrics-from-azure-portal"></a>Zobrazení metrik z webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete podokno **Metriky.** Ve výchozím nastavení se v podokně metrik zobrazují metriky úložiště, index, jednotky požadavků pro všechny databáze ve vašem účtu Azure Cosmos. Tyto metriky můžete filtrovat podle databáze, kontejneru nebo oblasti. Metriky můžete také filtrovat v určitém čase rozlišovací schopnost. Další podrobnosti o propustnosti, úložiště, dostupnosti, latenci a konzistenci metriky jsou k dispozici na samostatných kartách. 

   ![Metriky výkonu Cosmos DB na webu Azure Portal](./media/use-metrics/performance-metrics.png)

V podokně Metriky jsou k dispozici následující **metriky:** 

* **Metriky propustnosti** – tato metrika zobrazuje počet požadavků spotřebovaných nebo neúspěšných (429 kód odpovědi), protože byla překročena propustnost nebo kapacita úložiště zřízená pro kontejner.

* **Metriky úložiště** – tato metrika zobrazuje velikost dat a využití indexu.

* **Metriky dostupnosti** – tato metrika zobrazuje procento úspěšných požadavků za celkový počet požadavků za hodinu. Úspěšnost je definována sla Služby SLA DB služby Azure Cosmos.

* **Metriky latence** – tato metrika zobrazuje latenci čtení a zápisu pozorovanou službou Azure Cosmos DB v oblasti, kde funguje váš účet. Můžete vizualizovat latenci napříč oblastmi pro geograficky replikovaný účet. Tato metrika nepředstavuje latenci požadavků od konce na konec.

* **Metriky konzistence** – tato metrika ukazuje, jak je konzistence pro model konzistence, který zvolíte. U účtů s více oblastmi tato metrika také zobrazuje latenci replikace mezi vybranými oblastmi.

* **Systémové metriky** – tato metrika ukazuje, kolik požadavků metadat jsou obsluhovány hlavní oddíl. Pomáhá také identifikovat omezené požadavky.

V následujících částech jsou vysvětleny běžné scénáře, kde můžete použít metriky Azure Cosmos DB. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Zjistěte, kolik požadavků je úspěšných nebo způsobujích chyby

Chcete-li začít, přejděte na [portál Azure](https://portal.azure.com) a přejděte na okno **Metriky.** V okně najděte **Počet požadavků překročil kapacitu za 1 minutový graf. Tento graf zobrazuje celkové požadavky po minutách segmentované stavovým kódem. Další informace o stavových kódech HTTP najdete [v tématu STAVOVÉ KÓDY HTTP pro Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Nejběžnější kód stavu chyby je 429 (omezení rychlosti nebo omezení). Tato chyba znamená, že požadavky na Azure Cosmos DB jsou více než zřízené propustnost. Nejběžnější řešení tohoto problému je [vertikálně navýšit kapacitu ru](./set-throughput.md) pro danou kolekci.

![Počet požadavků za minutu](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Určení distribuce propustnostna mezi oddíly

S dobrou mohutnost klíče oddílu je nezbytné pro všechny škálovatelné aplikace. Chcete-li určit rozložení propustnost libovolného oddílového kontejneru rozděleného podle oddílů, přejděte k **okně Metriky** na [webu Azure Portal](https://portal.azure.com). Na kartě **Propustnost** se rozdělení úložiště zobrazuje v **maximální spotřebované RU za sekundu podle každého grafu fyzického oddílu.** Následující obrázek znázorňuje příklad špatné distribuce dat, jak ukazuje zkosený oddíl zcela vlevo.

![Jeden oddíl vidí těžké využití v 15:05](media/use-metrics/metrics-17.png)

Nerovnoměrné rozložení propustnost může způsobit *horké* oddíly, což může mít za následek omezené požadavky a může vyžadovat přerozdělení. Další informace o dělení v Azure Cosmos DB najdete [v tématu dělení a škálování v Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Určení distribuce úložiště mezi oddíly

S dobrou mohutnost oddílu je nezbytné pro všechny škálovatelné aplikace. Chcete-li zjistit rozložení úložiště libovolného oddílového kontejneru rozděleného podle oddílů, přemisťujte se do okna Metriky na [webu Azure Portal](https://portal.azure.com). Na kartě Úložiště se rozdělení úložiště zobrazí v úložišti Data + Index spotřebovaném grafem klíčů horního oddílu. Následující obrázek znázorňuje špatnou distribuci úložiště dat, jak ukazuje zkosený oddíl zcela vlevo.

![Příklad špatné distribuce dat](media/use-metrics/metrics-07.png)

Můžete způsobit, který klíč oddílu zkosení distribuce kliknutím na oddíl v grafu.

![Klíč oddílu zkosení distribuce](media/use-metrics/metrics-05.png)

Po určení, který klíč oddílu je příčinou zkosení v distribuci, bude pravděpodobně nutné přerozdělit kontejner s více distribuovaný klíč oddílu. Další informace o dělení v Azure Cosmos DB najdete [v tématu dělení a škálování v Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Porovnání velikosti dat s velikostí indexu

V Azure Cosmos DB je celkové spotřebované úložiště kombinací velikosti dat i velikosti indexu. Velikost indexu je obvykle zlomek velikosti dat. V okně Metriky na [webu Azure Portal](https://portal.azure.com)karta Úložiště zobrazuje rozdělení spotřeby úložiště na základě dat a indexu.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Chcete-li ušetřit místo v indexu, můžete upravit [zásady indexování](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Ladění, proč dotazy běží pomalu

V sadách Sql API SDK poskytuje Azure Cosmos DB statistiky spuštění dotazu.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* poskytuje podrobnosti o tom, jak dlouho každá součást dotazu trvalo spuštění. Nejběžnější hlavní příčinou pro dlouho běžící dotazy je prohledává, což znamená, že dotaz nebyl schopen využít indexy. Tento problém lze vyřešit s lepší podmínkou filtru.

## <a name="next-steps"></a>Další kroky

Teď jste se naučili, jak sledovat a ladit problémy pomocí metriky uvedené na webu Azure Portal. Další informace o zlepšení výkonu databáze získáte v následujících článcích:

* Další informace o tom, jak zobrazit metriky z Azure Monitor, najdete v článku [Získat metriky z Azure Monitoru.](cosmos-db-azure-monitor-metrics.md) 
* [Testování výkonu a škálování s Azure Cosmos DB](performance-testing.md)
* [Tipy pro zvýšení výkonu pro Azure Cosmos DB](performance-tips.md)
