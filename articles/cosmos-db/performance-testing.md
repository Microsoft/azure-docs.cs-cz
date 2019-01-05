---
title: Testování pomocí služby Azure Cosmos DB výkonu a škálování
description: Zjistěte, jak škálovat a výkon pomocí služby Azure Cosmos DB. Pak můžete vyhodnotit funkce služby Azure Cosmos DB pro scénáře vysoce výkonných aplikací.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 6d2863e39b5f28c56e2b9045513aa83326d8b8c7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043188"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Testování pomocí služby Azure Cosmos DB výkonu a škálování

Testování výkonu a škálování je klíče krok ve vývoji aplikací. Pro mnoho aplikací na úrovni databáze má významný dopad na celkový výkon a škálovatelnost. Proto je zásadní součástí sady testování výkonu. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) účelově vytvořené pro pružné škálování a předvídatelný výkon. Tyto možnosti umožňují skvěle hodí pro aplikace, které potřebují vysoce výkonné databázové vrstvě. 

Tento článek je odkaz pro vývojáře v implementaci sad testů výkonu pro své úlohy služby Azure Cosmos DB. To také umožňuje vyhodnotit službu Azure Cosmos DB pro scénáře vysoce výkonných aplikací. Se zaměřují hlavně na testování výkonu izolované databáze, ale také obsahuje osvědčené postupy pro produkční aplikace.

Po přečtení tohoto článku, budete moci odpovědět na následující otázky: 

* Kde najdu ukázková klientská aplikace .NET pro testování výkonnosti služby Azure Cosmos DB? 
* Jak dosáhnout vysoké propustnosti s Azure Cosmos DB z Moje klientská aplikace?

Abyste mohli začít s kódem, stáhněte si projekt z [testování ukázkové výkonu služby Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Cílem této aplikace je ukazují, jak získat nejlepší výkon ze služby Azure Cosmos DB s malý počet klientských počítačů. Cílem vzorku není k dosažení maximální kapacitu propustnosti služby Azure Cosmos DB (která může škálovat bez žádné omezení).
> 
> 

Pokud hledáte možnosti konfigurace na straně klienta pro zlepšení výkonu služby Azure Cosmos DB, přečtěte si téma [tipy ke zvýšení výkonu služby Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Spuštění aplikace pro testování výkonu
Nejrychlejší způsob, jak začít pracovat se kompilace a spuštění ukázky .NET, jak je popsáno v následujících krocích. Můžete také zkontrolujte zdrojový kód a implementovat podobné konfigurace na vlastní klientské aplikace.

**Krok 1:** Stáhněte si projekt z [testování ukázkové výkonu služby Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), nebo vytvořit fork úložiště GitHub.

**Krok 2:** Upravte nastavení pro EndpointUrl, authorizationkey tak, CollectionThroughput a DocumentTemplate (volitelné) v souboru App.config.

> [!NOTE]
> Než zřídíte kolekce s vysokou propustností, odkazovat [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) odhadnout náklady na kolekci. Azure Cosmos DB účtů úložiště a propustnost nezávisle na po hodinách. Dá ušetřit odstraněním nebo po testování snížení propustnosti kolekce Azure Cosmos DB.
> 
> 

**Krok 3:** Kompilace a spuštění aplikace konzoly z příkazového řádku. Byste měli vidět výstup podobný tomuto:

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


**Krok 4 (v případě potřeby):** Propustnost hlášené (RU/s) z nástroje by měla být stejná nebo vyšší. zřízená propustnost kolekce nebo sady kolekcí. Pokud není, zvýšení DegreeOfParallelism po malých krocích vám můžou pomoct dosáhla limitu. Pokud se propustnost z klientské aplikace plateaus spusťte více instancí aplikace na další klientské počítače. Pokud potřebujete pomoc s tímto krokem, e-mailu askcosmosdb@microsoft.com nebo z lístek podpory [webu Azure portal](https://portal.azure.com).

Jakmile budete mít spuštěnou aplikaci, můžete zkusit jiný [zásadám indexování](index-policy.md) a [úrovně konzistence](consistency-levels.md) pochopit jejich dopad na propustnost a latenci. Můžete také zkontrolovat zdrojový kód a implementovat podobné konfigurace vlastními testovacích sad nebo aplikacích v produkčním prostředí.

## <a name="next-steps"></a>Další postup
V tomto článku jsme se podívali na tom, jak můžete provádět testování pomocí služby Azure Cosmos DB pomocí konzolové aplikace .NET výkonu a škálování. Další informace najdete v následujících článcích:

* [Testování ukázkové výkonu Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Možnosti konfigurace klienta pro zlepšení výkonu služby Azure Cosmos DB](performance-tips.md)
* [Dělení na straně serveru ve službě Azure Cosmos DB](partition-data.md)


