---
title: Testování výkonu a škálování s Azure Cosmos DB
description: Zjistěte, jak provést škálování a testování výkonu pomocí Azure Cosmos DB. Potom můžete vyhodnotit funkce Azure Cosmos DB pro scénáře vysoce výkonných aplikací.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313739"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Testování výkonu a škálování s Azure Cosmos DB

Testování výkonu a škálování je klíčovým krokem ve vývoji aplikací. Pro mnoho aplikací databázové vrstvy má významný vliv na celkový výkon a škálovatelnost. Proto je důležitou součástí testování výkonu. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je účelově vypracovává pro elastické škálování a předvídatelný výkon. Díky těmto funkcím se skvěle hodí pro aplikace, které potřebují vysoce výkonnou databázovou vrstvu. 

Tento článek je odkaz pro vývojáře, kteří implementují sady testů výkonu pro jejich úlohy Azure Cosmos DB. Slouží také k vyhodnocení Azure Cosmos DB pro scénáře vysoce výkonných aplikací. Zaměřuje se především na izolované testování výkonu databáze, ale také obsahuje osvědčené postupy pro produkční aplikace.

Po přečtení tohoto článku budete moci odpovědět na následující otázky: 

* Kde najdu ukázkovou klientskou aplikaci .NET pro testování výkonu Azure Cosmos DB? 
* Jak dosáhnout vysoké úrovně propustnosti s Azure Cosmos DB z mé klientské aplikace?

Pokud chcete začít s kódem, stáhněte si projekt z [ukázky testování výkonu azure cosmos db](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Cílem této aplikace je ukázat, jak získat nejlepší výkon z Azure Cosmos DB s malým počtem klientských počítačů. Cílem ukázky není dosáhnout maximální propustnost kapacity Azure Cosmos DB (které lze škálovat bez omezení).
> 
> 

Pokud hledáte možnosti konfigurace na straně klienta ke zlepšení výkonu Azure Cosmos DB, přečtěte si článek [Tipy pro výkon Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Spuštění aplikace pro testování výkonu
Nejrychlejší způsob, jak začít, je zkompilovat a spustit ukázku .NET, jak je popsáno v následujících krocích. Můžete také zkontrolovat zdrojový kód a implementovat podobné konfigurace ve vlastních klientských aplikacích.

**Krok 1:** Stáhněte si projekt z [ukázky testování výkonu Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)nebo rozvinte úložiště GitHub.

**Krok 2:** Upravte nastavení pro EndpointUrl, AuthorizationKey, CollectionThroughput a DocumentTemplate (volitelné) v App.config.

> [!NOTE]
> Před zřízením kolekce s vysokou propustností, naleznete na [stránce Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/) odhadnout náklady na kolekci. Azure Cosmos DB účtuje úložiště a propustnost nezávisle na hodinovém základě. Náklady můžete ušetřit odstraněním nebo snížením propustnosti kontejnerů Azure Cosmos po testování.
> 
> 

**Krok 3:** Zkompilujte a spusťte konzolovou aplikaci z příkazového řádku. Výstup byste měli vidět takto:

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


**Krok 4 (v případě potřeby):** Propustnost hlášená (RU/s) z nástroje by měla být stejná nebo vyšší než zřízená propustnost kolekce nebo sady kolekcí. Pokud tomu tak není, zvýšení DegreeOfParallelism v malých krocích vám může pomoci dosáhnout limitu. Pokud propustnost z plošiny klientské aplikace, spusťte více instancí aplikace na dalších klientských počítačích. Pokud potřebujete pomoc s tímto krokem soubor lístek podpory z [portálu Azure](https://portal.azure.com).

Po spuštění aplikace můžete vyzkoušet různé [zásady indexování](index-policy.md) a [úrovně konzistence,](consistency-levels.md) abyste pochopili jejich dopad na propustnost a latenci. Můžete také zkontrolovat zdrojový kód a implementovat podobné konfigurace jako vlastní testovací sady nebo produkční aplikace.

## <a name="next-steps"></a>Další kroky
V tomto článku jsme se podívali na to, jak můžete provádět testování výkonu a škálování s Azure Cosmos DB pomocí aplikace konzoly .NET. Další informace najdete v těchto článcích:

* [Ukázka testování výkonu azure cosmos db](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Možnosti konfigurace klienta pro zlepšení výkonu Služby Azure Cosmos DB](performance-tips.md)
* [Dělení na straně serveru v Azure Cosmos DB](partition-data.md)


