---
title: Sledování a ladění pomocí metriky v Azure Cosmos DB | Microsoft Docs
description: Použijte metriky v Azure Cosmos DB ladit běžné problémy a databáze.
keywords: Průzkumníku metrik
services: cosmos-db
author: gnot
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 5f6852de2c09e3de9375a2cb5d73f052ac68f039
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100568"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Sledování a ladění pomocí metriky v Azure Cosmos DB

Azure Cosmos DB poskytuje metriky propustnosti, úložiště, konzistence, dostupnosti a latenci. [Portál Azure](https://portal.azure.com) poskytuje souhrnné zobrazení se o tyto metriky; pro podrobnější metriky, u klienta SDK a [diagnostické protokoly](./logging.md) jsou k dispozici.

Pokud chcete získat přehled o nové metriky a další najít aktivní oddíly v databázi, v následujícím videu Azure pátek:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Tento článek vás provede běžné případy použití a jak metrik Azure Cosmos DB slouží k analýze a ladění tyto problémy. Metriky se shromažďují každých pět minut a jsou uchovány sedm dní.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Principy počet požadavků jsou buď úspěšné nebo způsobuje chyby

Abyste mohli začít, přejděte na [portál Azure](https://portal.azure.com) a přejděte do **metriky** okno. V okně Najít **překročen počet požadavků kapacity za 1 minutu** grafu. Tento graf znázorňuje celkový počet minut pomocí minutu požadavky oddělených stavový kód. Další informace o stavových kódech HTTP najdete v tématu [stavové kódy HTTP pro Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Nejběžnější stavový kód chyby je 429 (míra omezení nebo omezení), což znamená, že jsou požadavky na Azure Cosmos DB překročení zřízené propustnosti. Nejběžnější řešením je [škálovat RUs](./set-throughput.md) pro danou kolekci.

![Počet požadavků za minutu](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Určení distribuční propustnost napříč oddíly

S funkčním mohutnost klíče oddílu je nezbytné pro všechny škálovatelné aplikace. Chcete-li určit propustnost rozdělení jakékoli dělené kolekce členěné podle oddílů, přejděte na **okno metriky** v [portál Azure](https://portal.azure.com). V **propustnost** kartě rozpis úložiště se zobrazí v **maximální RU za sekundu spotřebovávají každý fyzický oddíl** grafu. Následující obrázek znázorňuje příklad nízký rozdělení dat doloženo zkreslilo oddílu na levém. 

![Jeden oddíl zobrazuje velkou využití v 15:05:00](media/use-metrics/metrics-17.png)

Může způsobit, že nerovnoměrné propustnost distribuční *aktivní* oddíly, které může vést k omezenému požadavky a může vyžadovat změnou rozdělení. Další informace o vytváření oddílů v Azure Cosmos DB najdete v tématu [oddílu a škálování v Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Určení distribuční úložiště napříč oddíly

S funkčním kardinalitu oddílu na disku je nezbytné pro všechny škálovatelné aplikace. K určení distribučních propustnost žádné dělené kolekce členěné podle oddílů, přejděte do okna metriky v [portál Azure](https://portal.azure.com). Na kartě propustnost úložiště rozpis ukazuje maximální spotřebovávají každým grafem fyzickém oddílu RU za sekundu. Nízký distribuci dat doloženo zkreslilo oddílu na levém je znázorněný na následujícím obrázku. 

![Příklad rozdělení nízký dat](media/use-metrics/metrics-07.png)

Můžete hlavní příčina, který klíč oddílu je zkosení distribuce kliknutím na oddíl v grafu. 

![Klíč oddílu je zkosení distribuce](media/use-metrics/metrics-05.png)

Po identifikaci které klíč oddílu je v distribučním příčinou zkosení, bude pravděpodobně změny rozdělení kolekce s více distribuovanými klíč oddílu. Další informace o vytváření oddílů v Azure Cosmos DB najdete v tématu [oddílu a škálování v Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Porovnání velikost dat proti velikost indexu

Celkové spotřebované úložiště v Azure Cosmos DB, je kombinace velikost dat a velikost indexu. Zlomek velikost dat je obvykle velikost indexu. V okně metriky v [portál Azure](https://portal.azure.com), na kartě úložiště umožňující prezentovat rozpis spotřeba úložiště na základě dat a index. Bitové kopie (možná) Alternativně ze sady SDK můžete najít aktuální využití úložiště přes kolekci pro čtení.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Pokud chcete kvůli úspoře místa na index, můžete upravit [indexování zásad](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Ladění proto dotazy běží pomalu.

V rozhraní API sady SQL SDK poskytuje Azure Cosmos DB statistik provádění dotazů. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
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

*QueryMetrics* poskytuje podrobné informace o tom, jak dlouho trvalo jednotlivé komponenty dotazu k provádění. Nejběžnější příčiny pro dlouho spuštěná dotazy jsou kontroly (dotaz nebylo možné využít indexy), které lze vyřešit s lepší podmínku filtrování.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili sledování a ladění problémů pomocí metriky v portálu Azure, můžete další informace týkající se vylepšení výkonu databáze přečíst v následujících článcích:

* [Výkonu a možností škálování testování pomocí Azure Cosmos DB](performance-testing.md)
* [Tipy pro zvýšení výkonu pro Azure Cosmos DB](performance-tips.md)
