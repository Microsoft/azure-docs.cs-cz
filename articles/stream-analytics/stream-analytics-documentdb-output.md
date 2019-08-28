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
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062016"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Výstup Azure Stream Analytics ke službě Azure Cosmos DB  
Stream Analytics můžete směrovat [služby Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) výstup ve formátu JSON, povolení archivace a s nízkou latencí dotazy na data nestrukturovaných dat JSON. Tento dokument uvádí i některé doporučené postupy pro implementaci této konfigurace.

Pro ty, kteří zkušenosti se službou Cosmos DB, podívejte se na [výuky pro služby Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) začít. 

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje jenom připojení ke službě Azure Cosmos DB pomocí **rozhraní SQL API**.
> Další rozhraní API služby Azure Cosmos DB se zatím nepodporují. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, nemusí být data uložená správně. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Základy služby Cosmos DB jako cíl výstupu
Výstup Azure Cosmos DB v Stream Analytics umožňuje zapisovat výsledky zpracování streamu jako výstup JSON do kontejnerů, které jsou Cosmos DB. Stream Analytics ve vaší databázi nevytváří kontejnery, ale vyžaduje, abyste je vytvořili předem. To je tak, aby náklady na Cosmos DB kontejnery byly řízeny vámi, a aby bylo možné vyladit výkon, konzistenci a kapacitu kontejnerů přímo pomocí [rozhraní api Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Do seznamu povolených IP adres z brány Azure Cosmos DB firewall musíte přidat 0.0.0.0.

Některé možnosti kontejneru Cosmos DB jsou podrobně popsané níže.

## <a name="tune-consistency-availability-and-latency"></a>Vyladění konzistencí, dostupností a latencí
Aby se shodovaly s požadavky vaší aplikace, Azure Cosmos DB vám umožní vyladit databáze a kontejnery a dělat kompromisy mezi konzistencí, dostupností, latencí a propustností. V závislosti na tom, jaké úrovně konzistence čtení vašim potřebám scénář pro čtení a zápis latenci, že můžete použít úrovně konzistence na váš účet databáze. Propustnost lze zlepšit škálováním jednotek žádostí (ru) v kontejneru. Ve výchozím nastavení Azure Cosmos DB také umožňuje synchronní indexování každé operace CRUD do vašeho kontejneru. Toto je další užitečná možnost řízení výkonu zápisu/čtení ve službě Azure Cosmos DB. Další informace najdete v článku [změnit úrovně konzistence vaší databáze a dotazu](../cosmos-db/consistency-levels.md) článku.

## <a name="upserts-from-stream-analytics"></a>Upsertuje ze Stream Analytics
Stream Analytics Integration with Azure Cosmos DB umožňuje do kontejneru vkládat nebo aktualizovat záznamy na základě zadaného sloupce s ID dokumentu. To se také označuje jako *Upsert*.

Stream Analytics používá optimistické upsert přístupu, kde aktualizace jsou pouze provedeno při vložení selže kvůli konfliktu ID dokumentu. S úrovní kompatibility 1,0 se tato aktualizace provádí jako oprava, takže umožňuje částečné aktualizace dokumentu. to znamená, že přidání nových vlastností nebo nahrazení stávající vlastnosti je provedeno přírůstkově. Však není sloučit změny hodnoty vlastnosti pole ve vašich výsledku dokumentu JSON v celého pole získávání přepsány, to znamená, pole. S 1,2 je chování Upsert upraveno pro vložení dokumentu nebo jeho nahrazení. To je popsáno dále v níže uvedené části úroveň kompatibility 1,2.

Pokud vstupní dokument JSON má existující pole ID, že pole je automaticky použít jako sloupec ID dokumentu ve službě Cosmos DB a jakékoli další zápisy jsou zpracovány jako takové, což vede k jednomu z těchto situací:
- Jedinečné ID vést k vložení
- duplicitní ID a "ID dokumentu' nastavena na"ID"vede má upsertovat
- duplicitní ID a 'ID dokumentu' není sada vede k chybě, po první dokument

Pokud chcete uložit <i>všechny</i> dokumenty, včetně těch s duplicitním ID přejmenovat pole ID v dotazu (s klíčovým slovem AS) a umožní Cosmos DB vytvořit pole ID nebo nahraďte ID s hodnotou jiného sloupce (pomocí klíčového slova AS, nebo pomocí nastavení "ID dokumentu").

## <a name="data-partitioning-in-cosmos-db"></a>Dělení dat v databázi Cosmos DB
Azure Cosmos DB [](../cosmos-db/partition-data.md) neomezenými kontejnery jsou doporučeným přístupem k vytváření oddílů dat, protože Azure Cosmos DB automaticky škálují oddíly na základě vašich úloh. Když zapisujete do neomezených kontejnerů, Stream Analytics použije jako předchozí krok dotazu a jako vstupní schéma dělení tolik paralelních zapisovačů.
> [!NOTE]
> V tuto chvíli Azure Stream Analytics podporuje jenom neomezené kontejnery s klíči oddílů na nejvyšší úrovni. Například `/region` je podporována. Vnořené klíče oddílů (třeba `/region/name`) nejsou podporovány. 

V závislosti na zvoleném klíči oddílu se může zobrazit toto _Upozornění_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Je důležité zvolit vlastnost klíče oddílu, která má několik různých hodnot, a umožňuje rovnoměrně distribuovat úlohy napříč těmito hodnotami. V rámci přirozeného artefaktu dělení jsou požadavky zahrnující stejný klíč oddílu omezené maximální propustností jednoho oddílu. Kromě toho je velikost úložiště pro dokumenty patřící do stejného klíče oddílu omezená na 10 GB. Ideální klíč oddílu je takový, který se často objevuje jako filtr v dotazech a má dostatečnou mohutnost pro zajištění škálovatelnosti vašeho řešení.

Klíč oddílu je také hranice pro transakce v uložených procedurách a triggerech DocumentDB. Měli byste zvolit klíč oddílu, aby dokumenty, ke kterým dojde společně v transakcích, sdílely stejnou hodnotu klíče oddílu. Článek [dělení Cosmos DB](../cosmos-db/partitioning-overview.md) obsahuje další podrobnosti o výběru klíče oddílu.

U pevných Azure Cosmos DB kontejnerů neumožňuje Stream Analytics horizontálního navýšení nebo navýšení kapacity směrem nahoru nebo dolů. Mají maximální limit je 10 GB a propustnosti 10 000 RU/s.  Pokud chcete migrovat data z pevný kontejner do neomezeného kontejneru (například jeden s minimálně 1 000 RU/s a klíč oddílu), budete muset použít [nástroj pro migraci dat](../cosmos-db/import-data.md) nebo [změnit informační kanál knihovny](../cosmos-db/change-feed.md).

Možnost zápisu do více pevných kontejnerů je zastaralá a nedoporučuje se pro horizontální navýšení kapacity Stream Analytics úlohy.

## <a name="improved-throughput-with-compatibility-level-12"></a>Vylepšená propustnost s úrovní kompatibility 1,2
S úrovní kompatibility 1,2 Stream Analytics podporuje nativní integraci pro hromadné zápisy do Cosmos DB. To umožňuje efektivně zapisovat Cosmos DB s maximalizací propustnosti a efektivně zpracovávat požadavky na omezování. Vylepšený mechanismus zápisu je k dispozici na nové úrovni kompatibility, protože se jedná o rozdíl v chování Upsert.  Před 1,2 je chování Upsert pro vložení nebo sloučení dokumentu. S 1,2 je chování upsertuje upraveno pro vložení dokumentu nebo jeho nahrazení.

Před 1,2 používá vlastní uloženou proceduru k hromadnému Upsert dokumentů na klíč oddílu do Cosmos DB, kde se dávka zapisuje jako transakce. I v případě, že jeden záznam narazí na přechodnou chybu (omezování), musí se celá dávka opakovat. To se provádí ve scénářích s ještě přiměřeným omezením relativně pomalejšího. Následující porovnání ukazuje, jak se tyto úlohy budou chovat s 1,2.

Následující příklad ukazuje dvě totožné úlohy Stream Analytics čtené ze stejného vstupu centra událostí. Obě úlohy Stream Analytics jsou [plně rozdělené](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) pomocí předávacího dotazu a zapisují se do identických kontejnerů CosmosDB. Metriky na levé straně jsou z úlohy nakonfigurované s úrovní kompatibility 1,0 a u nich napravo jsou nakonfigurované s 1,2. Klíč oddílu Cosmos DB kontejneru je jedinečný identifikátor GUID, který přichází ze vstupní události.

![porovnání metrik Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Míra příchozích událostí v centru událostí je 2x větší než Cosmos DB kontejnerů (20 tisíc ru) je nakonfigurovaná na příjem, takže se v Cosmos DB očekává omezení. Úloha s 1,2 se ale konzistentně zapisuje s vyšší propustností (výstupní události za minutu) a s nižším průměrem využití SU%. Tento rozdíl bude záviset na několika dalších faktorech, jako je například Volba formátu události, vstupní událost/velikost zprávy, klíče oddílu, dotaz atd.

![porovnání metrik Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

S 1,2 je Stream Analytics více inteligentních procesů, které využívají 100% dostupné propustnosti v Cosmos DB s velmi malým počtem opětovného odeslání od omezení/omezení rychlosti. To poskytuje lepší možnosti pro jiné úlohy, jako jsou dotazy běžící na kontejneru ve stejnou dobu. V případě, že potřebujete vyzkoušet, jak se má ASA škálovat Cosmos DB jako jímka pro 1 tisíc na 10 000 zpráv za sekundu, je tady [projekt ukázek Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) , který vám to umožňuje.
Upozorňujeme, že Cosmos DB výstupní propustnost je shodná s 1,0 a 1,1. Vzhledem k tomu, že 1,2 není aktuálně výchozí, můžete [nastavit úroveň kompatibility](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) pro úlohu Stream Analytics pomocí portálu nebo pomocí [volání Create Job REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). V ASA s Cosmos DB se *důrazně doporučuje* používat úroveň kompatibility 1,2.



## <a name="cosmos-db-settings-for-json-output"></a>Nastavení služby cosmos DB pro výstup ve formátu JSON

Vytváření služby Cosmos DB jako výstup ve službě Stream Analytics generuje řádku informace, jak je vidět níže. Tato část obsahuje vysvětlení definici vlastnosti.

![documentdb stream analytics výstupní obrazovky](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Popis|
|-------------   | -------------|
|Alias pro výstup    | Alias pro odkazování na tento výstup v dotazu ASA.|
|Subscription    | Vyberte předplatné Azure.|
|ID účtu      | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB.|
|Klíč účtu     | Sdílený přístupový klíč pro účet Azure Cosmos DB.|
|Databáze        | Azure Cosmos DB název databáze.|
|Název kontejneru | Název kontejneru, který se má použít. `MyContainer`musí existovat vzorový platný kontejner Input-One s `MyContainer` názvem.  |
|ID dokumentu     | Volitelné. Název sloupce ve výstupních událostech používaný jako jedinečný klíč, na které insert nebo update musí být operace založené. Pokud pole ponecháte prázdné, všechny události se vloží, bez možnosti aktualizace.|

## <a name="error-handling-and-retries"></a>Zpracování chyb a opakování

V případě přechodného selhání, nedostupnost služby nebo omezování při posílání událostí do Cosmos DB Stream Analytics opakované pokusy o úspěšné dokončení této operace. Existuje však několik chyb, u kterých se pokus o opakování neprovede. Jedná se o následující chyby:

- Neautorizováno (kód chyby HTTP 401)
- NotFound (kód chyby HTTP 404)
- Zakázáno (kód chyby HTTP 403)
- Důvodu chybného požadavku (kód chyby HTTP 400)
