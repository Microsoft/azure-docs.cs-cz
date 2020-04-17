---
title: Změna návrhových vzorů informačního kanálu v Azure Cosmos DB
description: Přehled běžných vzorů návrhu zdrojů změn
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450347"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Změna návrhových vzorů informačního kanálu v Azure Cosmos DB

Kanál změn Azure Cosmos DB umožňuje efektivní zpracování velkých datových sad s velkým objemem zápisů. Kanál změn také nabízí alternativu k dotazování celé datové sady k identifikaci toho, co se změnilo. Tento dokument se zaměřuje na běžné vzory návrhu kanálu změn, kompromisy návrhu a omezení kanálu změn.

Azure Cosmos DB je vhodný pro IoT, hraní her, maloobchod a provozní protokolování aplikací. Běžným návrhovým vzorem v těchto aplikacích je použití změn dat k aktivaci dalších akcí. Příklady dalších opatření:

* Aktivace oznámení nebo volání rozhraní API při vložení nebo aktualizaci položky.
* Zpracování datových proudů v reálném čase pro zpracování IoT nebo analýzy v reálném čase na provozních datech.
* Přesun y dat, jako je synchronizace s mezipamětí, vyhledávačem, datovým skladem nebo chladírenšovým úložištěm.

Kanál změn v Azure Cosmos DB umožňuje vytvářet efektivní a škálovatelná řešení pro každý z těchto vzorů, jak je znázorněno na následujícím obrázku:

