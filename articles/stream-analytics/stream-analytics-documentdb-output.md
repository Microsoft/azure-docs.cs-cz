---
title: Azure Stream Analytics výstup do Azure Cosmos DB
description: Tento článek popisuje, jak pomocí Azure Stream Analytics uložit výstup do Azure Cosmos DB pro výstup JSON, pro archivaci dat a pro dotazy s nízkou latencí v nestrukturovaných datech JSON.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 2d00d489ff248ecf5599d78e0a351c93248cf8ee
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018085"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics výstup do Azure Cosmos DB  
Azure Stream Analytics může cílit [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) na výstup JSON, povolit archivaci dat a dotazy s nízkou latencí na nestrukturovaná data JSON. Tento dokument popisuje některé osvědčené postupy pro implementaci této konfigurace. Doporučujeme, abyste nastavili úlohu na úroveň kompatibility 1,2 při použití Azure Cosmos DB jako výstupu.

Pokud nejste obeznámeni s Azure Cosmos DB, přečtěte si téma [dokumentace Azure Cosmos DB](../cosmos-db/index.yml) a začněte. 

> [!Note]
> V tuto chvíli Stream Analytics podporuje připojení k Azure Cosmos DB jenom přes *rozhraní SQL API*.
> Jiná rozhraní API Azure Cosmos DB ještě nejsou podporovaná. Pokud Stream Analytics naAzure Cosmos DB účty vytvořené pomocí jiných rozhraní API, nemusí být data správně uložená. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Základy Azure Cosmos DB jako cíle výstupu
Výstup Azure Cosmos DB v Stream Analytics umožňuje zapisovat výsledky zpracování streamu jako výstup JSON do kontejnerů Azure Cosmos DB. 

Stream Analytics ve vaší databázi nevytváří kontejnery. Místo toho vyžaduje, abyste je vytvořili předem. Pak můžete řídit náklady na fakturaci Azure Cosmos DB kontejnerů. Výkon, konzistenci a kapacitu kontejnerů můžete také ladit přímo pomocí [rozhraní api Azure Cosmos DB](/rest/api/cosmos-db/).

> [!Note]
> Do seznamu povolených IP adres z brány Azure Cosmos DB firewall musíte přidat 0.0.0.0.

Následující části obsahují podrobné informace o možnostech kontejneru pro Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Optimalizace konzistence, dostupnosti a latence
Aby se shodovaly s požadavky vaší aplikace, Azure Cosmos DB vám umožní vyladit databáze a kontejnery a dělat kompromisy mezi konzistencí, dostupností, latencí a propustností. 

V závislosti na tom, jaké úrovně konzistence čtení vaše situace potřebuje k latenci čtení a zápisu, můžete zvolit úroveň konzistence v databázovém účtu. Propustnost můžete zlepšit škálováním jednotek žádostí (ru) v kontejneru. 

Ve výchozím nastavení Azure Cosmos DB také umožňuje synchronní indexování každé operace CRUD do vašeho kontejneru. Toto je další užitečnou možností pro řízení výkonu zápisu a čtení v Azure Cosmos DB. 

