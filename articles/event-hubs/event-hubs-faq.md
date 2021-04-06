---
title: Nejčastější dotazy – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje seznam nejčastějších dotazů pro Azure Event Hubs a jejich odpovědí.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: e6fd4814e771d03827e51f1cd5ee182c9e432cc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98696104"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs nejčastějších dotazech

## <a name="general"></a>Obecné

### <a name="what-is-an-event-hubs-namespace"></a>Co je Event Hubs obor názvů?
Obor názvů je kontejner oboru pro témata centra událostí/Kafka. Poskytuje jedinečný [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Obor názvů slouží jako kontejner aplikace, který může obsahovat několik témat centra událostí/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Kdy vytvořím nový obor názvů vs. použít existující obor názvů?
Přidělení kapacity ([jednotky propustnosti (počet propustnosti)](#throughput-units)) se účtuje na úrovni oboru názvů. Obor názvů je také přidružen k oblasti.

Místo použití existujícího oboru názvů v jednom z následujících scénářů je vhodné vytvořit nový obor názvů: 

- Potřebujete centrum událostí přidružené k nové oblasti.
- Potřebujete centrum událostí přidružené k jinému předplatnému.
- Budete potřebovat centrum událostí s odlišným přidělením kapacity (to znamená, že kapacita pro obor názvů s přidaným centrem událostí by překročila prahovou hodnotu 40.) a nechcete pro vyhrazený cluster přejít.  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaký je rozdíl mezi Event Hubs úrovně Basic a Standard?

Úroveň Standard služby Azure Event Hubs poskytuje funkce nad rámec toho, co je k dispozici na úrovni Basic. Standardně jsou k dispozici následující funkce:

* Delší doba uchovávání událostí
* Další zprostředkovaná připojení s náklady nadlimitního využití za více než uvedené množství
* Víc než jedna [Skupina příjemců](event-hubs-features.md#consumer-groups)
* [Zachytávání](event-hubs-capture-overview.md)
* [Integrace Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Další informace o cenových úrovních, včetně Event Hubs úrovně Dedicated, najdete v článku [o cenách Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Kde je Azure Event Hubs k dispozici?

Azure Event Hubs je k dispozici ve všech podporovaných oblastech Azure. Seznam najdete na stránce [oblastí Azure](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Můžu použít jedno rozšířený protokol řízení front zpráv (AMQP) (AMQP) připojení pro odesílání a příjem z několika Center událostí?

Ano, pokud jsou všechna centra událostí ve stejném oboru názvů.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Jaká je maximální doba uchování pro události?

Úroveň Standard Event Hubs v současné době podporuje maximální dobu uchování po dobu sedmi dnů. Centra událostí nejsou zamýšlená jako trvalá úložiště dat. Doby uchování delší než 24 hodin jsou určené pro scénáře, ve kterých je vhodné přehrát datový proud událostí do stejných systémů. Například pro školení nebo ověření nového modelu strojového učení pro stávající data. Pokud budete potřebovat dobu uchovávání zpráv déle než 7 dní, povolením [Event Hubsho zachycení](event-hubs-capture-overview.md) v centru událostí získáte data z centra událostí do účtu úložiště nebo účtu služby Azure Data Lake, který zvolíte. Povolení služby Capture účtuje poplatek na základě zakoupených jednotek propustnosti.

Můžete nakonfigurovat dobu uchování zachycených dat ve vašem účtu úložiště. Funkce **správy životního cyklu** Azure Storage nabízí bohatou zásadu založenou na pravidlech pro účty pro obecné účely v2 a BLOB Storage. Zásady můžete použít k převodu dat do příslušných úrovní přístupu nebo vypršení jejich platnosti na konci životního cyklu dat. Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Návody monitorovat můj Event Hubs?
Event Hubs emituje vyčerpávající metriky, které poskytují stav vašich prostředků [Azure monitor](../azure-monitor/overview.md). Také vám umožní vyhodnotit celkový stav služby Event Hubs nejen na úrovni oboru názvů, ale také na úrovni entity. Přečtěte si informace o tom, jaké monitorování se nabízí pro [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-data"></a><a name="in-region-data-residency"></a>Kde Azure Event Hubs ukládá data?
Azure Event Hubs Standard a vyhrazené úrovně ukládají metadata a data v oblastech, které vyberete. Pokud je pro obor názvů Azure Event Hubs nastaveno geografické zotavení po havárii, zkopírují se metadata do sekundární oblasti, kterou vyberete. Proto tato služba automaticky splňuje požadavky na umístění dat oblastí, včetně těch, které jsou zadány v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Integrace Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Návody integrovat stávající aplikaci v Kafka s Event Hubs?
Event Hubs poskytuje koncový bod Kafka, který mohou používat vaše stávající aplikace založené na Apache Kafka. K dispozici je všechny změny konfigurace, které se vyžadují pro prostředí PaaS Kafka. Nabízí alternativu ke spuštění vlastního clusteru Kafka. Event Hubs podporuje Apache Kafka 1,0 a novější verze klientů a spolupracuje s vašimi stávajícími aplikacemi, nástroji a rozhraními Kafka. Další informace najdete v tématu [Event Hubs pro úložiště Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Jaké změny konfigurace je potřeba udělat, aby se moje stávající aplikace mohla spojit s Event Hubs?
Pokud se chcete připojit k centru událostí, budete muset aktualizovat konfigurace klienta Kafka. Provede se vytvořením oboru názvů Event Hubs a získání [připojovacího řetězce](event-hubs-get-connection-string.md). Změňte Bootstrap. Server tak, aby ukazovaly Event Hubs plně kvalifikovaný název domény a port na 9093. Aktualizujte sasl.jaas.config a nasměrujte klienta Kafka na koncový bod Event Hubs (což je připojovací řetězec, který jste získali), se správným ověřováním, jak je uvedeno níže:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Příklad:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
Poznámka: Pokud sasl.jaas.config ve vašem rozhraní není podporovaná konfigurace, najděte konfigurace používané k nastavení uživatelského jména a hesla SASL a používejte je místo toho. Nastavte uživatelské jméno na $ConnectionString a heslo pro připojovací řetězec Event Hubs.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Jaká je velikost zprávy nebo události pro Event Hubs?
Maximální povolená velikost zprávy pro Event Hubs je 1 MB.

## <a name="throughput-units"></a>Jednotky propustnosti

### <a name="what-are-event-hubs-throughput-units"></a>Co jsou Event Hubs jednotky propustnosti?
Propustnost v Event Hubs definuje množství dat v megabajtů bajtech nebo číslo (v tisících) událostí 1 KB, které příchozí a odchozí přenos prostřednictvím Event Hubs. Tato propustnost se měří v jednotkách propustnosti (počet propustnosti). Zakupte si počet propustnosti, než budete moct začít používat službu Event Hubs. Můžete explicitně vybrat Event Hubs počet propustnosti buď pomocí portálu, nebo Event Hubs šablony Správce prostředků. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Vztahují se jednotky propustnosti na všechna centra událostí v oboru názvů?
Ano, jednotky propustnosti (počet propustnosti) platí pro všechna centra událostí v oboru názvů Event Hubs. Znamená to, že si koupíte počet propustnosti na úrovni oboru názvů a sdílíte se mezi centry událostí v rámci tohoto oboru názvů. Každé z nich opravňuje obor názvů k následujícím funkcím:

- Až 1 MB událostí příchozího přenosu dat (události odeslané do centra událostí) za sekundu, ale ne víc než 1000 událostí příchozího přenosu dat, operací správy nebo řídicích volání rozhraní API za sekundu.
- Až 2 MB událostí odchozího přenosu dat (události spotřebované z centra událostí) za sekundu, ale ne víc než 4096 odchozích událostí.
- Až 84 GB úložiště událostí (je dostatečné pro výchozí dobu uchovávání po dobu 24 hodin).

### <a name="how-are-throughput-units-billed"></a>Jak se účtují jednotky propustnosti?
Jednotky propustnosti (počet propustnosti) se účtují po hodinách. Účtování vychází z maximálního počtu jednotek vybraných během dané hodiny. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Jak můžu optimalizovat využití u mých jednotek propustnosti?
Můžete začít jako jednu jednotku propustnosti (TU) a zapnout [Automatické](event-hubs-auto-inflate.md)rozpínání. Funkce automatického rozvíjení umožňuje rozšířit počet propustnosti jako provoz nebo datovou část. Můžete také nastavit horní limit počtu počet propustnostiů.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Jak funguje automatická funkce Event Hubs funguje?
Funkce automatického rozšíření umožňuje horizontální navýšení kapacity jednotek propustnosti (počet propustnosti). To znamená, že můžete začít nákupem nízkého počet propustnostiu a automatického navýšení škály počet propustnosti při nárůstu příchozího přenosu dat. Nabízí cenově výhodné možnost a úplnou kontrolu nad počtem počet propustnosti, která se mají spravovat. Tato funkce je jedinou funkcí pro **škálování** a můžete úplně řídit škálování počtu počet propustnosti, a to tak, že ho aktualizujete. 

Možná budete chtít začít s počet propustnosti jednotkami s nízkou propustností (například 2 počet propustnosti). Pokud předpokládáte, že váš provoz může růst až 15 počet propustnosti, zapněte v oboru názvů funkci automatického rozpínání a nastavte maximální limit na 15 počet propustnosti. Počet propustnosti teď můžete rozšířit tak, jak vaše přenosy roste.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Jsou k dispozici náklady spojené s funkcí automatické rozpínání?
S touto funkcí nejsou spojené **žádné náklady** . 

### <a name="how-are-throughput-limits-enforced"></a>Jak se vynutily limity propustnosti?
Pokud celková propustnost příchozího přenosu dat **nebo celková frekvence** událostí příchozího přenosu dat ve všech centrech událostí v oboru názvů přesáhne agregované hodnoty jednotek propustnosti, odesílají se omezení a dostanou chyby, které signalizují překročení kvóty příchozího přenosu dat.

Pokud celková propustnost **odchozích** dat nebo celková přenosová rychlost události napříč všemi centry událostí v oboru názvů překročí agregované odchylky jednotek propustnosti, přijímače se omezují, ale negenerují se žádné chyby omezování. 

Kvóty příchozího a odchozího přenosu dat se uplatňují samostatně, takže žádný odesilatel nemůže způsobit zpomalení události, ani nemůže přijímač zabránit v posílání událostí do centra událostí.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>Existuje limit počtu jednotek propustnosti, které je možné rezervovat/vybrat?

Při vytváření oboru názvů Basic nebo Standard úrovně v Azure Portal můžete pro obor názvů vybrat až 20 počet propustnosti. Pokud ho chcete zvýšit na **přesně** 40 počet propustnosti, odešlete  [žádost o podporu](../azure-portal/supportability/how-to-create-azure-support-request.md).  

1. Na stránce **obor názvů sběrnice událostí** vyberte v nabídce vlevo možnost **Nová žádost o podporu** . 
1. Na stránce **Nová žádost o podporu** proveďte tyto kroky:
    1. Pro **Shrnutí** Popište problém několika slovy. 
    1. Jako **typ problému** vyberte **kvóta**. 
    1. V případě **podtypu problému** vyberte **požadavek na zvýšení nebo snížení jednotky propustnosti**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Stránka Support request":::

Kromě 40 počet propustnosti Event Hubs nabízí model založený na prostředku nebo kapacitě, který se označuje jako clustery Event Hubs úrovně Dedicated. Vyhrazené clustery se prodávají v jednotkách kapacity (kapacitní jednotky). Další informace najdete v tématu [Event Hubs úrovně Dedicated-Overview](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Vyhrazené clustery

### <a name="what-are-event-hubs-dedicated-clusters"></a>Co jsou clustery služby Event Hubs úrovně Dedicated?
Clustery Event Hubs úrovně Dedicated nabízejí nasazení s jedním tenantům pro zákazníky s nejnáročnějšími požadavky. Tato nabídka vytvoří cluster založený na kapacitě, který není svázán s jednotkami propustnosti. To znamená, že cluster můžete použít k ingestování a streamování dat, jak je využíváno využitím procesoru a paměti clusteru. Další informace najdete v tématu [Event Hubs úrovně Dedicated clustery](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Návody vytvořit cluster Event Hubs úrovně Dedicated?
Podrobné pokyny a další informace o nastavení Event Hubs vyhrazeného clusteru najdete v tématu [rychlý Start: vytvoření vyhrazeného clusteru Event Hubs pomocí Azure Portal](event-hubs-dedicated-cluster-create-portal.md). 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Oddíly

### <a name="how-many-partitions-do-i-need"></a>Kolik oddílů potřebuji?
Počet oddílů se zadává při vytváření a musí být v rozmezí od 1 do 32. Počet oddílů se nedá změnit na všech úrovních s výjimkou [vyhrazené úrovně](event-hubs-dedicated-overview.md), takže při nastavování počtu oddílů byste měli uvažovat o dlouhodobém škálování. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Další informace o oddílech najdete v tématu [oddíly](event-hubs-features.md#partitions).

Možná budete chtít nastavit, aby byla nejvyšší možná hodnota, která je v době vytváření 32. Pamatujte, že pokud bude mít více než jeden oddíl, budou události odesílány do několika oddílů bez zachování pořadí, pokud nenastavíte odesílatele tak, aby odesílali pouze jeden oddíl z 32 ponechání zbývajících 31 oddílů redundantní. V bývalém případě budete muset číst události ve všech oddílech 32. V druhém případě se od dodatečné konfigurace neúčtují žádné zjevnější náklady, které musíte udělat na hostiteli procesoru událostí.

Event Hubs je navržena tak, aby umožňovala jedno čtecí zařízení pro jednu skupinu příjemců. Ve většině případů použití je výchozí nastavení čtyř oddílů dostatečné. Pokud chcete škálovat zpracování událostí, možná budete chtít zvážit přidání dalších oddílů. Pro oddíl není k dispozici žádný konkrétní limit propustnosti, ale agregovaná propustnost ve vašem oboru názvů je omezená počtem jednotek propustnosti. Když zvýšíte počet jednotek propustnosti ve vašem oboru názvů, můžete chtít, aby měly další oddíly, které umožní souběžným čtenářům dosáhnout své vlastní maximální propustnosti.

Pokud však máte model, ve kterém má aplikace spřažení pro určitý oddíl, zvýšení počtu oddílů nemusí být pro vás výhodou. Další informace najdete v tématu [dostupnost a konzistence](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Zvětšit oddíly
Můžete požádat o zvýšení počtu oddílů na 40 (přesně) tím, že odešlete žádost o podporu. 

1. Na stránce **obor názvů sběrnice událostí** vyberte v nabídce vlevo možnost **Nová žádost o podporu** . 
1. Na stránce **Nová žádost o podporu** proveďte tyto kroky:
    1. Pro **Shrnutí** Popište problém několika slovy. 
    1. Jako **typ problému** vyberte **kvóta**. 
    1. Pro **podtyp problému** vyberte **požadavek na změnu oddílu**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Zvýšit počet oddílů":::

Počet oddílů se dá zvýšit přesně na 40. V takovém případě je potřeba zvýšit počet počet propustnosti také na 40. Pokud se později rozhodnete snížit limit hodnoty v hodnotě <= 20, limit maximálního počtu oddílů se také sníží na 32. 

Zmenšení oddílů neovlivní existující centra událostí, protože oddíly se aplikují na úrovni centra událostí a po vytvoření centra jsou neměnné. 

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-more-pricing-information"></a>Kde najdu Další informace o cenách?

Úplné informace o Event Hubs cenách najdete v [podrobnostech o cenách Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Účtuje se za dobu uchování Event Hubs událostí déle než 24 hodin.

Úroveň Standard Event Hubs povoluje dobu uchovávání zpráv delší než 24 hodin, maximálně po dobu sedmi dnů. Pokud velikost celkového počtu uložených událostí překročí povolenou velikost úložiště pro počet vybraných jednotek propustnosti (84 GB na jednotku propustnosti), bude se za velikost, na kterou se příspěvek překročí, účtovat celková míra úložiště objektů BLOB v Azure. V každé jednotce propustnosti se v rámci úložiště vztahují všechny náklady na úložiště na dobu uchování 24 hodin (výchozí nastavení), i když se jednotka propustnosti používá až do maximálního povoleného příchozího přenosu dat.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak se počítá a účtuje Event Hubs velikosti úložiště?

Celková velikost všech uložených událostí, včetně jakékoli interní režie pro záhlaví událostí nebo na struktury diskového úložiště ve všech centrech událostí, se měří v průběhu dne. Na konci dne se vypočítává velikost úložiště ve špičce. Denní povolené úložiště se vypočítá na základě minimálního počtu jednotek propustnosti vybraných během dne (každá jednotka propustnosti poskytuje povolený 84 GB). Pokud celková velikost překročí vypočítanou denní částku úložiště, bude se nadměrné úložiště účtovat pomocí sazeb Azure Blob Storage (na základě **místně redundantního úložiště** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak se počítají události příchozího přenosu dat Event Hubs?

Každá událost odeslaná do centra událostí se počítá jako fakturovatelná zpráva. *Událost* příchozího přenosu dat je definovaná jako jednotka dat, která je menší nebo rovna 64 KB. Jakákoli událost, která je menší nebo rovna 64 KB, se považuje za jednu fakturovatelnou událost. Pokud je událost větší než 64 KB, počítá se počet fakturovaných událostí podle velikosti události v násobcích 64 KB. Například událost 8 KB odeslaná do centra událostí se účtuje jako jedna událost, ale zpráva 96-KB odeslaná do centra událostí se účtuje jako dvě události.

Události spotřebované z centra událostí a operace správy a řídicí volání, jako jsou kontrolní body, se nepočítají jako Fakturovatelné události příchozího přenosu dat, ale narůstá na rezervu jednotek propustnosti.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Vztahují se na Event Hubs poplatky za zprostředkované připojení?

Poplatky za připojení platí jenom v případě, že se používá protokol AMQP. Za odesílání událostí pomocí protokolu HTTP se neúčtují žádné poplatky za připojení, bez ohledu na počet odesílajících systémů nebo zařízení. Pokud máte v úmyslu používat AMQP (například k dosažení efektivnějšího streamování událostí nebo k povolení obousměrné komunikace ve scénářích příkazů a ovládacích prvků IoT), přečtěte si informace o tom, kolik připojení je zahrnuté v každé úrovni služby, na stránce s [informacemi o cenách Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) .

### <a name="how-is-event-hubs-capture-billed"></a>Jak se účtuje Event Hubs zaznamenání?

Funkce Capture je povolená, když má libovolné centrum událostí v oboru názvů povolenou možnost zachycení. Event Hubs Capture se účtuje po hodinách za každou zakoupenou jednotku propustnosti. Když se počet jednotek propustnosti zvýší nebo sníží, Event Hubs zachytávání se tyto změny projeví během celých hodinových přírůstků. Další informace o fakturaci za Event Hubs Capture najdete v tématu [Event Hubs informace o cenách](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Účtuje se mi účet úložiště, který vyberu pro Event Hubs zachytávání?

Capture používá účet úložiště, který zadáte, pokud je povolený v centru událostí. Vzhledem k tomu, že se jedná o váš účet úložiště, všechny změny této konfigurace se účtují do předplatného Azure.

## <a name="quotas"></a>Kvóty

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Jsou k Event Hubs k dispozici nějaké kvóty?

Seznam všech kvót Event Hubs najdete v tématu [kvóty](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Proč nemůžu vytvořit obor názvů po jeho odstranění z jiného předplatného? 
Když odstraníte obor názvů z předplatného, počkejte 4 hodiny, než ho znovu vytvoříte se stejným názvem v jiném předplatném. V opačném případě se může zobrazit následující chybová zpráva: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jaké jsou některé výjimky vygenerované Event Hubs a jejich navrhovanými akcemi?

Seznam možných výjimek Event Hubs naleznete v tématu [Exception Overview](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Event Hubs podporuje dva typy [diagnostických protokolů](event-hubs-diagnostic-logs.md) – protokoly chyb zachytávání a provozní protokoly – oba z nich jsou reprezentovány ve formátu JSON a lze je zapnout prostřednictvím Azure Portal.

### <a name="support-and-sla"></a>Podpora a smlouva SLA

Technická podpora pro Event Hubs je k dispozici na [stránce s dotazem Microsoft Q&pro Azure Service Bus](/answers/topics/azure-service-bus.html). Podpora fakturace a správy předplatného se poskytuje bez jakýchkoli nákladů.

Další informace o naší smlouvě SLA najdete na stránce [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/) .

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Jak můžu cílit na konkrétní verzi sady Azure Storage SDK při použití Azure Blob Storage jako úložiště kontrolních bodů?
Pokud spustíte tento kód v Azure Stackovém centru, dojde k chybám za běhu, pokud necílíte na konkrétní verzi rozhraní API úložiště. Důvodem je, že sada Event Hubs SDK používá nejnovější dostupné rozhraní API Azure Storage dostupné v Azure, které nemusí být k dispozici na vaší platformě služby Azure Stack hub. Centrum Azure Stack může podporovat jinou verzi sady SDK služby Storage blob, než která je obvykle dostupná v Azure. Pokud jako úložiště kontrolního bodu používáte Azure blogu Storage, podívejte se na [podporovanou verzi rozhraní API Azure Storage pro sestavení centra Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) a cílení na verzi v kódu. 

Pokud například používáte v Azure Stack centra verze 2005, nejvyšší dostupná verze služby úložiště je verze 2019-02-02. Ve výchozím nastavení používá Klientská knihovna Event Hubs SDK nejvyšší dostupnou verzi v Azure (2019-07-07 v době vydání sady SDK). V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2019-02-02. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v následujících ukázkách pro jazyky C#, Java, Python a JavaScript a TypeScript.  

Příklad, jak cílit na konkrétní verzi rozhraní API úložiště z vašeho kódu, najdete v následujících ukázkách na GitHubu: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python – [synchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) a [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs](./event-hubs-about.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Automaticky neplochý Event Hubs](event-hubs-auto-inflate.md)
