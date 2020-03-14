---
title: Časté otázky – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje seznam nejčastější dotazy (FAQ) pro Azure Event Hubs a jejich odpovědi.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 3b46c574ea47622ec97e70c0d2f2cdc3aa54ec0d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264957"
---
# <a name="event-hubs-frequently-asked-questions"></a>Nejčastější dotazy k Event Hubs

## <a name="general"></a>Obecné

### <a name="what-is-an-event-hubs-namespace"></a>Co je obor názvů služby Event Hubs?
Obor názvů je kontejner oboru pro témata Event Hub a Kafka. Poskytuje jedinečný [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Obor názvů slouží jako kontejner aplikace, která může zastřešovat i více témata Event Hub a Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Kdy vytvořím nový obor názvů vs. použít existující obor názvů?
Přidělení kapacity ([jednotky propustnosti (počet propustnosti)](#throughput-units)) se účtuje na úrovni oboru názvů. Obor názvů je také přidružen k oblasti.

Místo použití existujícího oboru názvů v jednom z následujících scénářů je vhodné vytvořit nový obor názvů: 

- Potřebujete centrum událostí přidružené k nové oblasti.
- Potřebujete centrum událostí přidružené k jinému předplatnému.
- Budete potřebovat centrum událostí s odlišným přidělením kapacity (to znamená, že kapacita pro obor názvů s přidaným centrem událostí by překročila prahovou hodnotu 40.) a nechcete pro vyhrazený cluster přejít.  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaký je rozdíl mezi Event Hubs úrovně Basic a Standard?

Úroveň Standard služby Azure Event Hubs poskytuje funkce nad rámec co je k dispozici na úrovni Basic. Následující funkce jsou součástí Standard:

* Delší dobu uchování událostí
* Další zprostředkovaná připojení se při překročení limitu účtovat větší než číslo zahrnuté
* Víc než jedna [Skupina příjemců](event-hubs-features.md#consumer-groups)
* [Snímky](event-hubs-capture-overview.md)
* [Integrace Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Další informace o cenových úrovních, včetně Event Hubs úrovně Dedicated, najdete v článku [o cenách Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Pokud je k dispozici služby Azure Event Hubs?

Azure Event Hubs je k dispozici ve všech podporovaných oblastech Azure. Seznam najdete na stránce [oblastí Azure](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Můžete použít samostatné připojení AMQP k odeslání a příjem z více event hubs?

Ano, pokud jsou všechna centra event hubs v oboru názvů stejný.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Co je maximální doba uchovávání pro události?

Event Hubs úrovně Standard úroveň momentálně podporuje maximální doba sedm dní. Centra událostí nejsou zamýšlená jako trvalá úložiště dat. Doby uchování delší než 24 hodin jsou určené pro scénáře, ve kterých je vhodné přehrát datový proud událostí do stejných systémů. například pro školení nebo ověření nového modelu strojového učení pro stávající data. Pokud budete potřebovat dobu uchovávání zpráv déle než 7 dní, povolením [Event Hubsho zachycení](event-hubs-capture-overview.md) v centru událostí získáte data z centra událostí do účtu úložiště nebo účtu služby Azure Data Lake, který zvolíte. Povolení funkce zachytávání se účtují poplatky podle vaší zakoupené jednotky propustnosti.

Můžete nakonfigurovat dobu uchování zachycených dat ve vašem účtu úložiště. Funkce **správy životního cyklu** Azure Storage nabízí bohatou zásadu založenou na pravidlech pro účty pro obecné účely v2 a BLOB Storage. Zásady můžete použít k převodu dat do příslušných úrovní přístupu nebo vypršení jejich platnosti na konci životního cyklu dat. Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Jak můžu monitorovat Moje služby Event Hubs?
Event Hubs emituje vyčerpávající metriky, které poskytují stav vašich prostředků [Azure monitor](../azure-monitor/overview.md). Také vám umožňují posouzení celkového stavu služby Event Hubs pouze na úrovni oboru názvů, ale také na úrovni entity. Přečtěte si informace o tom, jaké monitorování se nabízí pro [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jaké porty potřebuji v bráně firewall otevřít? 
K posílání a přijímání zpráv můžete použít následující protokoly s Azure Service Bus:

- Rozšířený protokol řízení front zpráv (AMQP) (AMQP)
- HTTP
- Apache Kafka

V následující tabulce najdete Odchozí porty, které musíte otevřít, abyste mohli tyto protokoly používat ke komunikaci s Azure Event Hubs. 

| Protokol | Porty | Podrobnosti | 
| -------- | ----- | ------- | 
| AMQP | 5671 a 5672 | Viz [Průvodce protokolem AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) . | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Viz [použití Event Hubs z aplikací Kafka](event-hubs-for-kafka-ecosystem-overview.md) .

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jaké IP adresy potřebuji pro seznam povolených?
Chcete-li najít správné IP adresy pro připojení k seznamu, postupujte podle následujících kroků:

1. Z příkazového řádku spusťte následující příkaz: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Poznamenejte si IP adresu vrácenou v `Non-authoritative answer`. Jediná doba, kterou by se změnila, je, že obor názvů obnovíte na jiný cluster.

Pokud používáte redundanci zóny pro svůj obor názvů, musíte provést několik dalších kroků: 

1. Nejprve spustíte nástroj nslookup v oboru názvů.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Poznamenejte si název v části **nesměrodatná odpověď** , která je v jednom z následujících formátů: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Spusťte nástroj nslookup pro každý z nich s příponami S1, S2 a S3 k získání IP adres všech tří instancí spuštěných ve třech zónách dostupnosti. 

## <a name="apache-kafka-integration"></a>Integrace Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Jak integrovat existující aplikaci Kafka s Event Hubs?
Služba Event Hubs poskytuje Kafka koncového bodu, který lze použít ve svých stávajících aplikací na základě Apache Kafka. Změna konfigurace je vše, co je potřeba mít prostředí PaaS Kafka. To poskytuje alternativu ke spuštění clusteru Kafka. Event Hubs podporuje Apache Kafka 1.0 a novějšími verzemi klienta a spolupracuje s vaší stávající Kafka aplikace, nástroje a architektury. Další informace najdete v tématu [Event Hubs pro úložiště Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Jaké změny konfigurace je potřeba udělat pro svoji existující aplikaci komunikovat s Event Hubs?
K připojení do centra událostí povolené Kafka, budete muset aktualizovat konfiguraci klienta Kafka. Provede se vytvořením oboru názvů Event Hubs a získání [připojovacího řetězce](event-hubs-get-connection-string.md). Změňte bootstrap.servers tak, aby odkazoval na 9093 Event Hubs plně kvalifikovaný název domény a port. Aktualizujte soubor SASL. jaas. config tak, aby klienta Kafka nasměroval do vašeho koncového bodu Event Hubs s povoleným Kafka (což je připojovací řetězec, který jste získali) se správným ověřováním, jak je uvedeno níže:

Bootstrap.Servers={your. EVENTHUBS. Plně kvalifikovaný název domény}: 9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule požadované uživatelské jméno = "$ConnectionString" password = "{YOUR. EVENTHUBS. PŘIPOJENÍ. ŘETĚZEC} ";

Příklad:

Bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule požadované uživatelské jméno = "$ Připojovací řetězec"password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Poznámka: Pokud SASL. jaas. config není ve vašem rozhraní podporovaná konfigurace, najděte konfigurace, které se používají k nastavení uživatelského jména a hesla SASL a místo toho použijte je. Nastavte uživatelské jméno na $ConnectionString a heslo, aby váš připojovací řetězec služby Event Hubs.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Co je velikost zpráv/události Kafka s podporou služby Event hubs?
Maximální povolená velikost zprávy pro Event Hubs s povoleným Kafka je 1 MB.

## <a name="throughput-units"></a>Jednotky propustnosti

### <a name="what-are-event-hubs-throughput-units"></a>Co jsou jednotky propustnosti služby Event Hubs?
Propustnost ve službě Event Hubs definuje množství dat v megabajty nebo (v tisících) počet 1 KB událostí tohoto příchozího a odchozího přenosu dat prostřednictvím služby Event Hubs. Propustnost se měří v jednotek propustnosti (jednotek propustnosti, které). Kupte si jednotek propustnosti, které předtím, než můžete začít používat službu Event Hubs. Můžete explicitně vybrat jednotek propustnosti Event Hubs buď pomocí portálu nebo pomocí šablon Resource Manageru Event Hubs. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Jednotky propustnosti platí pro všechna centra event hubs v oboru názvů?
Ano, jednotky propustnosti (jednotek propustnosti), platí pro všechna centra event hubs v oboru názvů služby Event Hubs. Znamená to, že si koupíte jednotek propustnosti, které na úrovni oboru názvů a jsou sdíleny v rámci daného oboru názvů služby event hubs. Každý jednotek Propustnosti získává obor názvů následující možnosti:

- Až 1 MB událostí příchozího přenosu dat (událostí odeslaných do centra událostí), ale žádné více než 1 000 událostí příchozího přenosu dat, operace správy, nebo ovládací prvek volání rozhraní API za sekundu.
- Až 2 MB za sekundu odchozího přenosu dat události (události spotřebované z centra událostí), ale ne více než 4096 událostí odchozího přenosu dat.
- Až 84 GB úložiště událostí (dostatečné pro výchozí dobu uchování 24 hodin).

### <a name="how-are-throughput-units-billed"></a>Jak se účtují jednotky propustnosti?
Jednotky propustnosti (jednotek propustnosti) se účtují po hodinách. Účtování vychází maximální počet jednotek, který byl vybrán během dané hodiny. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Jak lze optimalizovat využití na můj jednotek propustnosti?
Můžete začít jako jednu jednotku propustnosti (TU) a zapnout [Automatické](event-hubs-auto-inflate.md)rozpínání. Automatické rozšiřování umožňuje růst vašeho jednotek propustnosti, které zvyšuje provoz/datové části. Můžete také nastavit horní limit počtu jednotek propustnosti.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Jak funguje automatické rozšiřování funkcí služby Event Hubs?
Automatické rozšiřování funkce vám umožní škálovat kapacitu jednotek propustnosti (jednotek propustnosti, které). Znamená to, že můžete začít zakoupením jednotek propustnosti, nízké které a automatické rozšiřování škálování nahoru vaše jednotek propustnosti, které jako vaše příchozího přenosu dat se zvyšuje. Poskytuje nákladově efektivní možnosti a naprostou kontrolu nad počet jednotek propustnosti, které ke správě. Tato funkce je jedinou funkcí pro **škálování** a můžete úplně řídit škálování počtu počet propustnosti, a to tak, že ho aktualizujete. 

Můžete začít s nízkou jednotek propustnosti (jednotek propustnosti), například 2 jednotek propustnosti. Pokud předpověď, že provoz může zvětšit 15 jednotek propustnosti, zapnutí ověřování automatické rozšiřování funkce oboru názvů služby a nastavit maximální limit 15 jednotek propustnosti. Vaše jednotek propustnosti, které teď můžou při nárůstu provozu růst automaticky.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Platí se za spojená, když zapnete funkci automatické rozšiřování?
S touto funkcí nejsou spojené **žádné náklady** . 

### <a name="how-are-throughput-limits-enforced"></a>Jak se vynucují limity propustnosti?
Pokud příchozí přenos dat celkem propustnosti nebo frekvence událostí příchozího přenosu dat celkový počet ve všech centrech event hubs v oboru názvů překročí celkový počet přidělených jednotek propustnosti, odesílatelů jsou omezené a obdržíte chyby naznačující překročení kvóty pro příchozí přenos.

Pokud se propustnost celkový počet odchozího přenosu dat nebo frekvence celkový počet událostí odchozího přenosu dat ve všech centrech event hubs v oboru názvů překročí celkový počet přidělených jednotek propustnosti, příjemci jsou omezené a přijímat chyby naznačující překročení kvóty pro odchozí přenos. Kvóty příchozího a odchozího přenosu dat se uplatňují odděleně tak, aby žádný odesílatel nezpomalil spotřebu událostí způsobit ani příjemce zabránit událostí odesílaných do centra událostí.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Je nějaký limit počtu jednotek propustnosti (jednotek propustnosti), které může být vyhrazený/vybrané?
Na více tenantů, nabídky a jednotek propustnosti může růst až 40 jednotek propustnosti (můžete vybrat až 20 jednotek propustnosti, které na portálu a zvýšit lístek podpory a zvýšit ji na 40 jednotek propustnosti, které na stejný obor názvů). Kromě 40 počet propustnosti Event Hubs nabízí model založený na prostředku nebo kapacitě, který se označuje jako **clustery Event Hubs úrovně Dedicated**. Vyhrazené clustery se prodává v kapacitních jednotkách (CUs).

## <a name="dedicated-clusters"></a>Vyhrazené clustery

### <a name="what-are-event-hubs-dedicated-clusters"></a>Co jsou clustery vyhrazená Služba Event Hubs?
Event hubs úrovně Dedicated clustery nabízí nasazení jednoho tenanta pro zákazníky s nejnáročnější požadavky. Tuto nabídku sestavení založená na kapacitě clusteru, který není vázán prostřednictvím jednotek propustnosti. To znamená, že cluster můžete použít k ingestování a streamování dat, jak je využíváno využitím procesoru a paměti clusteru. Další informace najdete v tématu [Event Hubs úrovně Dedicated clustery](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Kolik jeden kapacitní jednotku, dejte mi dosáhnout?
V případě vyhrazeného clusteru může ingestování a streamování záviset na různých faktorech, jako jsou vaše producenti, spotřebitelé, rychlost, kterou sledujete a zpracováváte, a mnohem víc. 

Následující tabulka ukazuje výsledky srovnávacích testů jsme dosáhli během naše testování:

| Datová část obrazce | Příjemci | Šířka pásma příchozího přenosu dat| Příchozí zprávy | Šířka pásma pro výchozí přenos dat | Odchozí zprávy | Celkový počet jednotek propustnosti | Jednotek propustnosti, které na kapacitní jednotku |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 tisíc zprávy za sekundu | 800 MB/s | 800k zprávy za sekundu | 400 jednotek propustnosti | 100 jednotek propustnosti | 
| Dávky 10x10KB | 2 | 666 MB/s | zprávy 66.6 k/s | 1.33 GB/s | 133k zprávy za sekundu | 666 jednotek propustnosti | 166 jednotek propustnosti |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34k zprávy za sekundu | 1,05 GB/s | 34k zprávy za sekundu | 1000 jednotek propustnosti | 250 jednotek propustnosti |

Při testování, použil následující kritéria:

- Použil se vyhrazený cluster služby Event Hubs s čtyři kapacitních jednotek (CUs). 
- Centra událostí používá pro příjem bylo 200 oddíly. 
- Který se ingestuje data byla přijata dvě aplikace příjemce přijímají ze všech oddílů.

Výsledky můžete získat představu o co jde dosáhnout s clusterem vyhrazená Služba Event Hubs. Kromě toho obsahuje clusteru vyhradit Event Hubs Capture, povolené pro vaše scénáře mikrodávek i dlouhodobé uchovávání.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Jak vytvořit cluster vyhrazená Služba Event Hubs?
Event Hubs vyhrazený cluster vytvoříte tak, že odešlete [žádost o podporu zvýšení kvóty](https://portal.azure.com/#create/Microsoft.Support) nebo se obrátíte na [tým Event Hubs](mailto:askeventhubs@microsoft.com). Chcete-li získat cluster nasadit a předán můžete používat obvykle trvá přibližně dva týdny. Tento proces je dočasný, dokud nebude k dispozici kompletní Samoobslužná služba prostřednictvím šablon Azure Portal nebo Azure Resource Manager, která během nasazování clusteru trvá přibližně dvě hodiny.

## <a name="best-practices"></a>Osvědčené postupy

### <a name="how-many-partitions-do-i-need"></a>Počet oddílů budu potřebovat?
Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů se nedá měnit, takže při nastavování počtu oddílů byste měli uvažovat o dlouhodobém škálování. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Další informace o oddílech najdete v tématu [oddíly](event-hubs-features.md#partitions).

Možná budete chtít nastavit, aby byla nejvyšší možná hodnota, která je v době vytváření 32. Pamatujte, že pokud bude mít více než jeden oddíl, budou události odesílány do několika oddílů bez zachování pořadí, pokud nenastavíte odesílatele tak, aby odesílali pouze jeden oddíl z 32 ponechání zbývajících 31 oddílů redundantní. V bývalém případě budete muset číst události ve všech oddílech 32. V druhém případě se od dodatečné konfigurace neúčtují žádné zjevnější náklady, které musíte udělat na hostiteli procesoru událostí.

Event Hubs je navržena k umožnění čtečku jeden oddíl na skupinu uživatelů. Ve většině případů použití stačí výchozí nastavení čtyři oddíly. Pokud chcete škálovat zpracování událostí, možná budete chtít zvážit přidání dalších oddílů. Pro oddíl není k dispozici žádný konkrétní limit propustnosti, ale agregovaná propustnost ve vašem oboru názvů je omezená počtem jednotek propustnosti. Zvýšení počtu jednotek propustnosti ve vašem oboru názvů, možná budete chtít povolit souběžných čtenářů k dosažení vlastní maximální propustnost další oddíly.

Ale pokud máte model, ve které má vaše aplikace spřažení na konkrétní oddíl zvýšením počtu oddílů nemusí být jakékoli výhody. Další informace najdete v tématu [dostupnost a konzistence](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-more-pricing-information"></a>Kde najdu Další informace o cenách?

Úplné informace o Event Hubs cenách najdete v [podrobnostech o cenách Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Platí se poplatek za uchování událostí služby Event Hubs pro více než 24 hodin?

Standardní Event Hubs úrovně neumožňuje uchovávání zpráv období delší než 24 hodin, maximálně sedm dní. Pokud velikost celkového počtu uložených událostí překročí úložiště pro počet vybraných jednotek propustnosti (84 GB za každou jednotku propustnosti), velikost, která překročí se účtuje za publikované sazby úložiště objektů Blob v Azure. Pokrývá povolené úložiště každé jednotky propustnosti všechny náklady na úložiště na dobu uchování 24 hodin (výchozí) i v případě, že jednotka propustnosti umožňuje maximálního povoleného příchozího přenosu.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak je velikost úložiště služby Event Hubs počítá a účtuje?

Celková velikost všech uložených událostí, včetně interní rezervy na záhlaví událostí nebo na strukturu diskového úložiště ve všech centrech event hubs, se měří průběžně během dne. Na konci dne se vypočítá největší dosažená velikost úložiště. Povolené denní úložiště se vypočítá podle minimálního počtu jednotek propustnosti vybraných během dne (každá jednotka propustnosti má povolenou velikost 84 GB). Pokud celková velikost překročí vypočítanou denní částku úložiště, bude se nadměrné úložiště účtovat pomocí sazeb Azure Blob Storage (na základě **místně redundantního úložiště** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak se počítají událostí příchozího přenosu dat služby Event Hubs?

Každá událost odeslaná do centra událostí se počítá jako Účtovaná zpráva. *Událost* příchozího přenosu dat je definovaná jako jednotka dat, která je menší nebo rovna 64 KB. Událost, která je menší než nebo rovna 64 KB velikost se považuje za jednu účtovanou událost. Pokud je událost větší než 64 KB, vypočítá se počet účtovaných událostí podle velikosti události v násobcích 64 KB. Například událost velikosti 8 KB odeslaná do centra událostí se účtuje jako jedna událost, ale zpráva velikosti 96 KB odeslaná do centra událostí se účtuje jako dvě události.

Události spotřebované z centra událostí, jak dobře jako volání ovládacího prvku, jako jsou kontrolní body a operace správy, se nepočítají události fakturovatelné příchozího přenosu dat, ale kumulovat až jednotek propustnosti.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Nevztahují poplatky za zprostředkované připojení do služby Event Hubs?

Poplatky za připojení použije, pouze pokud je použit protokol AMQP. Za odesílání událostí přes HTTP se neúčtují žádné poplatky za připojení, bez ohledu na počet odesílajících systémů nebo zařízení. Pokud máte v úmyslu používat AMQP (například k dosažení efektivnějšího streamování událostí nebo k povolení obousměrné komunikace ve scénářích příkazů a ovládacích prvků IoT), přečtěte si informace o tom, kolik připojení je zahrnuté v každé úrovni služby, na stránce s [informacemi o cenách Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) .

### <a name="how-is-event-hubs-capture-billed"></a>Jak se funkce Event Hubs Capture účtuje?

Zachycení je povolena, když má libovolné Centrum událostí v oboru názvů povolenou možnost zachycení. Funkce Event Hubs Capture se účtuje po hodinách na základě zakoupených jednotek propustnosti. Když je počet jednotek propustnosti zvýší nebo sníží, účtování funkce Event Hubs Capture odráží tyto změny v přírůstcích po celých hodinách. Další informace o fakturaci za Event Hubs Capture najdete v tématu [Event Hubs informace o cenách](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Mi bude účtovat pro účet úložiště, který vyberu pro Event Hubs Capture?

Capture využívá účet úložiště, které poskytnete při povolené v Centru událostí. Protože je váš účet úložiště, všechny změny pro tuto konfiguraci se účtují ke svému předplatnému Azure.

## <a name="quotas"></a>kvóty,

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existují nějaké kvóty spojené s Event Hubs?

Seznam všech kvót Event Hubs najdete v tématu [kvóty](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Odstraňování potíží

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Proč nemůžu vytvořit obor názvů po jeho odstranění z jiného předplatného? 
Když odstraníte obor názvů z předplatného, počkejte 4 hodiny, než ho znovu vytvoříte se stejným názvem v jiném předplatném. V opačném případě se může zobrazit následující chybová zpráva: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované služby Event Hubs a jejich doporučené akce?

Seznam možných výjimek Event Hubs naleznete v tématu [Exception Overview](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Event Hubs podporuje dva typy [diagnostických protokolů](event-hubs-diagnostic-logs.md) – protokoly chyb zachytávání a provozní protokoly – oba z nich jsou reprezentovány ve formátu JSON a lze je zapnout prostřednictvím Azure Portal.

### <a name="support-and-sla"></a>Podpora a SLA

Technická podpora pro Event Hubs je dostupná prostřednictvím [komunitních fór](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Podpora k fakturaci a správě předplatného se poskytuje zadarmo.

Další informace o naší smlouvě SLA najdete na stránce [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Automaticky neplochý Event Hubs](event-hubs-auto-inflate.md)
