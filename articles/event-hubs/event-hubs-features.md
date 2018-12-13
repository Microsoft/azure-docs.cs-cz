---
title: Přehled funkcí - Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje podrobnosti o funkcích a terminologie služby Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 917a551b8a187db6645e523d0189533e333d5371
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075162"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkce a terminologii používané v Azure Event Hubs

Azure Event Hubs je škálovatelná služba, která ingestuje a zpracovává velké objemy událostí a dat, s nízkou latencí a vysokou spolehlivostí pro zpracování událostí. Zobrazit [co je Event Hubs?](event-hubs-what-is-event-hubs.md) přehled vysoké úrovně.

Tento článek vychází z informací v [přehledovém článku](event-hubs-what-is-event-hubs.md)a poskytuje implementaci a technické podrobnosti o součásti služby Event Hubs a funkce.

## <a name="namespace"></a>Obor názvů
Obor názvů služby Event Hubs poskytuje jedinečné kontejner oboru, odkazuje jeho [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ve kterém se vytváří služba event hubs nebo témat Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs pro Apache Kafka

[Tato funkce](event-hubs-for-kafka-ecosystem-overview.md) poskytuje koncový bod, který umožňuje zákazníkům ke komunikaci s Event Hubs pomocí protokolu Kafka. Tato integrace poskytuje zákazníkům koncovým bodem v systému Kafka. To umožňuje zákazníkům konfigurace stávající aplikace Kafka ke komunikaci s Event Hubs, poskytuje alternativu ke spuštění vlastní clustery Kafka. Event Hubs pro Apache Kafka podporuje protokol Kafka 1.0 nebo novější. 

Tato integrace není nutné ke spuštění clusterů Kafka nebo spravovat je pomocí Zookeeper. To také umožňuje pracovat s některými z nejnáročnějších funkce Event Hubs jako zachycení, automatické rozšiřování a zotavení po havárii Geo.

Tato integrační také umožňuje aplikací, jako je Tvůrce zrcadlení nebo architekturu jako třeba připojit Kafka pracoval clusterless pouze změny konfigurace. 

## <a name="event-publishers"></a>Zdroje událostí

Každá entita, která odesílá data do centra událostí se Tvůrce událostí nebo *vydavatel události*. Zdroje událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0 nebo Kafka 1.0 nebo novější. Zdroje událostí se v centru událostí identifikují pomocí tokenu sdíleného přístupového podpisu (SAS) a mohou mít jedinečnou identitu, nebo mohou používat společný token SAS.

### <a name="publishing-an-event"></a>Publikování události

Můžete publikovat události pomocí protokolu AMQP 1.0, Kafka, 1.0 (nebo novější) nebo HTTPS. Služba Event Hubs poskytuje [klientských knihoven a třídy](event-hubs-dotnet-framework-api-overview.md) k publikování událostí z klientů .NET do centra událostí. Pro jiné moduly runtime a platformy můžete použít libovolného klienta protokolu AMQP 1.0, například [Apache Qpid](http://qpid.apache.org/). Události můžete publikovat samostatně nebo v dávce. Jedna publikace (instance dat události) je stanovený limit 1 MB, bez ohledu na to, zda se jedná o jedinou událost nebo dávku. Publikování události větší než tato prahová hodnota dojde k chybě. Nejvhodnějším postupem pro zdroje je nezajímat se o oddíly v centru událostí a specifikovat pouze *klíč oddílu* (představíme v další části) nebo svoji identitu prostřednictvím tokenu SAS.

Volba, jestli se použije protokol AMQP nebo HTTPS, závisí na konkrétním scénáři použití. Protokol AMQP vyžaduje nejen protokol TLS (Transport Level Security) nebo SSL/TLS, ale i vytvoření trvalého obousměrného soketu. AMQP má vyšší náklady na síť při inicializaci relace, ale HTTPS pro každý požadavek vyžaduje další režii SSL. AMQP má pro často používané zdroje vyšší výkon.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Služba Event Hubs zajišťuje, aby se všechny události, které sdílejí hodnotu klíče oddílu, v pořádku doručily do stejného oddílu. Pokud se klíče oddílů používají společně se zásadami zdroje, musí si identita zdroje a hodnota klíče oddílu odpovídat. V opačném případě dojde k chybě.

### <a name="publisher-policy"></a>Zásady zdroje

Služba Event Hubs umožňuje podrobnou kontrolu nad zdroji událostí prostřednictvím *zásad zdroje*. Zásady zdroje jsou běhové funkce, které byly navržené pro usnadnění kontroly nad velkým množstvím nezávislých zdrojů událostí. Zásady zdroje poskytují s použitím následujícího mechanismu každému zdroji vlastní identifikátor, který se používá při publikování událostí do centra událostí:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Názvy zdrojů není potřeba vytvářet dopředu, při publikování události ale musí odpovídat tokenu SAS, aby se zajistilo, že každý zdroj bude mít nezávislou identitu. Při použití zásad zdroje se každému názvu zdroje nastaví hodnota **PartitionKey** (Klíč oddílu). Aby vše správně fungovalo, musí tyto hodnoty odpovídat.

## <a name="capture"></a>Zachycování

[Funkce Event Hubs Capture](event-hubs-capture-overview.md) vám umožní automaticky zachytit streamovaná data ve službě Event Hubs a uložte ho do zvoleného účtu Blob storage nebo účet služby Azure Data Lake. Můžete povolit sběr dat z webu Azure portal a zadejte časové okno k provedení sběru a minimální velikost. Pomocí funkce Event Hubs Capture, můžete zadat vlastní účet služby Azure Blob Storage a kontejneru nebo účtu služby Azure Data Lake, jedním z nich se používá k ukládání zachycená data. Zachycená data je zapsána ve formátu Apache Avro.

## <a name="partitions"></a>Oddíly

Služba Event Hubs poskytuje datový proud zpráv pomocí schématu rozdělujícího datový proud na oddíly pro jednotlivé příjemce. To zajišťuje, aby každý příjemce četl jenom konkrétní podmnožinu nebo oddíl datového proudu zpráv. Toto schéma umožňuje vodorovné škálování zpracování událostí a poskytuje další funkce zaměřené na datový proud, které nejsou ve frontách a tématech k dispozici.

Oddíl je seřazená posloupnost událostí, která se nachází v centru událostí. Události, které nově přichází, se zařazují na konec této posloupnosti. Oddíl si lze představit jako „protokol transakcí“.

![Event Hubs](./media/event-hubs-features/partition.png)

Event Hubs uchovává data dobu uchování nakonfigurované, která se vztahuje na všechny oddíly v centra událostí. Události mizí na základě času, nemůžete je explicitně odstranit. Vzhledem k tomu, že oddíly jsou nezávislé a obsahují vlastní posloupnost dat, často se jejich velikost mění různým tempem.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů není možné měnit. Proto je při nastavování počtu oddílů potřeba uvažovat z dlouhodobého hlediska. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Pokud chcete použít vyšší počet oddílů než 32, kontaktujte tým služby Event Hubs.

I když oddíly identifikovat a je možné odeslat přímo, odesílání přímo do oddílu se nedoporučuje. Místo toho můžete použít konstrukce vyšší úrovně, které představujeme v tématech věnovaných [zdroji událostí](#event-publishers) a [kapacitě](#capacity). 

Oddíly jsou naplněné posloupností dat událostí, která obsahují tělo události, uživatelem definované vlastnosti kontejneru objektů a dat a metadat – například její posun v oddílu a pořadí v posloupnosti datového proudu.

Další informace o oddílech a kompromisu mezi dostupností a spolehlivostí najdete v tématech [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md#partition-key) a [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Klíč oddílu

[Klíč oddílu](event-hubs-programming-guide.md#partition-key) můžete použít k mapování příchozích dat událostí do konkrétních oddílů pro účely organizace dat. Klíč oddílu je hodnota zadaná odesílatelem, která byla předaná do centra událostí. Zpracovává se pomocí statické hashovací funkce, která vytvoří přiřazení k oddílu. Pokud při publikování události nezadáte klíč oddílu, použije se přiřazení metodou kruhového dotazování.

Zdroj události zná jenom svůj klíč oddílu, a ne oddíl, do kterého se události publikují. Díky tomuto oddělení klíče a oddílu odesílatel toho nepotřebuje vědět o zpracování příjmu dat příliš mnoho. Vhodným klíčem oddílu je jedinečná identita uživatele nebo zařízení, ale k seskupení souvisejících událostí do jednoho oddílu je možné použít i další atributy, například geografickou polohu.

## <a name="sas-tokens"></a>Tokeny SAS

Služba Event Hubs využívá *sdílené přístupové podpisy*, které jsou dostupné na úrovni oboru názvů a centra událostí. Token SAS, který se generuje z klíče SAS, je adresa URL, která je zašifrovaná pomocí hašovacího algoritmu SHA a zakódovaná ve specifickém formátu. Služba Event Hubs může znovu vygenerovat hash kód pomocí názvu klíče (zásady) a tokenu, a ověřit tak odesílatele. Za normálních okolností se tokeny SAS pro zdroje událostí vytváří jen s oprávněními pro **odesílání** na konkrétní centrum událostí. Tento mechanismus adresy URL a tokenu SAS slouží jako základ pro identifikaci zdroje, kterou představíme v části o zásadách zdroje. Další informace o práci s tokenem SAS naleznete v tématu o [ověřování u služby Service Bus pomocí sdíleného přístupového podpisu](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Příjemci událostí

*Příjemcem událostí* je každá entita, která čte data událostí z centra událostí. Všichni příjemci se ve službě Event Hubs připojují pomocí relace protokolu AMQP 1.0 a události se doručují tak, jak jsou postupně dostupné. Klient se na dostupnost dat nemusí dotazovat.

### <a name="consumer-groups"></a>Skupiny příjemců

Mechanismus publikování/odebírání ve službě Event Hubs umožňují *skupiny příjemců*. Skupina příjemců je zobrazení (stavu, pozice nebo posunu) celého centra událostí. Skupiny příjemců poskytují různým přijímajícím aplikacím oddělená zobrazení datového proudu událostí a umožňují jim nezávisle číst datový proud vlastním tempem a s použitím vlastních posunů.

V architektuře zpracování datového proudu se každá aplikace pro příjem dat rovná skupině příjemců. Pokud chcete zapisovat data událostí do dlouhodobého úložiště, pak je aplikace, která do úložiště zapisuje, skupinou příjemců. Komplexní zpracování událostí zase může provádět jiná, samostatná skupina příjemců. K oddílům můžete přistupovat pouze prostřednictvím skupiny příjemců. V centru událostí je vždy jedna výchozí skupina příjemců. Na standardní úrovni centra událostí můžete vytvořit až dvacet skupin příjemců.

Může existovat maximálně 5 souběžných čtenářů na oddíl na skupinu příjemců; ale **se doporučuje, aby existovala pouze jednoho aktivního příjemce na oddíl na skupinu příjemců**. V rámci jednoho oddílu každý Čtenář přijímá všechny zprávy. Pokud máte několik čtenářů do stejného oddílu, můžete proces duplicitní zprávy. Musíte se o to postarají ve vašem kódu, který nemusí být triviální. Je však platný přístup v některých scénářích.


Následují příklady konvenčního zápisu identifikátoru URI skupiny příjemců:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Následující obrázek znázorňuje architekturu zpracování datového proudu Event Hubs:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Posuny datového proudu

*Posun* je pozice události v rámci oddílu. Posun si můžete představit jako kurzor na straně klienta. Posun je číslo bajtu události. Tento posun umožňuje příjemci události (čtenáři) určit bod v datovém proudu událostí, od kterého chce události začít číst. Posun můžete zadat v podobě časového razítka nebo hodnoty posunu. Příjemci si sami zodpovídají za uložení svých hodnot posunu mimo službu Event Hubs. Každá událost v rámci oddílu zahrnuje posun.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Vytváření kontrolních bodů

*Vytváření kontrolních bodů* je proces, pomocí kterého čtenáři označují nebo potvrzují svou pozici v rámci posloupnosti událostí v oddílu. Za vytváření kontrolních bodů zodpovídá příjemce. Proces probíhá na bázi oddílů ve skupinách příjemců. Taková zodpovědnost znamená, že si každý čtenář oddílu v každé skupině příjemců musí udržovat přehled o své aktuální pozici v datovém proudu událostí a může informovat službu, když bude považovat datový proud za dokončený.

Pokud se čtenář z oddílu odpojí, začne při opětovném připojení číst od kontrolního bodu, který dříve zaslal poslední čtenář daného oddílu z této skupiny příjemců. Když se čtenář připojí, předá posun do centra událostí a určí tak umístění, od kterého začne číst. Takto můžete vytváření kontrolních bodů použít jak k označování událostí jako „dokončených“, tak k zajištění ochrany pro případ, že nastane selhání u čtenářů spuštěných na různých strojích. Ke starším datům se je možné vrátit tak, že určíte nižší posun od tohoto kontrolního bodu. Díky tomuto mechanismu umožňuje vytváření kontrolních bodů nejen obnovu při selhání, ale i opakované přehrání datového proudu.

### <a name="common-consumer-tasks"></a>Běžné úlohy příjemce

Všichni příjemci služby Event Hubs připojují pomocí relace protokolu AMQP 1.0, stav obousměrného komunikačního kanálu. Každý oddíl má relaci AMQP 1.0, která usnadňuje transport událostí rozdělených do oddílů.

#### <a name="connect-to-a-partition"></a>Připojení k oddílu

Při přímém připojení k oddílům se obvykle používá mechanismus „pronájmu“, aby se připojení čtenářů ke konkrétním oddílům koordinovala. Díky tomu je možné mít u každého oddílu ve skupině příjemců pouze jednoho aktivního čtenáře. Použití kontrolních bodů, pronájem a správu čtenářů zjednodušuje použití třídy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) pro klienty .NET. EventProcessorHost je inteligentní agent příjemce.

#### <a name="read-events"></a>Čtení událostí

Po otevření připojení a relace AMQP 1.0 u konkrétního oddílu služba Event Hubs doručí události do klienta protokolu AMQP 1.0. Tento mechanismus doručení umožňuje vyšší propustnost a nižší latenci než mechanismy založené na operaci Pull, jako je například metoda GET protokolu HTTP. Události se posílají klientovi a každá instance dat události obsahuje důležitá metadata, jako je posun a číslo posloupnosti, která se používají k zjednodušení vytváření kontrolních bodů v posloupnosti událostí.

Data události:
* Posun
* Pořadové číslo
* Tělo
* Uživatelské vlastnosti
* Systémové vlastnosti

Správa posunu je na vás.

## <a name="capacity"></a>Kapacita

Služba Event Hubs využívá vysoce škálovatelnou paralelní architekturu a při nastavování velikosti a škálování je potřeba zvážit několik klíčových aspektů.

### <a name="throughput-units"></a>Jednotky propustnosti

Kapacita propustnosti je ve službě Event Hubs řízená prostřednictvím *jednotek propustnosti*. Jednotky propustnosti jsou předem zakoupené jednotky kapacity. Jedna jednotka propustnosti zahrnuje následující kapacitu:

* Příchozí data: Až 1 MB za sekundu nebo 1000 událostí za sekundu (podle toho, co nastane dřív).
* Odchozí data: Až 2 MB za sekundu nebo 4096 událostí za sekundu.

Nad rámec kapacity zakoupených jednotek propustnosti je příjem příchozích dat omezen a vrátí se výjimka [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Odchozí data nezpůsobují takové výjimky, ale jsou omezená na objem přenosu dat, který poskytují zakoupené jednotky propustnosti. Pokud se vám objevují výjimky související s frekvencí publikování nebo v budoucnu očekáváte větší objem odchozích dat, zkontrolujte, kolik jednotek propustnosti jste pro konkrétní obor názvů zakoupili. Jednotky propustnosti můžete spravovat na **škálování** okně oboru názvů na [webu Azure portal](https://portal.azure.com). Můžete také spravovat programově pomocí jednotek propustnosti [rozhraní API Event Hubs](event-hubs-api-overview.md).

Jednotky propustnosti se kupují předem a se účtuje po hodinách. Zakoupené jednotky propustnosti se účtují minimálně za jednu hodinu. Propustnost až 20 jednotek pro obor názvů služby Event Hubs můžete zakoupit a jsou sdílené ve všech centrech event hubs v tomto oboru názvů.

Můžete zakoupit další jednotky propustnosti v blocích po 20 až 100 jednotek propustnosti, kontaktujte podporu Azure. Nad rámec tohoto limitu můžete zakoupit bloky, které 100 jednotek propustnosti.

Doporučujeme, abyste vyvážili jednotky propustnosti a oddíly, abyste dosáhli optimálního škálování. Škálování jednoho oddílu dovoluje maximálně jednu jednotku propustnosti. Počet jednotek propustnosti by měl být menší nebo roven počtu oddílů v centru událostí.

Podrobné informace o cenách služby Event Hubs najdete na stránce [Ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [kurz služby Event Hubs][Event Hubs tutorial]
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky služby Event Hubs][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Ukázky služby Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
