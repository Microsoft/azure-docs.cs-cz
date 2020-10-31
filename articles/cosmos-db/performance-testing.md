---
title: Testování výkonu a škálování pomocí Azure Cosmos DB
description: Naučte se, jak provádět testování rozsahu a výkonu pomocí Azure Cosmos DB. Pak můžete vyhodnotit funkčnost Azure Cosmos DB pro vysoce výkonné aplikace.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: b89c48b5040c198cb1b4a6ce383baed51dfac19e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097409"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Testování výkonu a škálování pomocí Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Testování výkonu a škálování je klíčový krok při vývoji aplikací. U mnoha aplikací má databázová vrstva významný dopad na celkový výkon a škálovatelnost. Proto je to kritická součást testování výkonu. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je účelově sestavený pro elastické škálování a předvídatelný výkon. Tyto funkce se skvěle hodí pro aplikace, které potřebují vysoce výkonnou databázovou vrstvu. 

Tento článek je referenční informace pro vývojáře implementující sady testů výkonnosti pro své Azure Cosmos DB úlohy. Dá se taky použít k vyhodnocení Azure Cosmos DB pro vysoce výkonné aplikace. Zaměřuje se hlavně na izolované testování výkonu databáze, ale zahrnuje i osvědčené postupy pro produkční aplikace.

Po přečtení tohoto článku budete moci zodpovědět následující otázky: 

* Kde najdu ukázkovou klientskou aplikaci rozhraní .NET pro testování výkonu Azure Cosmos DB? 
* Návody dosáhnout vysoké propustnosti pomocí Azure Cosmos DB z mé klientské aplikace?

Chcete-li začít s kódem, Stáhněte projekt z [Azure Cosmos DB ukázka testování výkonu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Cílem této aplikace je Ukázat, jak dosáhnout nejlepšího výkonu z Azure Cosmos DB s malým počtem klientských počítačů. Cílem této ukázky je nedosáhnout kapacity propustnosti Azure Cosmos DB (což se může škálovat bez omezení).

Pokud hledáte možnosti konfigurace na straně klienta pro zlepšení výkonu Azure Cosmos DB, přečtěte si téma [tipy pro Azure Cosmos DB výkon](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Spuštění aplikace testování výkonu
Nejrychlejší způsob, jak začít, je zkompilovat a spustit ukázku .NET, jak je popsáno v následujícím postupu. Můžete si také projít zdrojový kód a implementovat podobné konfigurace na vlastní klientské aplikace.

**Krok 1:** Stáhněte si projekt z [ukázky Azure Cosmos DB testování výkonu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)nebo rozvětvete úložiště GitHub.

**Krok 2:** Upravte nastavení pro EndpointUrl, Authorizationkey tak, CollectionThroughput a DocumentTemplate (volitelné) v App.config.

> [!NOTE]
> Než zřídíte kolekce s vysokou propustností, získáte informace na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) za účelem odhadu nákladů na kolekci. VyAzure Cosmos DB služby Storage a propustnost nezávisle na hodinovém základě. Po otestování můžete ušetřit náklady odstraněním nebo snížením propustnosti kontejnerů Azure Cosmos.
> 
> 

**Krok 3:** Zkompilujte a spusťte konzolovou aplikaci z příkazového řádku. Měl by se zobrazit výstup podobný následujícímu:

```bash
C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
Summary:
---------------------------------------------------------------------
Endpoint: https://arramacquerymetrics.documents.azure.com:443/
Collection : db.data at 100000 request units per second
Document Template*: Player.json
Degree of parallelism*: -1
---------------------------------------------------------------------
DocumentDBBenchmark starting...
Found collection data with 100000 RU/s
Starting Inserts with 100 tasks
Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

Summary:
---------------------------------------------------------------------
Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
---------------------------------------------------------------------
DocumentDBBenchmark completed successfully.
Press any key to exit...
```

**Krok 4 (v případě potřeby):** Propustnost uvedená v tomto nástroji (RU/s) musí být stejná nebo vyšší než zajištěná propustnost kolekce nebo sada kolekcí. Pokud to tak není, můžete zvýšit DegreeOfParallelism v malých přírůstcíchch, které vám pomůžou dosáhnout tohoto limitu. Pokud propustnost z klientské aplikace plateaus, spusťte více instancí aplikace na dalších klientských počítačích. Pokud potřebujete pomoc s tímto krokem, Projděte si lístek podpory z [Azure Portal](https://portal.azure.com).

Po spuštění aplikace můžete vyzkoušet různé [zásady indexování](index-policy.md) a [úrovně konzistence](consistency-levels.md) a pochopit jejich dopad na propustnost a latenci. Můžete také zkontrolovat zdrojový kód a implementovat podobné konfigurace do vlastních testovacích sad nebo produkčních aplikací.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme se podívali na to, jak můžete provádět testování výkonu a škálování pomocí Azure Cosmos DB pomocí konzolové aplikace .NET. Další informace najdete v následujících článcích:

* [Ukázka testování výkonu Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Možnosti konfigurace klienta pro zlepšení výkonu Azure Cosmos DB](performance-tips.md)
* [Dělení na straně serveru v Azure Cosmos DB](partitioning-overview.md)