![Použití zdroje změn Azure Cosmos DB k využití analýz v reálném čase a výpočetních scénářů řízených událostmi](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Výpočetní technika událostí a oznámení

Kanál změn Azure Cosmos DB můžete zjednodušit scénáře, které je třeba aktivovat oznámení nebo volání rozhraní API na základě určité události. [Knihovna procesů kanálu změn](change-feed-processor.md) můžete použít k automatickému dotazování kontejneru na změny a volání externího rozhraní API pokaždé, když dojde k zápisu nebo aktualizaci.

Můžete také selektivně aktivovat oznámení nebo odeslat volání do rozhraní API na základě konkrétních kritérií. Například pokud čtete z kanálu změn pomocí [Funkce Azure](change-feed-functions.md), můžete vložit logiku do funkce pouze odeslat oznámení, pokud byla splněna určitá kritéria. Zatímco kód funkce Azure by se spustit během každého zápisu a aktualizace, oznámení by se odeslat pouze v případě, že byla splněna určitá kritéria.

## <a name="real-time-stream-processing"></a>Zpracování datových proudů v reálném čase

Kanál změn Azure Cosmos DB se dá použít pro zpracování datových proudů v reálném čase pro IoT nebo zpracování analýzy v reálném čase na provozních datech.
Můžete například přijímat a ukládat data událostí ze zařízení, senzorů, infrastruktury a aplikací a zpracovávat tyto události v reálném čase pomocí [Spark](../hdinsight/spark/apache-spark-overview.md). Následující obrázek ukazuje, jak můžete implementovat architekturu lambda pomocí Azure Cosmos DB prostřednictvím kanálu změn:

![Kanál lambda založený na Azure Cosmos DB pro ingestování a dotaz](./media/change-feed/lambda.png)

V mnoha případech implementace zpracování datového proudu nejprve obdrží velký objem příchozích dat do fronty dočasných zpráv, jako je Azure Event Hub nebo Apache Kafka. Kanál změn je skvělou alternativou díky schopnosti Azure Cosmos DB podporovat trvalou vysokou míru ingestování dat s garantovanou nízkou latencí čtení a zápisu. Mezi výhody informačního kanálu změn Azure Cosmos DB ve frontě zpráv patří:

### <a name="data-persistence"></a>Trvalost dat

Data zapsaná do Azure Cosmos DB se zobrazí v kanálu změn a budou zachována, dokud nebudou odstraněna. Fronty zpráv mají obvykle maximální dobu uchování. [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) například nabízí maximální uchovávání dat 90 dní.

### <a name="querying-ability"></a>Možnost dotazování

Kromě čtení z kanálu změn kontejneru Cosmos můžete také spouštět dotazy SQL na data uložená v Azure Cosmos DB. Kanál změn není duplikace dat již v kontejneru, ale spíše jen jiný mechanismus čtení dat. Proto pokud čtete data z kanálu změn, bude vždy konzistentní s dotazy stejného kontejneru Azure Cosmos DB.

### <a name="high-availability"></a>Vysoká dostupnost

Azure Cosmos DB nabízí až 99,999 % dostupnosti čtení a zápisu. Na rozdíl od mnoha front zpráv azure cosmos DB data lze snadno globálně distribuovat a nakonfigurovat pomocí [RTO (Cíl doby obnovení)](consistency-levels-tradeoffs.md#rto) nula.

Po zpracování položek ve zdroji změn můžete vytvořit materializované zobrazení a zachovat agregované hodnoty zpět v Azure Cosmos DB. Pokud používáte Azure Cosmos DB k vytvoření hry, můžete například použít kanál změn k implementaci žebříčků v reálném čase na základě skóre z dokončených her.

## <a name="data-movement"></a>Přesuny dat

Můžete také číst z kanálu změn pro přesun dat v reálném čase.

Informační kanál o změnách vám například pomůže efektivně provádět následující úkoly:

* Aktualizujte mezipaměť, index vyhledávání nebo datový sklad s daty uloženými v Azure Cosmos DB.

* Provádějte žádné migrace na jiný účet Azure Cosmos nebo do jiného kontejneru Azure Cosmos s jiným klíčem logického oddílu.

* Implementujte vrstvení a archivaci dat na úrovni aplikace. Můžete například ukládat "horká data" v Azure Cosmos DB a stárnout "studená data" do jiných úložných systémů, jako je [Azure Blob Storage](../storage/common/storage-introduction.md).

Pokud máte [denormalizovat data mezi oddíly a kontejnery](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), můžete číst z kanálu změn kontejneru jako zdroj pro tuto replikaci dat. Replikace dat v reálném čase s kanálem změn může zaručit pouze konečnou konzistenci. Můžete [sledovat, jak daleko je procesor kanálu změn zaostává ve](how-to-use-change-feed-estimator.md) zpracování změn v kontejneru Cosmos.

## <a name="event-sourcing"></a>Získávání událostí

[Vzor získávání událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) zahrnuje použití úložiště pouze pro připojení k zaznamenání celé řady akcí na tato data. Zdroj změn Azure Cosmos DB je skvělou volbou jako centrální úložiště dat v architekturách získávání událostí, kde je všechny ingestování dat modelováno jako zápisy (žádné aktualizace nebo odstranění). V takovém případě každý zápis do Azure Cosmos DB je "událost" a budete mít úplný záznam minulých událostí ve zdroji změn. Typické použití událostí publikovaných centrálním úložištěm událostí je pro údržbu materializovaných zobrazení nebo pro integraci s externími systémy. Vzhledem k tomu, že neexistuje žádný časový limit pro uchovávání informací v kanálu změn, můžete přehrát všechny minulé události čtením od začátku kanálu změn kontejneru Cosmos.

Můžete mít [více změn krmiva spotřebitelé přihlásit ke stejnému kontejneru změny krmiva](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Kromě zřízené propustnosti [kontejneru zapůjčení](change-feed-processor.md#components-of-the-change-feed-processor) není k dispozici žádné náklady na využití kanálu změn. Krmivo pro změnu je k dispozici v každém kontejneru bez ohledu na to, zda je využívána.

Azure Cosmos DB je skvělé centrální úložiště trvalých dat pouze pro připojení ve vzoru získávání událostí kvůli jeho silné stránky v horizontální škálovatelnost a vysokou dostupnost. Knihovna zpracovatelů změn navíc nabízí záruku ["alespoň jednou",](change-feed-processor.md#error-handling) která zajišťuje, že nezmeškáte zpracování žádných událostí.

## <a name="current-limitations"></a>Aktuální omezení

Kanál změn má důležitá omezení, kterým byste měli rozumět. Zatímco položky v kontejneru Cosmos vždy zůstanou v kanálu změn, kanál změn není úplný protokol operace. Existují důležité oblasti, které je třeba vzít v úvahu při navrhování aplikace, která využívá kanál změn.

### <a name="intermediate-updates"></a>Průběžné aktualizace

Do informačního kanálu o změnách je zahrnuta pouze poslední změna pro danou položku. Při zpracování změn si přečtete nejnovější dostupnou verzi položky. Pokud existuje více aktualizací na stejnou položku v krátkém časovém období, je možné vynechat zpracování zprostředkující aktualizace. Pokud chcete sledovat aktualizace a mít možnost přehrát minulé aktualizace položky, doporučujeme modelování těchto aktualizací jako sérii zápisů místo.

### <a name="deletes"></a>Odstraní

Kanál změn nezachycuje odstranění. Pokud odstraníte položku z kontejneru, je také odebrána z kanálu změn. Nejběžnější metodou zpracování tohoto je přidání měkké značky na položky, které jsou odstraněny. Můžete přidat vlastnost s názvem "odstraněno" a nastavit ji na "true" v době odstranění. Tato aktualizace dokumentu se zobrazí ve zdroji změn. U této položky můžete nastavit ttl, aby ji bylo možné později automaticky odstranit.

### <a name="guaranteed-order"></a>Garantovaná objednávka

V kanálu změn je zaručeno pořadí v rámci hodnoty klíče oddílu, ale ne napříč hodnotami klíče oddílu. Měli byste vybrat klíč oddílu, který vám poskytne záruku smysluplné objednávky.

Zvažte například maloobchodní aplikaci pomocí návrhového vzoru zdroje událostí. V této aplikaci různé akce uživatele jsou každý "události", které jsou modelovány jako zápisy do Azure Cosmos DB. Představte si, že některé příklady událostí došlo v následujícím pořadí:

1. Zákazník přidá položku A do nákupního košíku
2. Zákazník přidá položku B do nákupního košíku
3. Zákazník odebere položku A ze svého nákupního košíku
4. Zákazník se odhlásí a obsah nákupního košíku je odeslán

Pro každého zákazníka je zachováno zhmotněné zobrazení aktuálního obsahu nákupního košíku. Tato aplikace musí zajistit, že tyto události jsou zpracovány v pořadí, ve kterém k nim dochází. Pokud by například pokladna košíku měla být zpracována před odebráním položky A, je pravděpodobné, že by zákazník nechal odeslat položku A, na rozdíl od požadované položky B. Aby bylo zaručeno, že tyto čtyři události jsou zpracovány v pořadí jejich výskytu, by měly spadat do stejné hodnoty klíče oddílu. Pokud vyberete **uživatelské jméno** (každý zákazník má jedinečné uživatelské jméno) jako klíč oddílu, můžete zaručit, že tyto události se zobrazí v kanálu změn ve stejném pořadí, ve kterém jsou zapsány do Azure Cosmos DB.

## <a name="examples"></a>Příklady

Zde jsou některé příklady kódu kanálu změn v reálném světě, které přesahují rozsah ukázek uvedených v dokumentech společnosti Microsoft:

- [Úvod do kanálu změn](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Případ použití IoT soustředěný kolem kanálu změn](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Případ použití maloobchodu soustředěný kolem zdroje změn](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Další kroky

* [Přehled kanálů změn](change-feed.md)
* [Možnosti čtení informačního kanálu o změnách](read-change-feed.md)
* [Používání informačního kanálu o změnách pomocí funkcí Azure](change-feed-functions.md)