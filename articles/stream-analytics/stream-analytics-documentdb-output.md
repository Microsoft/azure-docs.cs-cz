---
title: Výstup Azure Stream Analytics do Azure Cosmos DB
description: Tento článek popisuje, jak pomocí Azure Stream Analytics uložit výstup do Azure Cosmos DB pro výstup JSON, pro archivaci dat a dotazy s nízkou latencí na nestrukturovaná data JSON.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254440"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Výstup Azure Stream Analytics do Azure Cosmos DB  
Azure Stream Analytics může cílit [na Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) pro výstup JSON, což umožňuje archivaci dat a dotazy s nízkou latencí na nestrukturovaná data JSON. Tento dokument obsahuje některé osvědčené postupy pro implementaci této konfigurace.

Pokud nejste obeznámeni s Azure Cosmos DB, najdete v [dokumentaci k Azure Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) abyste mohli začít. 

> [!Note]
> V tuto chvíli Stream Analytics podporuje připojení k Azure Cosmos DB pouze prostřednictvím *rozhraní SQL API*.
> Ostatní azure cosmos DB API ještě nejsou podporovány. Pokud naminete Stream Analytics na účty Azure Cosmos DB vytvořené pomocí jiných api, data nemusí být správně uložena. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Základy Azure Cosmos DB jako výstupní cíl
Výstup Azure Cosmos DB v Stream Analytics umožňuje zápis výsledků zpracování datového proudu jako výstup JSON do kontejnerů Azure Cosmos DB. 