Další informace najdete v článku [Změna úrovně konzistence databáze a dotazu](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upsertuje z Stream Analytics
Stream Analytics Integration with Azure Cosmos DB umožňuje do kontejneru vkládat nebo aktualizovat záznamy na základě zadaného sloupce s **ID dokumentu** . Tento název se také označuje jako *Upsert*.

Stream Analytics používá optimistický přístup k Upsert. K aktualizacím dochází pouze v případě, že se operace INSERT nezdařila s konfliktem ID dokumentu. 

S úrovní kompatibility 1,0 Stream Analytics provádí tuto aktualizaci jako operaci opravy, takže umožňuje částečné aktualizace dokumentu. Stream Analytics přidává nové vlastnosti nebo nahradí stávající vlastnost přírůstkově. Změny v hodnotách vlastností pole v dokumentu JSON ale způsobí přepsání celého pole. To znamená, že pole není sloučeno. 

S 1,2 je chování Upsert upraveno pro vložení dokumentu nebo jeho nahrazení. Další část o úrovni kompatibility 1,2 dále popisuje toto chování.

Pokud má příchozí dokument JSON existující pole ID, toto pole se automaticky použije jako sloupec **ID dokumentu** v Azure Cosmos DB. Jakékoli následné zápisy jsou zpracovávány jako takové, což vede k jedné z těchto situací:

- Jedinečné identifikátory mají za následek vložení.
- Duplicitní ID a **ID dokumentu** nastavené na **ID** vede k Upsert.
- V případě, že se v prvním dokumentu nenastaví duplicitní ID a **ID dokumentu** , došlo k chybě.

Pokud chcete uložit *všechny* dokumenty včetně těch, které mají duplicitní ID, přejmenujte pole ID v dotazu (pomocí klíčového slova **as** ). Nechejte Azure Cosmos DB vytvořit pole ID nebo nahradit ID jinou hodnotou sloupce (pomocí klíčového slova **as** nebo pomocí nastavení **ID dokumentu** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Dělení dat v Azure Cosmos DB
Azure Cosmos DB automaticky škálují oddíly na základě vašich úloh. Proto doporučujeme [neomezeným](../cosmos-db/partitioning-overview.md) kontejnerům jako metodu pro dělení dat. Když Stream Analytics zapisuje do neomezených kontejnerů, používá jako předchozí krok dotazu nebo vstupní schéma dělení tolik paralelních zapisovačů.

> [!NOTE]
> Azure Stream Analytics podporuje v nejvyšší úrovni pouze neomezený počet kontejnerů s klíči oddílů. Například `/region` je podporován. Vnořené klíče oddílů (například `/region/name` ) nejsou podporovány. 

V závislosti na zvoleném klíči oddílu se může zobrazit toto _Upozornění_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Je důležité zvolit vlastnost klíče oddílu, která má několik různých hodnot a která umožňuje rovnoměrně distribuovat úlohy napříč těmito hodnotami. V rámci přirozeného artefaktu dělení jsou požadavky, které zahrnují stejný klíč oddílu, omezené maximální propustností jednoho oddílu. 

Velikost úložiště pro dokumenty, které patří do stejné hodnoty klíče oddílu, je omezená na 20 GB ( [omezení velikosti fyzického oddílu](../cosmos-db/partitioning-overview.md) je 50 GB). [Ideální klíč oddílu](../cosmos-db/partitioning-overview.md#choose-partitionkey) je takový, který se často objevuje jako filtr ve vašich dotazech a má dostatečnou mohutnost pro zajištění škálovatelnosti vašeho řešení.

Klíče oddílů používané pro Stream Analytics dotazy a Cosmos DB nemusejí být identické. Plně paralelní topologie doporučuje použít *klíč vstupního oddílu*, `PartitionId` jako klíč oddílu Stream Analyticsho dotazu, ale nemusí být doporučená volba pro klíč oddílu Cosmos DB kontejneru.

Klíč oddílu je také hranice pro transakce v uložených procedurách a triggerech pro Azure Cosmos DB. Měli byste zvolit klíč oddílu, aby dokumenty, ke kterým dojde společně v transakcích, sdílely stejnou hodnotu klíče oddílu. Článek [dělení Azure Cosmos DB](../cosmos-db/partitioning-overview.md) obsahuje další podrobnosti o výběru klíče oddílu.

U pevných Azure Cosmos DB kontejnerů Stream Analytics neumožňuje horizontální navýšení nebo navýšení kapacity směrem nahoru nebo dolů. Mají horní limit 10 GB a 10 000 RU/s propustnost. Chcete-li migrovat data z pevného kontejneru do neomezeného kontejneru (například jeden s minimálně 1 000 RU/s a klíč oddílu), použijte [Nástroj pro migraci dat](../cosmos-db/import-data.md) nebo [knihovnu změn kanálu](../cosmos-db/change-feed.md).

Možnost zápisu do více pevných kontejnerů je zastaralá. Nedoporučujeme ho pro horizontální navýšení kapacity Stream Analytics úlohy.

## <a name="improved-throughput-with-compatibility-level-12"></a>Vylepšená propustnost s úrovní kompatibility 1,2
S úrovní kompatibility 1,2 Stream Analytics podporuje nativní integraci pro hromadné zápisy do Azure Cosmos DB. Tato integrace umožňuje efektivně zapisovat Azure Cosmos DB a současně maximalizovat propustnost a efektivně zpracovávat požadavky na omezování. 

Vylepšený mechanismus zápisu je k dispozici na nové úrovni kompatibility kvůli rozdílu v chování Upsert. V případě úrovní před 1,2 je chování Upsert pro vložení nebo sloučení dokumentu. S 1,2 je chování Upsert upraveno pro vložení dokumentu nebo jeho nahrazení.

S úrovněmi před 1,2 Stream Analytics používá vlastní uloženou proceduru k hromadnému Upsert dokumentů na klíč oddílu do Azure Cosmos DB. Zde se zapisuje dávka jako transakce. I v případě, že jeden záznam narazí na přechodnou chybu (omezování), musí se celá dávka opakovat. Díky tomu mají scénáře i přiměřené omezení poměrně pomalu.

Následující příklad ukazuje dvě stejné úlohy Stream Analytics čtené ze stejného vstupu Azure Event Hubs. Obě úlohy Stream Analytics jsou [plně rozdělené](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) pomocí předávacího dotazu a zapisují je do identických Azure Cosmos DB kontejnerů. Metriky na levé straně jsou z úlohy nakonfigurované s úrovní kompatibility 1,0. Metriky na pravé straně mají nakonfigurovanou 1,2. Klíč oddílu Azure Cosmos DB kontejneru je jedinečný identifikátor GUID, který pochází ze vstupní události.

![Porovnání metrik Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Míra příchozích událostí v Event Hubs je dvakrát větší než Azure Cosmos DB kontejnery (20 000 ru), které jsou nakonfigurovány tak, aby pomohly být v nástroji, takže se v Azure Cosmos DB očekává omezení. Úloha s 1,2 se ale konzistentně zapisuje s vyšší propustností (výstup událostí za minutu) a s nižším průměrem využití SU%. Tento rozdíl bude v prostředí záviset na několika dalších faktorech. Mezi tyto faktory patří Volba formátu události, vstupní událost/velikost zprávy, klíče oddílu a dotaz.

![Porovnání metrik Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

S 1,2 je Stream Analytics více inteligentních procesů, které využívají 100 procent dostupné propustnosti v Azure Cosmos DB s velmi malým počtem opětovného odeslání od omezení nebo omezení rychlosti. To poskytuje lepší možnosti pro jiné úlohy, jako jsou dotazy běžící na kontejneru ve stejnou dobu. Pokud chcete zjistit, jak Stream Analytics škálovat s Azure Cosmos DB jako jímku pro 1 000 až 10 000 zpráv za sekundu, vyzkoušejte  [Tento ukázkový projekt Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb).

Propustnost výstupu Azure Cosmos DB je shodná s 1,0 a 1,1. *Důrazně doporučujeme* , abyste v Stream Analytics s Azure Cosmos DB používali úroveň kompatibility 1,2.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Nastavení Azure Cosmos DB pro výstup JSON

Použití Azure Cosmos DB jako výstupu v Stream Analytics generuje následující výzvu k zadání informací.

![Informační pole pro výstupní datový proud Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Popis|
|-------------   | -------------|
|Alias pro výstup    | Alias pro odkazování na tento výstup v dotazu Stream Analytics.|
|Předplatné    | Předplatné Azure.|
|Account ID      | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB.|
|Klíč účtu     | Sdílený přístupový klíč pro účet Azure Cosmos DB.|
|Databáze        | Azure Cosmos DB název databáze.|
|Název kontejneru | Název kontejneru, například `MyContainer` . Musí existovat jeden kontejner s názvem `MyContainer` .  |
|ID dokumentu     | Nepovinný parametr. Název sloupce ve výstupních událostech použitý jako jedinečný klíč, na kterém musí být operace INSERT nebo Update založená. Pokud necháte pole prázdné, budou vloženy všechny události bez možnosti aktualizace.|

Jakmile nakonfigurujete Azure Cosmos DB výstup, můžete ho použít v dotazu jako cíl [příkazu into](/stream-analytics-query/into-azure-stream-analytics). Pokud používáte výstup Azure Cosmos DB, je [třeba nastavit klíč oddílu explicitně](./stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). 

Výstupní záznam musí obsahovat sloupec s rozlišováním velkých a malých písmen pojmenovaný za klíčem oddílu v Azure Cosmos DB. Pro dosažení většího paralelismu může příkaz vyžadovat [klauzuli partition by](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) , která používá stejný sloupec.

Tady je ukázkový dotaz:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Zpracování chyb a opakování

Pokud dojde k přechodnému selhání, nedostupnost služby nebo omezování, když Stream Analytics odesílá události do Azure Cosmos DB, Stream Analytics opakované pokusy o úspěšné dokončení operace. Ale nedojde k pokusu o opakování následujících selhání:

- Neautorizováno (kód chyby HTTP 401)
- NotFound (kód chyby HTTP 404)
- Zakázáno (kód chyby HTTP 403)
- Důvodu chybného požadavku (kód chyby HTTP 400)

## <a name="common-issues"></a>Běžné problémy

1. Do kolekce je přidáno jedinečné omezení indexu a výstupní data z Stream Analytics porušují toto omezení. Zajistěte, aby výstupní data z Stream Analytics neporušila jedinečná omezení nebo odebrala omezení. Další informace najdete v tématu [omezení jedinečnosti klíčů v Azure Cosmos DB](../cosmos-db/unique-keys.md).

2. `PartitionKey`Sloupec neexistuje.

3. `Id`Sloupec neexistuje.

## <a name="next-steps"></a>Další kroky

* [Porozumění výstupům z Azure Stream Analytics](stream-analytics-define-outputs.md) 
* [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Azure Stream Analytics vlastní dělení výstupu objektů BLOB](stream-analytics-custom-path-patterns-blob-storage-output.md)