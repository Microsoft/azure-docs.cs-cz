---
title: Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB
description: Použijte metriky ve službě Azure Cosmos DB k ladění běžné problémy a databáze.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: afc3b9dbb1965ea28995d67f8f5e174721d73197
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999437"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje metriky pro výkon, úložiště, konzistencí, dostupností a latencí. [Webu Azure portal](https://portal.azure.com) poskytuje souhrnný náhled na tyto metriky. Podrobnější metriky, jak Klientská sada SDK a [diagnostické protokoly](./logging.md) jsou k dispozici.

Tento článek vás provede běžné případy použití a použití metrik služby Azure Cosmos DB k analýze a ladění těchto problémů. Metriky shromažďovaných každých pět minut a uchovávají po dobu sedmi dní.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Porozumět, kolik požadavků jsou buď úspěšné nebo příčinou chyb

Abyste mohli začít, přejděte k [webu Azure portal](https://portal.azure.com) a přejděte do **metriky** okno. V okně Najít **počet žádostí překračujících kapacitu za 1 minutu** grafu. Tento graf zobrazuje celkový počet minut pomocí minutu požadavků segmentované podle stavový kód. Další informace o stavových kódů HTTP najdete v tématu [stavové kódy HTTP pro službu Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Je nejběžnější stavový kód chyby 429 (frekvence omezení nebo omezení využití sítě). Tato chyba znamená, že požadavky na služby Azure Cosmos DB překračuje zřízenou propustnost. Nejběžnější řešením tohoto problému je [vertikálně navýšit kapacitu jednotky ru](./set-throughput.md) pro danou kolekci.

![Počet žádostí za minutu](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Určit propustnost rozdělení na oddíly

S dobrým Kardinalita klíče oddílu je zásadní pro jakékoli škálovatelné aplikace. Pokud chcete zjistit propustnost distribuci libovolné dělený kontejner porušena oddíly, přejděte **do okna metrik** v [webu Azure portal](https://portal.azure.com). V **propustnost** kartě rozpis úložiště se zobrazí v **maximální počet spotřebovaných RU/s pro jednotlivé fyzické oddíly** grafu. Na následujícím obrázku najdete příklad špatného distribuci dat, jak je znázorněno výrazně nerovnoměrnou distribucí oddílem úplně vlevo.

![Jeden oddíl zobrazuje náročné využití v 15:05:00](media/use-metrics/metrics-17.png)

Může způsobit, že nerovnoměrné propustnost distribuce *horké* oddíly, což může vést k omezené požadavky a může vyžadovat oddílů. Další informace o dělení ve službě Azure Cosmos DB najdete v tématu [dělení a škálování ve službě Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Určit úložiště rozdělení na oddíly

S dobrým Kardinalita oddílů je zásadní pro jakékoli škálovatelné aplikace. Určit úložiště distribuci libovolné dělený kontejner porušena oddíly, přejděte do okna metrik [webu Azure portal](https://portal.azure.com). Na kartě úložiště se zobrazí rozpis úložiště v Data + úložiště spotřebovávané graf klíče oddílů horní Index. Nízký výskyt úložiště dat, jak je znázorněno výrazně nerovnoměrnou distribucí oddílem úplně vlevo je znázorněný na následujícím obrázku.

![Příklad distribuce nízký dat](media/use-metrics/metrics-07.png)

Můžete hlavní příčiny, které klíče oddílu je zkosení distribuci po kliknutí na oddíl v grafu.

![Klíč oddílu je zkosení distribuce](media/use-metrics/metrics-05.png)

Po identifikaci který klíč oddílu je příčinou zkosení v distribuci, budete muset změnit rozdělení kontejneru s více distribuované klíč oddílu. Další informace o dělení ve službě Azure Cosmos DB najdete v tématu [dělení a škálování ve službě Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Porovnejte velikost dat proti velikost indexu

Celkové úložiště spotřebované ve službě Azure Cosmos DB, je kombinací identifikátoru velikost dat i velikost indexu. Velikost indexu je obvykle zlomek velikost dat. V okně metriky [webu Azure portal](https://portal.azure.com), na kartě úložiště představuje sadu rozpis využití úložiště na základě dat a indexu.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Pokud chcete ušetřit místo na index, můžete upravit [zásady indexování](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Ladění, proč dotazy se zpracovávají pomalu

V rozhraní API sady SQL SDK služby Azure Cosmos DB poskytuje statistiky provádění dotazu.

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

*QueryMetrics* poskytuje podrobné informace o jak dlouho trvalo každou komponentu dotazu ke spuštění. Nejběžnější příčina pro dlouho běžící dotazy je kontrol, to znamená dotaz nebylo možné využívat indexy. Tento problém lze vyřešit s lepší podmínky filtru.

## <a name="next-steps"></a>Další postup

Nyní jste zjistili, jak monitorovat a ladění problémů pomocí metrik na webu Azure Portal k dispozici. Chcete další informace týkající se vylepšení výkonu databáze najdete v následujících článcích:

* [Testování pomocí služby Azure Cosmos DB výkonu a škálování](performance-testing.md)
* [Tipy ke zvýšení výkonu pro službu Azure Cosmos DB](performance-tips.md)