Stream Analytics nevytváří kontejnery ve vaší databázi. Místo toho vyžaduje, abyste je vytvořili předem. Potom můžete řídit fakturační náklady kontejnerů Azure Cosmos DB. Výkon, konzistenci a kapacitu kontejnerů můžete také vyladit přímo pomocí [api azure cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Musíte přidat 0.0.0.0 do seznamu povolených IP adresy z brány firewall Azure Cosmos DB.

V následujících částech jsou podrobně popsány některé možnosti kontejneru pro Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Konzistence, dostupnost a latence ladění
Aby odpovídaly vašim požadavkům na aplikace, Azure Cosmos DB umožňuje doladit databázi a kontejnery a provádět kompromisy mezi konzistencí, dostupností, latencí a propustností. 

V závislosti na tom, jaké úrovně konzistence čtení váš scénář potřebuje proti čtení a zápisu latence, můžete zvolit úroveň konzistence na databázovém účtu. Propustnost můžete zlepšit škálováním jednotek požadavků (RU) v kontejneru. 

Azure Cosmos DB také ve výchozím nastavení umožňuje synchronní indexování při každé operaci CRUD do kontejneru. To je další užitečná možnost řízení výkonu zápisu a čtení v Azure Cosmos DB. 

Další informace naleznete v článku [Změna úrovně konzistence databáze a dotazu.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Upserts z Stream Analytics
Integrace Stream Analytics s Azure Cosmos DB umožňuje vložit nebo aktualizovat záznamy v kontejneru na základě daného sloupce **ID dokumentu.** To se také nazývá *upsert*.

Stream Analytics používá optimistický upsert přístup. K aktualizacím dochází pouze v případě, že vložení selže s konfliktem ID dokumentu. 

S úrovní kompatibility 1.0 stream analytics provádí tuto aktualizaci jako operaci PATCH, takže umožňuje částečné aktualizace dokumentu. Stream Analytics přidává nové vlastnosti nebo postupně nahrazuje existující vlastnosti. Změny v hodnotách vlastností pole v dokumentu JSON však mají za následek přepsání celého pole. To znamená, že pole není sloučeno. 

S 1.2 je chování upsert upraveno tak, aby vkládalo nebo nahrazovalo dokument. Toto chování dále popisuje další část o úrovni kompatibility 1.2.

Pokud příchozí dokument JSON má existující pole ID, toto pole se automaticky použije jako sloupec **ID dokumentu** v Azure Cosmos DB. Všechny následné zápisy jsou zpracovány jako takové, což vede k jedné z těchto situací:

- Jedinečná ID vedou k vložení.
- Duplicitní ID a **ID dokumentu nastavené** na **ID** vedou k upsert.
- Duplicitní ID a **ID dokumentu** nenastavili po prvním dokumentu zájemce na chybu.

Pokud chcete uložit *všechny* dokumenty, včetně těch, které mají duplicitní ID, přejmenujte pole ID v dotazu (pomocí klíčového slova **AS).** Nechte Azure Cosmos DB vytvořit pole ID nebo nahraďte ID hodnotou jiného sloupce (pomocí klíčového slova **AS** nebo pomocí nastavení **ID dokumentu).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Dělení dat v Azure Cosmos DB
Azure Cosmos DB automaticky škáluje oddíly na základě vaší úlohy. Proto doporučujeme [neomezené](../cosmos-db/partition-data.md) kontejnery jako přístup k rozdělení dat. Když Stream Analytics zapisuje do neomezených kontejnerů, používá tolik paralelních zapisovačů jako předchozí krok dotazu nebo vstupní schéma dělení.

> [!NOTE]
> Azure Stream Analytics podporuje jenom neomezené kontejnery s klíči oddílů na nejvyšší úrovni. Například `/region` je podporován. Vnořené klíče oddílu `/region/name`(například) nejsou podporovány. 

V závislosti na zvoleném klíči oddílu se může zobrazit toto _upozornění_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Je důležité zvolit vlastnost klíče oddílu, která má řadu různých hodnot a která umožňuje rovnoměrně distribuovat úlohy mezi tyto hodnoty. Jako přirozený artefakt dělení požadavky, které se týkají stejného klíče oddílu jsou omezeny maximální propustnost íjednoho oddílu. 

Velikost úložiště pro dokumenty, které patří ke stejnému klíči oddílu, je omezena na 10 GB. Ideální klíč oddílu je ten, který se často zobrazuje jako filtr v dotazech a má dostatečnou mohutnost k zajištění, že vaše řešení je škálovatelné.

Klíč oddílu je také hranice pro transakce v uložené procedury a aktivační události pro Azure Cosmos DB. Měli byste zvolit klíč oddílu tak, aby dokumenty, které se vyskytují společně v transakcích, sdílely stejnou hodnotu klíče oddílu. V článku [dělení v Azure Cosmos DB](../cosmos-db/partitioning-overview.md) poskytuje další podrobnosti o výběru klíče oddílu.

Pro pevné kontejnery Azure Cosmos DB Stream Analytics neumožňuje žádný způsob, jak vertikálně navýšit kapacitu nebo ven po jejich úplné. Mají horní limit 10 GB a 10 000 RU/s propustnost. Chcete-li migrovat data z pevného kontejneru do neomezeného kontejneru (například s alespoň 1 000 RU/s a klíčem oddílu), použijte [nástroj pro migraci dat](../cosmos-db/import-data.md) nebo [knihovnu zdrojů změn](../cosmos-db/change-feed.md).

Schopnost zapisovat do více pevných kontejnerů se zastaralá. Nedoporučujeme ji pro horizontální navýšení kapacity úlohy Služby Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Vylepšená propustnost s úrovní kompatibility 1.2
S úrovní kompatibility 1.2 stream analytics podporuje nativní integraci pro hromadný zápis do Azure Cosmos DB. Tato integrace umožňuje efektivně psát do Azure Cosmos DB při maximalizaci propustnost a efektivně zpracování požadavků omezení. 

Vylepšený mechanismus zápisu je k dispozici pod novou úroveň kompatibility z důvodu rozdílu v chování upsert. S úrovněmi před 1.2 je chování upsert vložit nebo sloučit dokument. S 1.2 je chování upsert upraveno tak, aby vkládalo nebo nahrazovalo dokument.

S úrovněmi před 1.2 stream analytics používá vlastní uloženou proceduru k hromadnému upsert dokumentů na klíč oddílu do Azure Cosmos DB. Tam je dávka zapsána jako transakce. I v případě, že jeden záznam narazí na přechodnou chybu (omezení), musí být zopakována celá dávka. Díky scénářům s i rozumné omezení relativně pomalé.

Následující příklad ukazuje dvě identické úlohy Stream Analytics čtení ze stejného vstupu Centra událostí Azure. Obě úlohy Stream Analytics jsou [plně rozděleny](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) pomocí průchozího dotazu a zapisují do identických kontejnerů Azure Cosmos DB. Metriky vlevo jsou z úlohy nakonfigurované s úrovní kompatibility 1.0. Metriky vpravo jsou konfigurovány s 1.2. Klíč oddílu kontejneru Azure Cosmos DB je jedinečný identifikátor GUID, který pochází ze vstupní události.

![Porovnání metrik Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Rychlost příchozích událostí v rozbočovačích událostí je dvakrát vyšší než kontejnery Azure Cosmos DB (20 000 ru) jsou nakonfigurované pro příjem, takže omezení se očekává v Azure Cosmos DB. Úloha s 1.2 je však konzistentně zápis na vyšší propustnost (výstupní události za minutu) a s nižšíprůměrné využití SU%. Ve vašem prostředí bude tento rozdíl záviset na několika dalších faktorech. Mezi tyto faktory patří volba formátu události, velikost i velikost vstupní události/zprávy, klíče oddílu a dotaz.

![Porovnání metrik Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

S 1.2 je Stream Analytics inteligentnější při využití 100 procent dostupné propustnosti v Azure Cosmos DB s velmi málo resubmissions z omezení nebo omezení rychlosti. To poskytuje lepší prostředí pro jiné úlohy, jako jsou dotazy spuštěné v kontejneru ve stejnou dobu. Pokud chcete vidět, jak se streamanalytics škáluje s Azure Cosmos DB jako jímkou pro 1 000 až 10 000 zpráv za sekundu, vyzkoušejte [tento ukázkový projekt Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

Propustnost výstupu Azure Cosmos DB je shodná s 1.0 a 1.1. *Důrazně doporučujeme* používat úroveň kompatibility 1.2 v Stream Analytics s Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Nastavení Azure Cosmos DB pro výstup JSON

Použití Azure Cosmos DB jako výstupu v Stream Analytics generuje následující výzvu k zadání informací.

![Informační pole pro výstupní datový proud Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Popis|
|-------------   | -------------|
|Alias pro výstup    | Alias odkazující na tento výstup v dotazu Stream Analytics.|
|Předplatné    | Předplatné Azure.|
|Account ID      | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB.|
|Klíč účtu     | Sdílený přístupový klíč pro účet Azure Cosmos DB.|
|Databáze        | Název databáze Azure Cosmos DB.|
|Název kontejneru | Název kontejneru, `MyContainer`například . Jeden kontejner `MyContainer` s názvem musí existovat.  |
|ID dokumentu     | Nepovinný parametr. Název sloupce ve výstupních událostech používaný jako jedinečný klíč, na kterém musí být založeny operace vložení nebo aktualizace. Pokud jej ponecháte prázdný, budou vloženy všechny události bez možnosti aktualizace.|

Po konfiguraci výstupu Azure Cosmos DB, můžete jej použít v dotazu jako cíl [příkazu INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Pokud používáte výstup Azure Cosmos DB tímto způsobem, [musí být explicitně nastaven klíč oddílu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

Výstupní záznam musí obsahovat sloupec rozlišující malá a velká písmena pojmenovaný za klíčem oddílu v Azure Cosmos DB. Chcete-li dosáhnout větší paralelizace, příkaz může vyžadovat [klauzuli PARTITION BY,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) která používá stejný sloupec.

Zde je ukázkový dotaz:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Zpracování chyb a opakování

Pokud dojde k přechodné selhání, nedostupnosti služby nebo omezení, když Stream Analytics odesílá události do Azure Cosmos DB, Stream Analytics se opakuje neomezeně dlouho, aby úspěšně dokončil operaci. Ale nepokouší se o opakování následujících selhání:

- Neautorizováno (kód chyby HTTP 401)
- NotFound (kód chyby HTTP 404)
- Zakázáno (kód chyby HTTP 403)
- BadRequest (kód chyby HTTP 400)
