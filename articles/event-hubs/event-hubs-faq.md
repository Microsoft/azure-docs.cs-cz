---
title: Nejčastější dotazy – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje seznam nejčastějších dotazů (FAQ) pro Azure Event Hubs a jejich odpovědi.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 7f6e1896c97c96cd484d15fb9e6a3056e5c5d6b2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086364"
---
# <a name="event-hubs-frequently-asked-questions"></a>Nejčastější dotazy centra event hubů

## <a name="general"></a>Obecné

### <a name="what-is-an-event-hubs-namespace"></a>Co je obor názvů Event Hubs?
Obor názvů je kontejner oboru pro témata Centra událostí/Kafka. To vám dává jedinečný [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Obor názvů slouží jako kontejner aplikace, který může ubytovat více témat Centra událostí/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Kdy vytvořím nový obor názvů vs. použít existující obor názvů?
Přidělení kapacity ([jednotky propustnosti (Jednotky propustnosti)](#throughput-units)) se účtují na úrovni oboru názvů. Obor názvů je také přidružen k oblasti.

Můžete chtít vytvořit nový obor názvů namísto použití existujícího oboru v jednom z následujících scénářů: 

- Potřebujete centrum událostí přidružené k nové oblasti.
- Potřebujete Centrum událostí přidružené k jinému předplatnému.
- Potřebujete centrum událostí s odlišným přidělením kapacity (to znamená, že potřeba kapacity pro obor názvů s přidaným centrem událostí by překročila prahovou hodnotu 40 TU a nechcete jít pro vyhrazený cluster)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaký je rozdíl mezi úrovněmi Event Hubs Basic a Standard?

Úroveň Standard azure event hubů poskytuje funkce nad rámec toho, co je k dispozici na úrovni Basic. Standardní funkce jsou součástí následujících funkcí:

* Delší uchovávání událostí
* Další zprostředkovaná připojení s poplatkem za nadbytkvině za více než uvedený počet
* Více než jedna [skupina spotřebitelů](event-hubs-features.md#consumer-groups)
* [Zachytit](event-hubs-capture-overview.md)
* [Integrace Kafky](event-hubs-for-kafka-ecosystem-overview.md)

Další informace o cenových úrovních, včetně centra událostí Vyhrazené, najdete v [tématu Podrobnosti o cenách Centra událostí](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Kde jsou Azure Event Hubs dostupná?

Azure Event Hubs je dostupná ve všech podporovaných oblastech Azure. Seznam najdete na stránce [oblasti Azure.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Mohu k odesílání a přijímání z více center událostí používat jedno připojení AMQP?

Ano, pokud jsou všechny centra událostí ve stejném oboru názvů.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Jaká je maximální doba uchovávání událostí?

Úroveň Event Hubs Standard aktuálně podporuje maximální dobu uchovávání sedmi dnů. Centra událostí nejsou určena jako trvalé úložiště dat. Retenční období delší než 24 hodin jsou určena pro scénáře, ve kterých je vhodné přehrát datový proud událostí do stejných systémů; například trénovat nebo ověřovat nový model strojového učení na existujících datech. Pokud potřebujete uchování zpráv déle než sedm dní, povolení [akce Hubs Capture](event-hubs-capture-overview.md) ve vašem centru událostí načte data z centra událostí do účtu úložiště nebo účtu Služby Azure Data Lake Service podle vašeho výběru. Povolením sběru se účtuje poplatek na základě zakoupených jednotek propustností.

Můžete nakonfigurovat dobu uchování pro zachycená data v účtu úložiště. Funkce **správy životního cyklu** služby Azure Storage nabízí bohaté zásady založené na pravidlech pro účty úložiště s obecným pro obecné účely v2 a úložiště objektů blob. Pomocí zásad můžete data převést na příslušné úrovně přístupu nebo vyprší jejich platnost na konci životního cyklu dat. Další informace najdete [v tématu Správa životního cyklu úložiště objektů blob Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Jak můžu sledovat centra událostí?
Event Hubs vydává vyčerpávající metriky, které poskytují stav vašich prostředků azure [monitoru](../azure-monitor/overview.md). Umožňují také posoudit celkový stav služby Event Hubs nejen na úrovni oboru názvů, ale také na úrovni entity. Přečtěte si, jaké monitorování se nabízí pro [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jaké porty je třeba otevřít na firewallu? 
Pomocí služby Azure Service Bus můžete pomocí služby Azure Service Bus odesílat a přijímat zprávy:

- Rozšířený protokol řízení front zpráv (AMQP)
- HTTP
- Apache Kafka

V následující tabulce najdete odchozí porty, které je potřeba otevřít, abyste pomocí těchto protokolů komunikovali s Azure Event Hubs. 

| Protocol (Protokol) | Porty | Podrobnosti | 
| -------- | ----- | ------- | 
| AMQP | 5671 a 5672 | Viz [Průvodce protokolem AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Viz [Použití centra událostí z aplikací Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jaké IP adresy potřebuji k whitelistu?
Chcete-li najít správné adresy IP do seznamu povolených adres pro vaše připojení, postupujte takto:

1. Z příkazového řádku spusťte následující příkaz: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Poznamenejte si `Non-authoritative answer`adresu IP vrácenou v . Změna by se změnila pouze v případě, že obnovíte obor názvů do jiného clusteru.

Pokud pro obor názvů používáte redundanci zóny, je třeba provést několik dalších kroků: 

1. Nejprve spustit nslookup v oboru názvů.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Poznamenejte si název v části **neautoritativní odpovědi,** která je v jednom z následujících formátů: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Spusťte nslookup pro každý z nich s příponami s1, s2 a s3 získat IP adresy všech tří instancí běžících ve třech zónách dostupnosti, 

## <a name="apache-kafka-integration"></a>Integrace Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Jak lze integrovat stávající aplikaci Kafka s event huby?
Event Hubs poskytuje koncový bod Kafka, který může být použit pro vaše stávající aplikace založené na Apache Kafka. Změna konfigurace je vše, co je nutné mít prostředí PaaS Kafka. Poskytuje alternativu ke spuštění vlastního clusteru Kafka. Event Hubs podporuje Apache Kafka 1.0 a novější klientské verze a pracuje s vašimi stávajícími aplikacemi, nástroji a frameworky Kafka. Další informace naleznete v [tématu Event Hubs for Kafka repo](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Jaké změny konfigurace je třeba provést, aby moje stávající aplikace mohl y mluvit s event huby?
Chcete-li se připojit k centru událostí, budete muset aktualizovat konfigurace klienta Kafka. Provádí se tak vytvořením oboru názvů Event Hubs a získáním [připojovacího řetězce](event-hubs-get-connection-string.md). Změňte bootstrap.servers tak, aby ukazovaly hlavní název souboru QDN rozbočovače událostí a port na 9093. Aktualizujte soubor sasl.jaas.config tak, aby klienta Kafka nasměroval na koncový bod centra událostí (což je připojovací řetězec, který jste získali), se správným ověřováním, jak je znázorněno níže:

bootstrap.servers={VAŠE. EVENTHUBS. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.plainLoginModule required username="$ConnectionString" password="{YOUR. EVENTHUBS. Připojení. řetězec}";

Příklad:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.plainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespacespace.windows.net/; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Poznámka: Pokud sasl.jaas.config není podporovaná konfigurace ve vašem frameworku, najděte konfigurace, které se používají k nastavení uživatelského jména a hesla SASL, a místo toho je použijte. Nastavte uživatelské jméno na $ConnectionString a heslo k připojovacímu řetězci Event Hubs.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Jaká je velikost zprávy/události pro centra událostí?
Maximální velikost zprávy povolená pro centra událostí je 1 MB.

## <a name="throughput-units"></a>Jednotky propustnosti

### <a name="what-are-event-hubs-throughput-units"></a>Co jsou jednotky propustnost centra událostí?
Propustnost v event hubech definuje množství dat v mega bajtech nebo počet (v tisících) událostí 1 KB, které se promítají a odchozí přenos dat prostřednictvím event hubů. Tato propustnost se měří v jednotkách propustnost (TU). Než začnete používat službu Event Hubs, nakupujte hlavní použití. Můžete explicitně vybrat TU centra událostí buď pomocí portálu nebo šablony Správce prostředků centra událostí. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Platí jednotky propustnost pro všechny centra událostí v oboru názvů?
Ano, jednotky propustnost (Jednotky propustnost) platí pro všechny centra událostí v oboru názvů Event Hubs. To znamená, že zakoupíte tu na úrovni oboru názvů a jsou sdíleny mezi centra událostí v rámci tohoto oboru názvů. Každý TU opravňuje obor názvů k následujícím možnostem:

- Až 1 MB za sekundu příchozího přenosu dat událostí (události odeslané do centra událostí), ale ne více než 1000 příchozích přenosů dat události, operace správy nebo řízení volání rozhraní API za sekundu.
- Až 2 MB za sekundu odchozích událostí (události spotřebované z centra událostí), ale ne více než 4096 odchozí chod událostí.
- Až 84 GB úložiště událostí (dost pro výchozí 24hodinovou dobu uchovávání).

### <a name="how-are-throughput-units-billed"></a>Jak se účtují jednotky propustností?
Jednotky propustnost (Jednotky propustnost) se účtují po hodinách. Fakturace je založena na maximálním počtu jednotek, které byly vybrány během dané hodiny. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Jak mohu optimalizovat využití jednotek propustností?
Můžete začít tak nízko jako jedna jednotka propustnost (TU) a zapnout [automatické nafukování](event-hubs-auto-inflate.md). Funkce automatického nafouknutí umožňuje růst vašich tu s nárůstem provozu / užitečného zatížení. Můžete také nastavit horní limit počtu sou.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Jak funkce automatického nafouknutí centra událostí funguje?
Funkce automatického nafouknutí umožňuje vertikálně zvýšit kapacitu jednotek propustností (Jednotky propustnost). To znamená, že můžete začít tím, že zakoupíte nízké buňky a automaticky nafoukne temeni na vaše buňky, jak se zvyšuje příchozí přenos. Poskytuje nákladově efektivní možnost a úplnou kontrolu nad počtem sousto, které chcete spravovat. Tato funkce je **funkce pouze škálování** a můžete zcela řídit škálování počtu tu aktualizací. 

Můžete chtít začít s jednotkami s nízkou propustností (Jednotky propustnost), například 2 jednotky tupů. Pokud předpovídáte, že váš provoz může růst na 15 fosl, zapněte funkci automatického nafouknutí v oboru názvů a nastavte maximální limit na 15 foutů. Nyní můžete zvýšit vaše tu automaticky, jak váš provoz roste.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Jsou při zapnutí funkce automatického nafouknutí spojeny náklady?
S touto funkcí nejsou spojeny **žádné náklady.** 

### <a name="how-are-throughput-limits-enforced"></a>Jak jsou vynucena omezení propustnost?
Pokud celková propustnost příchozího přenosu dat nebo rychlost celkových událostí příchozího přenosu dat ve všech rozbočovačích událostí v oboru názvů překročí agregační příspěvky jednotek propustnosti, odesílatelé jsou omezeni a obdrží chyby označující, že byla překročena kvóta příchozího přenosu dat.

Pokud celková propustnost odchozího přenosu nebo rychlost odchozího přenosu událostí ve všech rozbočovačích událostí v oboru názvů překročí agregační povolenky jednotky propustnosti, příjemci jsou omezeni a obdrží chyby označující, že byla překročena kvóta odchozího přenosu. Kvóty příchozího a odchozího přenosu dat jsou vynuceny samostatně, takže žádný odesílatel nemůže způsobit zpomalení spotřeby událostí, ani příjemce nemůže zabránit odesílání událostí do centra událostí.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Existuje omezení počtu jednotek propustností,, které lze rezervovat nebo vybrat?
Na víceklientské nabídce mohou jednotky propustnosti růst až na 40 jednotek TuN (můžete vybrat až 20 jednotek Tu na portálu a zvýšit lístek podpory, který ji zvýší na 40 jednotek Tun ve stejném oboru názvů). Kromě 40 tu nabízí Event Hubs model založený na prostředku/kapacitě nazvaný **Vyhrazené clustery centra událostí**. Vyhrazené clustery se prodávají v jednotkách kapacity (CU).

## <a name="dedicated-clusters"></a>Vyhrazené clustery

### <a name="what-are-event-hubs-dedicated-clusters"></a>Co jsou clustery služby Event Hubs úrovně Dedicated?
Centra událostí Vyhrazená clustery nabízejí nasazení jednoho klienta pro zákazníky s nejnáročnějšími požadavky. Tato nabídka vytváří cluster založený na kapacitě, který není vázán jednotkami propustnosti. To znamená, že můžete použít cluster u ingestování a streamování dat podle využití procesoru a paměti clusteru. Další informace naleznete v [tématu Centra událostí Vyhrazené clustery](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Co mi umožní dosáhnout jedna jednotka kapacity?
U vyhrazeného clusteru závisí na různých faktorech, jako jsou výrobci, spotřebitelé, rychlost, s jakou ingestujete a zpracováváte, a mnoho dalšího. 

Následující tabulka ukazuje výsledky srovnávacích testů, kterých jsme dosáhli během testování:

| Obrazec datové části | Přijímače | Šířka pásma příchozího přenosu dat| Příchozí zprávy o přenosu dat | Šířka pásma odchozího přenosu | Odchozí zprávy | Celkem TUs | TU na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 tisíc zpráv/s | 800 MB/s | 800 tisíc zpráv/s | 400 tuu | 100 tuu | 
| Dávky 10x10KB | 2 | 666 MB/s | 66.6k zpráv/s | 1,33 GB/s | 133 kS zpráv/s | 666 TU | 166 TU |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34k zprávy / s | 1,05 GB/s | Zprávy 34 kS/s | 1000 tuu | 250 tuu |

Při testování byla použita následující kritéria:

- Byl použit vyhrazený cluster event hubů se čtyřmi jednotkami kapacity (CU). 
- Centrum událostí používané pro ingestování mělo 200 oddílů. 
- Data, která byla přijata byla přijata dvěma aplikacemi přijímače přijímajícími ze všech oddílů.

Výsledky poskytují představu o tom, čeho lze dosáhnout pomocí vyhrazeného clusteru Event Hubs. Kromě toho vyhradit clusteru je dodáván s event huby zachycení povoleno pro vaše mikro dávky a dlouhodobé uchovávání scénářů.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Jak vytvořím vyhrazený cluster centra událostí?
Vyhrazený cluster centra událostí vytvoříte odesláním [žádosti o podporu zvýšení kvóty](https://portal.azure.com/#create/Microsoft.Support) nebo kontaktováním [týmu Event Hubs](mailto:askeventhubs@microsoft.com). Obvykle trvá asi dva týdny, než se cluster nasadí a předá, aby ho mohl použít. Tento proces je dočasný, dokud se prostřednictvím portálu Azure nezpřístupní úplná samoobslužná služba.

## <a name="best-practices"></a>Osvědčené postupy

### <a name="how-many-partitions-do-i-need"></a>Kolik oddílů potřebuji?
Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů není měnitelný, takže byste měli zvážit dlouhodobé měřítko při nastavování počtu oddílů. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Další informace o oddílech naleznete v [tématu Oddíly](event-hubs-features.md#partitions).

Můžete ji nastavit jako nejvyšší možnou hodnotu, která je 32, v době vytvoření. Nezapomeňte, že mít více než jeden oddíl bude mít za následek události odeslané do více oddílů bez zachování pořadí, pokud nakonfigurujete odesílatele odeslat pouze do jednoho oddílu z 32 opuštění zbývajících 31 oddílů redundantní. V prvním případě budete muset číst události ve všech oddílech 32. V druhém případě neexistuje žádné zjevné dodatečné náklady kromě další konfigurace, kterou musíte provést na hostiteli procesoru událostí.

Centra událostí je navržena tak, aby umožnila čtečku jednoho oddílu na skupinu spotřebitelů. Ve většině případů použití je postačující výchozí nastavení čtyř oddílů. Pokud chcete škálovat zpracování událostí, můžete zvážit přidání dalších oddílů. Neexistuje žádný konkrétní limit propustnost na oddíl, ale agregační propustnost v oboru názvů je omezena počtem jednotek propustnost. Při zvýšení počtu jednotek propustnost v oboru názvů můžete chtít další oddíly, které umožní souběžným čtenářům dosáhnout vlastní maximální propustnost.

Pokud však máte model, ve kterém má vaše aplikace spřažení s určitým oddílem, zvýšení počtu oddílů nemusí být pro vás přínosem. Další informace naleznete v [tématu dostupnost a konzistence](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-more-pricing-information"></a>Kde najdu další informace o cenách?

Úplné informace o cenách Event Hubs najdete v [podrobnostech o cenách Centra událostí](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Platí se za uchovávání událostí Event Hubs po dobu delší než 24 hodin?

Úroveň Standard centra událostí umožňuje dobu uchovávání zpráv delší než 24 hodin, maximálně po dobu sedmi dnů. Pokud velikost celkového počtu uložených událostí překročí povolenku úložiště pro počet vybraných jednotek propustnosti (84 GB na jednotku propustnosti), bude velikost, která překročí povolenku, účtována při publikované rychlosti úložiště objektů blob Azure. Povolenka úložiště v každé jednotce propustnost pokrývá všechny náklady na úložiště pro retenční období 24 hodin (výchozí) i v případě, že je jednotka propustnost použita až do maximálního příspěvku pro příchozí přenos dat.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak se počítá a účtuje velikost úložiště Event Hubs?

Celková velikost všech uložených událostí, včetně všech interních relací pro záhlaví událostí nebo na strukturách diskového úložiště ve všech rozbočovačích událostí, se měří po celý den. Na konci dne se vypočítá velikost úložiště ve špičce. Denní příspěvek na úložiště se vypočítá na základě minimálního počtu jednotek propustností, které byly vybrány během dne (každá jednotka propustnostposkytuje příspěvek 84 GB). Pokud celková velikost překročí vypočtený denní příspěvek na úložiště, přebytek úložiště se účtuje pomocí sazby úložiště objektů blob Azure (na místně **redundantní úložiště** sazby).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak se počítají události příchozího přenosu dat centra událostí?

Každá událost odeslaná do centra událostí se počítá jako fakturovatelná zpráva. *Ingress událost* je definována jako jednotka dat, která je menší nebo rovna 64 KB. Každá událost, která je menší nebo rovna velikosti 64 KB, se považuje za jednu fakturovatelnou událost. Pokud je událost větší než 64 kB, počet fakturovatelných událostí se vypočítá podle velikosti události v násobcích 64 KB. Například událost 8 KB odeslaná do centra událostí se účtuje jako jedna událost, ale zpráva o 96 KB odeslaná do centra událostí se účtuje jako dvě události.

Události spotřebované z centra událostí, stejně jako operace správy a volání ovládacího prvku, jako jsou kontrolní body, se nepočítají jako fakturovatelné události příchozího přenosu dat, ale narůstají do příspěvku jednotky propustnost.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Vztahují se na centra událostí poplatky za zprostředkované připojení?

Poplatky za připojení platí pouze v případě, že je použit protokol AMQP. Bez ohledu na počet odesílacích systémů nebo zařízení se neúčtují žádné poplatky za připojení k událostem. Pokud plánujete použít AMQP (například k dosažení efektivnějšího streamování událostí nebo k povolení obousměrné komunikace ve scénářích příkazů a řízení IoT), přečtěte si stránku [s informacemi o cenách centra událostí,](https://azure.microsoft.com/pricing/details/event-hubs/) kde najdete podrobnosti o tom, kolik připojení je součástí každé úrovně služby.

### <a name="how-is-event-hubs-capture-billed"></a>Jak se zachytává zachytávání eventhubů?

Zachycení je povoleno, pokud má v centru událostí v oboru názvů povolenou možnost Zachytit. Zachycení centra událostí se účtuje každou hodinu za zakoupenou jednotku propustnost. Jako počet jednotek propustnost se zvyšuje nebo snižuje, Event Hubs Capture fakturace odráží tyto změny v celé hodinu přírůstky. Další informace o fakturaci centra Event Hubs Capture najdete v [tématu Informace o cenách centra událostí](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Bude se mi účtovat účet úložiště, který vyberu pro digitalizaci centra událostí?

Digitalizace používá účet úložiště, který zadáte, když je povolen v centru událostí. Vzhledem k tomu, že se jedná o váš účet úložiště, všechny změny pro tuto konfiguraci se účtují do vašeho předplatného Azure.

## <a name="quotas"></a>Kvóty

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existují nějaké kvóty spojené s event huby?

Seznam všech kvót centra událostí naleznete v [tématu kvóty](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Proč nemohu vytvořit obor názvů po jeho odstranění z jiného předplatného? 
Při odstranění oboru názvů z předplatného počkejte 4 hodiny, než jej znovu vytvoříte se stejným názvem v jiném předplatném. V opačném případě se může `Namespace already exists`zobrazit následující chybová zpráva: . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované event huby a jejich navrhované akce?

Seznam možných výjimek centra událostí naleznete v tématu [Přehled výjimek](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Event Hubs podporuje dva typy [diagnostických protokolů](event-hubs-diagnostic-logs.md) – protokoly chyb pro sběr a provozní protokoly – oba jsou reprezentované v jsonu a můžou být zapnuté prostřednictvím portálu Azure.

### <a name="support-and-sla"></a>Podpora a SLA

Technická podpora pro Event Hubs je k dispozici prostřednictvím [komunitních fór](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Podpora fakturace a správy předplatného je poskytována zdarma.

Další informace o naší smlouvě SLA najdete na stránce [Smlouvy o úrovni služeb.](https://azure.microsoft.com/support/legal/sla/)

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled centra událostí](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Automatické nafouknutí rozbočovačů událostí](event-hubs-auto-inflate.md)
