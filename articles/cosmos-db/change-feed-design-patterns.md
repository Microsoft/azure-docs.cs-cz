---
title: Změna vzorů návrhu informačního kanálu v Azure Cosmos DB
description: Přehled běžných vzorů návrhu kanálu změn
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334561"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Změna vzorů návrhu informačního kanálu v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kanál změny Azure Cosmos DB umožňuje efektivní zpracování velkých datových sad s velkým objemem zápisů. Kanál změn také nabízí alternativu k dotazování celé datové sady, aby bylo možné zjistit, co se změnilo. Tento dokument se zaměřuje na běžné vzorce pro změnu kanálu, návrhy kompromisů a omezení pro změnu kanálu.

Azure Cosmos DB je vhodný pro aplikace pro IoT, hraní, maloobchodní a provozní protokolování. Běžným vzorem návrhu v těchto aplikacích je použití změn dat pro aktivaci dalších akcí. Mezi další akce patří:

* Aktivace oznámení nebo volání rozhraní API, když je položka vložena nebo aktualizována.
* Zpracování datových proudů v reálném čase pro práci v reálném čase a zpracování analýz na provozních datech.
* Přesun dat, jako je třeba synchronizace s mezipamětí, vyhledávacím modulem, datovým skladem nebo studeným úložištěm.

Informační kanál změny v Azure Cosmos DB umožňuje vytváření efektivních a škálovatelných řešení pro každý z těchto vzorů, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Použití Azure Cosmos DB změn kanálu k napájení analýz v reálném čase a výpočetních scénářů založených na událostech" border="false":::

## <a name="event-computing-and-notifications"></a>Výpočet a oznámení událostí

Kanál změny Azure Cosmos DB může zjednodušit scénáře, které vyžadují spuštění oznámení nebo odeslání volání rozhraní API na základě určité události. [Knihovnu procesů změn kanálu](change-feed-processor.md) můžete použít k automatickému dotazování kontejneru na změny a volání externího rozhraní API pokaždé, když je k dispozici zápis nebo aktualizace.

Můžete také selektivně aktivovat oznámení nebo poslat volání rozhraní API na základě určitých kritérií. Pokud například čtete z kanálu změn pomocí [Azure Functions](change-feed-functions.md), můžete do funkce Vložit logiku, která odešle pouze oznámení v případě splnění konkrétního kritéria. I když se kód funkce Azure spustí během každého zápisu a aktualizace, oznámení se odešle jenom v případě, že byla splněna specifická kritéria.

## <a name="real-time-stream-processing"></a>Zpracování datových proudů v reálném čase

Kanál změny Azure Cosmos DB lze použít pro zpracování datových proudů v reálném čase pro zpracování dat IoT nebo v reálném čase pro provozní data.
Můžete například přijímat a ukládat data událostí ze zařízení, senzorů, infrastruktury a aplikací a zpracovávat tyto události v reálném čase pomocí [Sparku](../hdinsight/spark/apache-spark-overview.md). Následující obrázek ukazuje, jak můžete implementovat architekturu lambda pomocí Azure Cosmos DB přes změnu kanálu:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Azure Cosmos DB kanál lambda založený na ingestování a dotazování" border="false":::

V mnoha případech implementace zpracování datových proudů nejprve obdrží velký objem příchozích dat do dočasné fronty zpráv, jako je například Azure Event hub nebo Apache Kafka. Kanál změn je skvělou alternativou, protože Azure Cosmos DB schopnost podporovat trvalou vysokou míru příjmu dat s garantovanou nízkou latencí čtení a zápisu. Mezi výhody Azure Cosmos DBho kanálu změny v rámci fronty zpráv patří:

### <a name="data-persistence"></a>Trvalost dat

