---
title: Azure Cosmos DB škálování a testování výkonu | Microsoft Docs
description: Naučte se provádět škálování a výkon testování pomocí Azure Cosmos DB
keywords: Testování výkonu
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: ce2c0ddcce3813990bf819477f7db425d70e3595
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612295"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Výkonu a možností škálování testování pomocí Azure Cosmos DB

Testování výkonu a škálování je klíče krok při vývoji aplikace. Mnoho aplikací databázové vrstvy má významný dopad na celkový výkon a škálovatelnost. Proto je zásadní součástí testování výkonu. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je vytvořeného pro tento účel pro elastické škálování a předvídatelný výkon. Tyto funkce umožňují skvělé přizpůsobit pro aplikace, které potřebují a vysoce výkonné databázové vrstvy. 

Tento článek je odkaz pro vývojáře implementace sad testů výkonu pro své úlohy Azure Cosmos DB. Také může sloužit k vyhodnocení Azure Cosmos DB pro vysoce výkonné aplikace scénáře. Se zaměřují hlavně na test výkonnosti izolované databáze, ale také obsahuje osvědčené postupy pro výrobní aplikace.

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky: 

* Kde najdu ukázkovou aplikaci klienta rozhraní .NET pro testování výkonu databáze Cosmos Azure? 
* Jak dosáhnout vysoké propustnosti úrovně s Azure Cosmos DB z mé aplikace klienta?

Pokud chcete začít s kódem, stáhněte si projekt z [testování ukázkové výkonu Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Cílem této aplikace je ukazují, jak získat nejlepší výkon z databáze Cosmos Azure s malý počet klientských počítačů. Cílem vzorku se dosáhnout kapacita propustnosti ve špičce Azure DB Cosmos (který můžete škálovat bez žádné omezení).
> 
> 

Pokud hledáte možnosti konfigurace na straně klienta pro zlepšení výkonu Azure Cosmos DB, přečtěte si téma [tipy pro zvýšení výkonu Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Spuštění testování aplikace výkonu
Pro zkompilování a spuštění vzorového .NET je nejrychlejší způsob, jak začít pracovat, jak je popsáno v následujících krocích. Můžete také zkontrolovat zdrojový kód a implementovat podobné konfigurace na vlastní klientské aplikace.

**Krok 1:** stáhnout projekt z [testování ukázkové výkonu Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), nebo rozvětvit úložiště GitHub.

**Krok 2:** upravit nastavení adresa URL koncového bodu, authorizationkey tak, CollectionThroughput a DocumentTemplate (volitelné) v souboru App.config.

> [!NOTE]
> Než zřídíte kolekce s vysokou propustnost, naleznete [cenová stránky](https://azure.microsoft.com/pricing/details/cosmos-db/) k zjištění přibližné hodnoty náklady na kolekci. Azure účtuje poplatky za úložiště Cosmos databáze a propustnost nezávisle na hodinu. Odstraněním nebo snížení propustnost vaší kolekce Azure Cosmos DB po testování může významně snížit náklady.
> 
> 

**Krok 3:** zkompilování a spuštění aplikace konzoly z příkazového řádku. Měli byste vidět výstup podobný tomuto:

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


**Krok 4 (v případě potřeby):** propustnost hlášené (RU/s) z nástroje musí být stejná nebo vyšší než zřízené propustnosti kolekce nebo sady kolekcí. Pokud není, zvýšení DegreeOfParallelism v malých přírůstcích vám můžou pomoct dosáhne omezení. Pokud náhorních plošinách propustnost z vaší klientské aplikace, spusťte více instancí aplikace na další klientské počítače. Pokud potřebujete pomoc s Tento krok, e-mailu askcosmosdb@microsoft.com nebo soubor lístek podpory z [portál Azure](https://portal.azure.com).

Až budete mít aplikaci spuštěnou, můžete zkusit jiný [indexování zásady](indexing-policies.md) a [úrovně konzistence](consistency-levels.md) pochopit jejich dopad na propustnosti a latence. Můžete také zkontrolovat zdrojový kód a implementovat podobné konfigurace sad testů nebo výrobní aplikace.

## <a name="next-steps"></a>Další postup
V tomto článku jsme se podívali na tom, jak můžete provádět výkonu a možností škálování testování pomocí Azure Cosmos DB pomocí konzolové aplikace .NET. Další informace najdete v následujících článcích:

* [Testování ukázkové Azure Cosmos DB výkonu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Možnosti konfigurace klienta pro zlepšení výkonu Azure Cosmos DB](performance-tips.md)
* [Dělení na straně serveru v Azure Cosmos DB](partition-data.md)


