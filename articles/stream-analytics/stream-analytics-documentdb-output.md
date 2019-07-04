---
title: Výstup Azure Stream Analytics ke službě Cosmos DB
description: Tento článek popisuje, jak používat Azure Stream Analytics se uložit výstup do služby Azure Cosmos DB pro výstup JSON pro archivaci dat a dotazy s nízkou latencí nestrukturovaných dat JSON.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: de5febaeecd176a8718364720132d3fa4433c57f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443632"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Výstup Azure Stream Analytics ke službě Azure Cosmos DB  
Stream Analytics můžete směrovat [služby Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) výstup ve formátu JSON, povolení archivace a s nízkou latencí dotazy na data nestrukturovaných dat JSON. Tento dokument uvádí i některé doporučené postupy pro implementaci této konfigurace.

Pro ty, kteří zkušenosti se službou Cosmos DB, podívejte se na [výuky pro služby Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) začít. 

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje jenom připojení ke službě Azure Cosmos DB pomocí **rozhraní SQL API**.
> Další rozhraní API služby Azure Cosmos DB se zatím nepodporují. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, nemusí být data uložená správně. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Základy služby Cosmos DB jako cíl výstupu
Výstup Azure Cosmos DB ve službě Stream Analytics, který umožňuje zapisovat zpracování výsledky jako výstup ve formátu JSON do vaší služby Cosmos DB kontejnerů streamů. Stream Analytics nepodporuje vytvoření kontejnerů ve vaší databázi, místo toho by bylo potřeba vytvořit předem. Je to tak, aby fakturačních nákladů kontejnery služby Cosmos DB se řídí vám a tak, aby mohli vyladit výkon, konzistence a kapacitu vaší kontejnerů přímo pomocí [API služby Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> 0\.0.0.0 je nutné přidat do seznamu povolených IP adres ze brána firewall služby Azure Cosmos DB.

Některé možnosti kontejneru Cosmos DB je podrobně popsaný níže.

## <a name="tune-consistency-availability-and-latency"></a>Vyladění konzistencí, dostupností a latencí
Tak, aby odpovídaly vašim požadavkům na aplikace, služby Azure Cosmos DB vám umožní doladit databáze a kontejnerů a ujistěte se, kompromisy mezi konzistencí, dostupností, latence a propustnosti. V závislosti na tom, jaké úrovně konzistence čtení vašim potřebám scénář pro čtení a zápis latenci, že můžete použít úrovně konzistence na váš účet databáze. Propustnost můžete vylepšit vertikálním navýšení kapacity Units(RUs) žádosti v kontejneru. Ve výchozím nastavení, služby Azure Cosmos DB umožňuje synchronní indexování u každé operace CRUD do kontejneru. Toto je další užitečná možnost řízení výkonu zápisu/čtení ve službě Azure Cosmos DB. Další informace najdete v článku [změnit úrovně konzistence vaší databáze a dotazu](../cosmos-db/consistency-levels.md) článku.

## <a name="upserts-from-stream-analytics"></a>Upsertuje ze Stream Analytics
Integrace Stream Analytics pomocí služby Azure Cosmos DB umožňuje vložit nebo aktualizovat záznamy v kontejnerech založené na daný sloupec ID dokumentu. To se také označuje jako *Upsert*.

Stream Analytics používá optimistické upsert přístupu, kde aktualizace jsou pouze provedeno při vložení selže kvůli konfliktu ID dokumentu. S 1.0 úroveň kompatibility se tato aktualizace provádí jako opravu, takže umožňuje částečné aktualizace v dokumentu, který je, přidání nových vlastností nebo nahrazuje existující vlastnost se provádí postupně. Však není sloučit změny hodnoty vlastnosti pole ve vašich výsledku dokumentu JSON v celého pole získávání přepsány, to znamená, pole. S 1.2 se mění chování upsert k vložení nebo nahrazení dokumentu. To je popsána dále v níže uvedené části 1.2 úroveň kompatibility.

Pokud vstupní dokument JSON má existující pole ID, že pole je automaticky použít jako sloupec ID dokumentu ve službě Cosmos DB a jakékoli další zápisy jsou zpracovány jako takové, což vede k jednomu z těchto situací:
- Jedinečné ID vést k vložení
- duplicitní ID a "ID dokumentu' nastavena na"ID"vede má upsertovat
- duplicitní ID a 'ID dokumentu' není sada vede k chybě, po první dokument

Pokud chcete uložit <i>všechny</i> dokumenty, včetně těch s duplicitním ID přejmenovat pole ID v dotazu (s klíčovým slovem AS) a umožní Cosmos DB vytvořit pole ID nebo nahraďte ID s hodnotou jiného sloupce (pomocí klíčového slova AS, nebo pomocí nastavení "ID dokumentu").

## <a name="data-partitioning-in-cosmos-db"></a>Dělení dat v databázi Cosmos DB
Azure Cosmos DB [neomezené](../cosmos-db/partition-data.md) kontejnery jsou doporučený postup pro dělení dat, jako Azure Cosmos DB automaticky škáluje oddíly na základě vašich úloh. Při zápisu do neomezené kontejnery, Stream Analytics používá libovolný počet paralelních zapisovače předchozí dotaz nebo vstupu pro krok schéma rozdělení oddílů.
> [!NOTE]
> V tuto chvíli je v Azure Stream Analytics podporuje jenom neomezené kontejnery se klíče oddílů na nejvyšší úrovni. Například `/region` je podporována. Vnořené klíče oddílů (třeba `/region/name`) nejsou podporovány. 

V závislosti na zvoleného klíče oddílu se může zobrazit tato _upozornění_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Je důležité, abyste zvolili klíčová vlastnost oddílu, který má několik různých hodnot a umožňuje rovnoměrně distribuuje úlohy mezi tyto hodnoty. Jako přirozeným dělení požadavků týkajících se stejným klíčem oddílu se uplatňuje limit vycházející maximální propustnost jednoho oddílu. Kromě toho velikost úložiště pro dokumenty, které patří do stejného klíče oddílu je omezené na 10GB. Klíčem oddílu ideální je ten, který zobrazuje často jako filtr v dotazech a má dostatek kardinalitu Ujistěte se, že je škálovatelné řešení.

Klíč oddílu je také hranice pro transakce v DocumentDB uložené procedury a triggery. Měli byste zvolit klíč oddílu tak, aby dokumenty, které společně probíhá transakce sdílet stejnou hodnotu klíče oddílu. Tento článek [dělení ve službě Cosmos DB](../cosmos-db/partitioning-overview.md) poskytuje další podrobnosti o zvolit klíč oddílu.

Stream Analytics pevnou kontejnery služby Azure Cosmos DB, umožňuje žádný způsob, jak škálování směrem nahoru nebo navýšení kapacity, jakmile se dostanou úplné. Mají maximální limit je 10 GB a propustnosti 10 000 RU/s.  Pokud chcete migrovat data z pevný kontejner do neomezeného kontejneru (například jeden s minimálně 1 000 RU/s a klíč oddílu), budete muset použít [nástroj pro migraci dat](../cosmos-db/import-data.md) nebo [změnit informační kanál knihovny](../cosmos-db/change-feed.md).

Možnost zapisovat do více kontejnerů pevné již není používán a nedoporučuje se používat pro horizontální navýšení kapacity vaší úlohy Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Lepší výkon s 1.2 úroveň kompatibility
S úrovní kompatibility 1.2 Stream Analytics podporuje nativní integraci k hromadnému zápisu do služby Cosmos DB. To umožňuje psaní efektivně ke službě Cosmos DB se maximalizuje propustnost a efektivně popisovač omezení požadavků. Vylepšené zápis mechanismus je k dispozici pod novou úroveň kompatibility kvůli rozdílu upsert chování.  Chování upsert je před 1.2, vložení nebo sloučení dokumentu. S 1.2 se mění chování upsertuje k vložení nebo nahrazení dokumentu. 

Před 1.2 používá vlastní uložené procedury k hromadné upsert dokumentů na klíč oddílu do služby Cosmos DB, kde je zapsán dávku jako transakce. I když narazí na jeden záznam o přechodnou chybu (omezení využití sítě), je nutné zopakovat celý batch. Kvůli tomu bylo scénáře s relativně pomaleji i přiměřené omezování. Po porovnání ukazuje chování těchto úloh s 1.2.

Následující příklad ukazuje dvě identické úlohy Stream Analytics pro čtení z stejný vstup Centrum událostí. Obě úlohy Stream Analytics jsou [plně rozdělit na oddíly](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) průchozí dotaz a zápis do stejné kontejnery služby cosmos DB. Metriky na levé straně jsou z úlohy nakonfigurována s úrovní kompatibility 1.0 a ta na pravé straně jsou nakonfigurovány s 1.2. Klíč oddílu kontejneru Cosmos DB je jedinečný identifikátor GUID z vstupní události.

![porovnání metrik služby Stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Počet příchozích událostí do centra událostí je 2 × vyšší než kontejnery služby Cosmos DB (20 tisíc ru) jsou nakonfigurovány pro příjem, takže omezení se očekává v Cosmos DB. Úlohy s 1.2, je však zápis konzistentně vyšší propustnost (výstup událostí za minutu) a s nižší průměrný % využití SU. Ve vašem prostředí bude tento rozdíl záviset na několika další faktory, jako je volba formátu událostí, velikost vstupní události a zprávy, klíče oddílu, dotaz atd.

![porovnání metrik cosmos db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2 Stream Analytics je inteligentnější v využívá 100 % k dispozici propustnosti ve službě Cosmos DB s velmi málo opakovaná odeslání z omezení využití sítě/rychlosti. To poskytuje lepší prostředí pro jiné úlohy, jako jsou dotazy ve stejnou dobu spuštění v kontejneru. Abyste si případně mohli vyzkoušet, jak Azure Stream Analytics horizontálně navýší kapacitu pomocí služby Cosmos DB jako jímka pro 1 do 10 tis. zpráv za sekundu, tady je [projekt ukázky v azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) , který umožňuje udělat.
Mějte prosím na paměti, že propustnost výstupu služby Cosmos DB je identická se změnou 1.0 a 1.1. Protože 1.2 není aktuálně výchozí nastavení, můžete [nastavit úroveň kompatibility](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) pro úlohu Stream Analytics pomocí portálu nebo pomocí [vytvořit úlohu volání rozhraní REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Má *důrazně doporučujeme* 1.2 úrovni kompatibility Azure Stream Analytics pomocí služby Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Nastavení služby cosmos DB pro výstup ve formátu JSON

Vytváření služby Cosmos DB jako výstup ve službě Stream Analytics generuje řádku informace, jak je vidět níže. Tato část obsahuje vysvětlení definici vlastnosti.

![documentdb stream analytics výstupní obrazovky](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Popis|
|-------------   | -------------|
|Alias pro výstup    | Alias pro najdete na tomto výstup v dotazu Azure Stream Analytics.|
|Předplatné    | Zvolte předplatné Azure.|
|Account ID      | Název nebo identifikátor URI účtu služby Azure Cosmos DB koncového bodu.|
|Klíč účtu     | Sdílený přístupový klíč pro účet služby Azure Cosmos DB.|
|Databáze        | Název databáze Azure Cosmos DB.|
|Název kontejneru | Název kontejneru, který se má použít. `MyContainer` je platný vstup ukázkové – jeden kontejner s názvem `MyContainer` musí existovat.  |
|ID dokumentu     | Volitelné. Název sloupce ve výstupních událostech používaný jako jedinečný klíč, na které insert nebo update musí být operace založené. Pokud pole ponecháte prázdné, všechny události se vloží, bez možnosti aktualizace.|
