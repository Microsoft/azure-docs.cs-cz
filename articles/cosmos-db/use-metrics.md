---
title: Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Použijte metriky ve službě Azure Cosmos DB k ladění běžné problémy a databáze.
keywords: Průzkumníku metrik
services: cosmos-db
author: kanshiG
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 792e0b3f8fdfe4ab1b79fec5f45d0587033eca0d
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055195"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje metriky pro výkon, úložiště, konzistencí, dostupností a latencí. [Webu Azure portal](https://portal.azure.com) poskytuje souhrnný náhled na tyto metriky; pro podrobnější metriky, jak Klientská sada SDK a [diagnostické protokoly](./logging.md) jsou k dispozici.

Tento článek vás provede běžné případy použití a použití metrik služby Azure Cosmos DB k analýze a ladění těchto problémů. Metriky shromažďovaných každých pět minut a se uchovávají po dobu sedmi dní.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Principy kolik požadavků jsou buď úspěšné nebo příčinou chyb

Abyste mohli začít, přejděte k [webu Azure portal](https://portal.azure.com) a přejděte do **metriky** okno. V okně Najít **počet žádostí překračujících kapacitu za 1 minutu** grafu. Tento graf zobrazuje celkový počet minut pomocí minutu požadavků segmentované podle stavový kód. Další informace o stavových kódů HTTP najdete v tématu [stavové kódy HTTP pro službu Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Nejběžnější stavový kód chyby je 429 (přenosové rychlosti, omezení a omezení šířky pásma), což znamená, že požadavky na služby Azure Cosmos DB jsou překročení zřízené propustnosti. Nejběžnější řešením je [vertikálně navýšit kapacitu jednotky ru](./set-throughput.md) pro danou kolekci.

![Počet žádostí za minutu](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Zjištění propustnosti distribuce napříč oddíly

S dobrým Kardinalita klíče oddílu je zásadní pro jakékoli škálovatelné aplikace. Pokud chcete zjistit propustnost distribuci libovolné dělený kontejner porušena oddíly, přejděte **do okna metrik** v [webu Azure portal](https://portal.azure.com). V **propustnost** kartě rozpis úložiště se zobrazí v **maximální počet spotřebovaných RU/s pro jednotlivé fyzické oddíly** grafu. Následující obrázek znázorňuje příklad špatného distribuci dat doloženo výrazně nerovnoměrnou distribucí oddílu úplně vlevo. 

![Jeden oddíl zobrazuje náročné využití v 15:05:00](media/use-metrics/metrics-17.png)

Může způsobit, že nerovnoměrné propustnost distribuce *horké* oddíly, což může vést k omezené požadavky a může vyžadovat oddílů. Další informace o dělení ve službě Azure Cosmos DB najdete v tématu [dělení a škálování ve službě Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Určení úložiště distribuce napříč oddíly

S dobrým Kardinalita oddílů je zásadní pro jakékoli škálovatelné aplikace. Pokud chcete zjistit propustnost distribuci libovolné dělený kontejner porušena oddíly, přejděte do okna metrik [webu Azure portal](https://portal.azure.com). Na kartě propustnost rozpis úložiště se zobrazí v maximální počet spotřebovaných RU/s každým grafem fyzický oddíl. Nízký distribuci dat doloženo výrazně nerovnoměrnou distribucí oddílu úplně vlevo je znázorněný na následujícím obrázku. 

![Příklad distribuce nízký dat](media/use-metrics/metrics-07.png)

Můžete hlavní příčiny, které klíče oddílu je zkosení distribuci po kliknutí na oddíl v grafu. 

![Klíč oddílu je zkosení distribuce](media/use-metrics/metrics-05.png)

Po identifikaci který klíč oddílu je příčinou zkosení v distribuci, budete muset změnit rozdělení kontejneru s více distribuované klíč oddílu. Další informace o dělení ve službě Azure Cosmos DB najdete v tématu [dělení a škálování ve službě Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Porovnání dat velikosti velikost indexu

Celkové úložiště spotřebované ve službě Azure Cosmos DB, je kombinací identifikátoru velikost dat i velikost indexu. Velikost indexu je obvykle zlomek velikost dat. V okně metriky [webu Azure portal](https://portal.azure.com), na kartě úložiště představuje sadu rozpis využití úložiště na základě dat a indexu. Bitové kopie (možná) můžete také ze sady SDK můžete najít aktuální využití úložiště přes kolekci číst.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Pokud chcete ušetřit místo na index, můžete upravit [zásady indexování](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Ladění důvod, proč dotazy se zpracovávají pomalu

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

*QueryMetrics* poskytuje podrobné informace o jak dlouho trvalo každou komponentu dotazu ke spuštění. Nejběžnější příčina pro dlouho běžící dotazy jsou kontroly (dotaz nebyl schopen využít indexy), které mohou být vyřešeny s lepší podmínky filtru.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak k monitorování a ladění problémů pomocí metrik na webu Azure Portal k dispozici, můžete další informace týkající se vylepšení výkonu databáze najdete v následujících článcích:

* [Testování pomocí služby Azure Cosmos DB výkonu a škálování](performance-testing.md)
* [Tipy ke zvýšení výkonu pro službu Azure Cosmos DB](performance-tips.md)
