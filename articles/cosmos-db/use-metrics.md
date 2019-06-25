---
title: Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB
description: Použijte metriky ve službě Azure Cosmos DB k ladění běžné problémy a databáze.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 06/18/2019
ms.reviewer: sngun
ms.openlocfilehash: ef457fe8c21bc7e62f910a78913069df32bea1a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275687"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje metriky pro výkon, úložiště, konzistencí, dostupností a latencí. Na webu Azure portal poskytuje souhrnný náhled na tyto metriky. Můžete také zobrazit metriky služby Azure Cosmos DB z rozhraní API služby Azure Monitor. Další informace o tom, jak zobrazit metriky ze služby Azure monitor, najdete v článku [získat metriky ze služby Azure Monitor](cosmos-db-azure-monitor-metrics.md) článku. 

Tento článek vás provede běžné případy použití a použití metrik služby Azure Cosmos DB k analýze a ladění těchto problémů. Metriky shromažďovaných každých pět minut a uchovávají po dobu sedmi dní.

## <a name="view-metrics-from-azure-portal"></a>Zobrazení metrik z webu Azure portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřít **metriky** podokně. Ve výchozím nastavení v podokně metriky se zobrazují úložiště, index, požadavek jednotky metriky pro všechny databáze ve vašem účtu Azure Cosmos. Můžete filtrovat tyto metriky na databázi, kontejner nebo oblast. Můžete také filtrovat na konkrétní časové intervaly metriky. Další informace o propustnosti, úložiště, dostupnosti, latence a konzistence metriky jsou k dispozici v samostatných kartách. 

   ![Metriky výkonu služby cosmos DB na webu Azure portal](./media/use-metrics/performance-metrics.png)

Tyto metriky jsou k dispozici **metriky** podokna: 

* **Metriky propustnosti** – tato metrika zobrazuje počet požadavků využitý nebo se nezdařilo (kód odpověď 429), protože byla překročena kapacita propustnosti nebo úložiště zřízené pro kontejner.

* **Metriky úložiště** – tato metrika uvádí velikost využití dat a indexu.

* **Metriky dostupnosti** – tato metrika zobrazuje procento úspěšných požadavků za celkový počet požadavků za hodinu. Smlouvách SLA v Azure Cosmos DB je definována míra úspěšnosti.

* **Metriky latence** – tato metrika zobrazuje prostřednictvím služby Azure Cosmos DB v oblasti, ve kterém je váš účet operační latence pro čtení a zápisu. Můžete vizualizovat latence napříč oblastmi geograficky replikovaného účtu. Tato metrika nepředstavuje vyřízení požadavku začátku do konce.

* **Metriky konzistence** – tato metrika ukáže, jak konečnou konzistenci pro model pro zajištění konzistence zvolíte. Tato metrika pro účty ve více oblastech, také ukazuje latence replikace mezi oblastmi, které jste vybrali.

* **Systémové metriky** – tato metrika zobrazuje, kolik žádostí o metadata jsou obsluhovány hlavním oddílu. Také pomáhá identifikovat omezené požadavky.

Následující části popisují obvyklé scénáře, kde můžete použít metriky Azure Cosmos DB. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Porozumět, kolik požadavků jsou buď úspěšné nebo příčinou chyb

Abyste mohli začít, přejděte k [webu Azure portal](https://portal.azure.com) a přejděte do **metriky** okno. V okně Najít ** počet žádostí překračujících kapacitu za 1 minutu grafu. Tento graf zobrazuje celkový počet minut pomocí minutu požadavků segmentované podle stavový kód. Další informace o stavových kódů HTTP najdete v tématu [stavové kódy HTTP pro službu Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

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

* Další informace o tom, jak zobrazit metriky ze služby Azure monitor, najdete v článku [získat metriky ze služby Azure Monitor](cosmos-db-azure-monitor-metrics.md) článku. 
* [Testování pomocí služby Azure Cosmos DB výkonu a škálování](performance-testing.md)
* [Tipy ke zvýšení výkonu pro službu Azure Cosmos DB](performance-tips.md)