Data zapsaná do Azure Cosmos DB se zobrazí v kanálu změn a budou se uchovávat až po odstranění. Fronty zpráv mají obvykle maximální dobu uchování. Například [centrum událostí Azure](https://azure.microsoft.com/services/event-hubs/) nabízí maximální dobu uchovávání dat 90 dnů.

### <a name="querying-ability"></a>Dotazování na schopnost

Kromě čtení z kanálu změn kontejneru Cosmos můžete také spouštět dotazy SQL s daty uloženými v Azure Cosmos DB. Kanál změn není duplikace dat, která již v kontejneru existují, ale pouze k jinému mechanismu čtení dat. Proto pokud načtete data z kanálu změn, bude vždy konzistentní s dotazy stejného Azure Cosmos DB kontejneru.

### <a name="high-availability"></a>Vysoká dostupnost

Azure Cosmos DB nabízí až 99,999% dostupnost čtení a zápisu. Na rozdíl od mnoha front zpráv můžete Azure Cosmos DB data snadno globálně distribuovat a nakonfigurovat pomocí [RTO (plánovaná doba obnovení)](./consistency-levels.md#rto) nula.

Po zpracování položek v kanálu změn můžete vytvořit materializované zobrazení a zachovat agregované hodnoty zpátky v Azure Cosmos DB. Pokud používáte Azure Cosmos DB k vytvoření hry, můžete například použít kanál Change k implementaci žebříčky v reálném čase na základě skóre z dokončených her.

## <a name="data-movement"></a>Přesuny dat

Můžete si také přečíst z kanálu změn pro přesun dat v reálném čase.

Například kanál změny vám pomůže efektivně provádět následující úlohy:

* Aktualizujte mezipaměť, index vyhledávání nebo datový sklad s daty uloženými v Azure Cosmos DB.

* Proveďte žádné migrace za provozu do jiného účtu Azure Cosmos nebo jiného kontejneru Azure Cosmos s jiným klíčem logického oddílu.

* Implementujte a archivujte datovou vrstvu na úrovni aplikace. Například můžete ukládat "Hot data" v Azure Cosmos DB a stáří "studená data" do jiných systémů úložiště, jako je [Azure Blob Storage](../storage/common/storage-introduction.md).

Pokud potřebujete [denormalizovat data mezi oddíly a kontejnery](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), můžete si z kanálu změn kontejneru přečíst jako zdroj pro replikaci dat. Replikace dat v reálném čase pomocí kanálu změn může zaručit pouze konečnou konzistenci. Můžete [sledovat, jak daleko se procesor Change feed prodlevy za](how-to-use-change-feed-estimator.md) zpracování změn v kontejneru Cosmos.

## <a name="event-sourcing"></a>Původ události

[Vzor zdroje událostí](/azure/architecture/patterns/event-sourcing) zahrnuje použití úložiště pouze pro připojení k zaznamenání celé řady akcí na těchto datech. Informační kanál změn Azure Cosmos DB je skvělou volbou jako centrální úložiště dat v architekturách pro zdrojové události, kde se veškerá příjem dat modelují jako zápisy (bez aktualizací nebo odstranění). V takovém případě každý zápis do Azure Cosmos DB je "Event" a v kanálu změn budete mít úplný záznam o minulých událostech. Typická použití událostí publikovaných centrálním úložištěm událostí slouží ke správě materializovaná zobrazení nebo k integraci s externími systémy. Vzhledem k tomu, že v kanálu změn není časový limit pro uchovávání dat, můžete přehrát všechny minulé události čtením ze začátku kanálu změn kontejneru Cosmos.

[Ke stejnému kanálu změn kontejneru můžete přihlašovat více uživatelů kanálu změn](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Z poskytnuté propustnosti [kontejneru zapůjčení](change-feed-processor.md#components-of-the-change-feed-processor) není k dispozici žádné náklady na využití kanálu změn. Kanál změn je k dispozici v každém kontejneru bez ohledu na to, zda je využíván.

Azure Cosmos DB je skvělé centrální úložiště dat, které je k dispozici pouze v případě, že se jedná o síly v horizontální škálovatelnosti a vysoké dostupnosti. Kromě toho knihovna Change feed Processor nabízí jistotu ["alespoň jednou"](change-feed-processor.md#error-handling) , což zajistí, že nebudete moct zpracovávat žádné události.

## <a name="current-limitations"></a>Aktuální omezení

Kanál změn má důležitá omezení, která byste měli pochopit. I když položky v kontejneru Cosmos budou vždycky zůstat v kanálu změn, kanál změn není úplný protokol operací. Při navrhování aplikace, která využívá kanál změn, je třeba vzít v úvahu důležité oblasti.

### <a name="intermediate-updates"></a>Mezilehlé aktualizace

V kanálu změn je zahrnutá jenom Poslední změna dané položky. Při zpracování změn si přečtete nejnovější dostupnou verzi položky. Pokud je v krátké době k dispozici více aktualizací stejné položky, je možné zpracovat průběžné aktualizace. Pokud chcete sledovat aktualizace a budete moci znovu přehrát minulé aktualizace položky, doporučujeme místo toho modelování těchto aktualizací jako řady operací zápisu.

### <a name="deletes"></a>Odstraníte

Kanál změn nezachycuje odstranění. Pokud odstraníte položku z kontejneru, odebere se i z kanálu změn. Nejběžnější metodou manipulace je přidání měkké značky k položkám, které jsou odstraňovány. Můžete přidat vlastnost s názvem "odstraněno" a nastavit ji na hodnotu "pravda" v době odstranění. Tato aktualizace dokumentu se zobrazí v informačním kanálu pro změnu. Pro tuto položku můžete nastavit hodnotu TTL, aby ji bylo možné automaticky odstranit později.

### <a name="guaranteed-order"></a>Garantované pořadí

V informačním kanálu změny v rámci hodnoty klíče oddílu je zaručená objednávka, ale ne napříč hodnotami klíče oddílu. Měli byste vybrat klíč oddílu, který vám poskytne smysluplnou objednávku záruku.

Představte si třeba maloobchodní aplikaci pomocí vzoru návrhu vytváření událostí. V této aplikaci jsou všechny akce uživatele "události", které jsou modelovány jako zápisy do Azure Cosmos DB. Představte si, že v následujícím pořadí došlo k některým ukázkovým událostem:

1. Zákazník přidá položku A do svého nákupního košíku.
2. Zákazník přidá položku B do nákupního košíku.
3. Zákazník odstraní položku A z nákupního košíku.
4. Dodávají se rezerva a obsah nákupního košíku pro zákazníky.

Pro každého zákazníka se uchovává materializované zobrazení aktuálního obsahu nákupního košíku. Tato aplikace musí zajistit, aby se tyto události zpracovaly v pořadí, ve kterém se vyskytují. Pokud například chcete před odebráním položky A provést rezervaci z košíku, je možné, že zákazník by měl položku A dodán, a to na rozdíl od požadované položky B. Aby bylo zaručeno, že tyto čtyři události jsou zpracovávány v pořadí jejich výskytu, měly by spadat do stejné hodnoty klíče oddílu. Pokud vyberete možnost **uživatelské jméno** (každý zákazník má jedinečné uživatelské jméno) jako klíč oddílu, můžete zajistit, aby se tyto události zobrazovaly v kanálu změn ve stejném pořadí, ve kterém jsou zapsány do Azure Cosmos DB.

## <a name="examples"></a>Příklady

Tady jsou některé příklady kódu informačního kanálu, které přesahují rozsah ukázek poskytovaných v dokumentaci Microsoftu:

- [Úvod do kanálu změn](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Případ použití IoT na střed pro změnu kanálu](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Prodejní případ použití Centrované kolem kanálu změn](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Další kroky

* [Přehled kanálů změn](change-feed.md)
* [Možnosti čtení kanálu změn](read-change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)