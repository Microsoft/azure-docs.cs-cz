---
title: Přehled funkcí Azure Event Hubs | Microsoft Docs
description: Přehled a podrobnosti o funkcích Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: sethm
ms.openlocfilehash: f16f8aa73ecfa3e0a47ce2373a2e28a7a9968ff5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248737"
---
# <a name="event-hubs-features-overview"></a>Přehled funkcí služby Event Hubs

Azure Event Hubs je škálovatelnou událostí zpracování služba, která ingestuje a zpracovává velké objemy události a data, s nízkou latencí a vysokou spolehlivostí. V tématu [co je Služba Event Hubs?](event-hubs-what-is-event-hubs.md) souhrnné informace.

Tento článek vychází informace v [článek s přehledem](event-hubs-what-is-event-hubs.md)a poskytuje implementace a technické podrobnosti o Event Hubs komponenty a funkce.

## <a name="event-publishers"></a>Zdroje událostí

Každá entita, která odesílá data do centra událostí je producent události nebo *vydavatel události*. Zdroje událostí mohou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0. Zdroje událostí se v centru událostí identifikují pomocí tokenu sdíleného přístupového podpisu (SAS) a mohou mít jedinečnou identitu, nebo mohou používat společný token SAS.

### <a name="publishing-an-event"></a>Publikování události

Událost můžete publikovat prostřednictvím protokolu AMQP 1.0 nebo HTTPS. Event Hubs poskytuje [knihovny klienta a třídy](event-hubs-dotnet-framework-api-overview.md) k publikování událostí do centra událostí od klientů .NET. Pro jiné moduly runtime a platformy můžete použít libovolného klienta protokolu AMQP 1.0, například [Apache Qpid](http://qpid.apache.org/). Události můžete publikovat samostatně nebo v dávce. Jedna publikace (instance dat události) je omezena limitem 256 KB – bez ohledu na to, jestli se jedná o jedinou událost nebo dávku (batch). Publikování události větší než tato prahová hodnota dojde k chybě. Nejvhodnějším postupem pro zdroje je nezajímat se o oddíly v centru událostí a specifikovat pouze *klíč oddílu* (představíme v další části) nebo svoji identitu prostřednictvím tokenu SAS.

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

[Zaznamenat centra událostí](event-hubs-capture-overview.md) umožňuje automaticky zachycení dat ve službě Event Hubs a uložit ho do svého výběru účtu úložiště Blob nebo účet služby Azure Data Lake. Můžete povolit sběr dat z portálu Azure a zadejte časové okno k zaznamenání a minimální velikost. Použití funkce Capture centra událostí, je zadat vlastní účet Azure Blob Storage a kontejner nebo účet služby Azure Data Lake, z nichž jeden se používá k ukládání zaznamenaná data. Zaznamenaná data je zapsaný ve formátu Apache Avro.

## <a name="partitions"></a>Oddíly

Služba Event Hubs poskytuje datový proud zpráv pomocí schématu rozdělujícího datový proud na oddíly pro jednotlivé příjemce. To zajišťuje, aby každý příjemce četl jenom konkrétní podmnožinu nebo oddíl datového proudu zpráv. Toto schéma umožňuje vodorovné škálování zpracování událostí a poskytuje další funkce zaměřené na datový proud, které nejsou ve frontách a tématech k dispozici.

Oddíl je seřazená posloupnost událostí, která se nachází v centru událostí. Události, které nově přichází, se zařazují na konec této posloupnosti. Oddíl si lze představit jako „protokol transakcí“.

![Event Hubs](./media/event-hubs-features/partition.png)

Služba Event Hubs uchovává data pro dobu nakonfigurované uchování, která platí pro všechny oddíly události rozbočovače. Události mizí na základě času, nemůžete je explicitně odstranit. Vzhledem k tomu, že oddíly jsou nezávislé a obsahují vlastní posloupnost dat, často se jejich velikost mění různým tempem.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů není možné měnit. Proto je při nastavování počtu oddílů potřeba uvažovat z dlouhodobého hlediska. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Pokud chcete použít vyšší počet oddílů než 32, kontaktujte tým služby Event Hubs.

Při oddíly identifikovat a mohou být odesílány přímo, odesílání přímo na oddíl se nedoporučuje. Místo toho můžete použít konstrukce vyšší úrovně, které představujeme v tématech věnovaných [zdroji událostí](#event-publishers) a [kapacitě](#capacity). 

Oddíly jsou naplněné posloupností dat událostí, která obsahují tělo události, uživatelem definovaný kontejner objektů a dat a různá metadata události, jako je třeba posun v rámci oddílu a pořadí v posloupnosti datového proudu.

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

V architektuře zpracování datového proudu se každá aplikace pro příjem dat rovná skupině příjemců. Pokud chcete zapisovat data událostí do dlouhodobého úložiště, pak je aplikace, která do úložiště zapisuje, skupinou příjemců. Komplexní zpracování událostí zase může provádět jiná, samostatná skupina příjemců. K oddílům můžete přistupovat pouze prostřednictvím skupiny příjemců. Může být maximálně 5 souběžných čtenářů na oddíl na skupinu uživatelů; ale **se doporučuje, zda je na oddíl na skupinu spotřebitelů pouze jeden aktivní příjemce**. V centru událostí je vždy jedna výchozí skupina příjemců. Na standardní úrovni centra událostí můžete vytvořit až dvacet skupin příjemců.

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

Pokud se čtenář z oddílu odpojí, začne při opětovném připojení číst od kontrolního bodu, který dříve zaslal poslední čtenář daného oddílu z této skupiny příjemců. Když se čtenář připojí, předá tento posun do centra událostí, a určí tak umístění, od kterého začne číst. Takto můžete vytváření kontrolních bodů použít jak k označování událostí jako „dokončených“, tak k zajištění ochrany pro případ, že nastane selhání u čtenářů spuštěných na různých strojích. Ke starším datům se je možné vrátit tak, že určíte nižší posun od tohoto kontrolního bodu. Díky tomuto mechanismu umožňuje vytváření kontrolních bodů nejen obnovu při selhání, ale i opakované přehrání datového proudu.

### <a name="common-consumer-tasks"></a>Běžné úlohy příjemce

Všichni příjemci Event Hubs připojení přes relaci protokolu AMQP 1.0, stav obousměrný komunikační kanál. Každý oddíl má relaci AMQP 1.0, která usnadňuje transport událostí rozdělených do oddílů.

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

* Příchozí data: Až 1 MB za sekundu nebo 1000 událostí za sekundu (podle toho, co nastane dříve)
* Odchozí data: Až 2 MB za sekundu

Nad rámec kapacity zakoupených jednotek propustnosti je příjem příchozích dat omezen a vrátí se výjimka [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Odchozí data nezpůsobují takové výjimky, ale jsou omezená na objem přenosu dat, který poskytují zakoupené jednotky propustnosti. Pokud se vám objevují výjimky související s frekvencí publikování nebo v budoucnu očekáváte větší objem odchozích dat, zkontrolujte, kolik jednotek propustnosti jste pro konkrétní obor názvů zakoupili. Jednotky propustnosti můžete spravovat na **škálování** okno obory názvů v [portál Azure](https://portal.azure.com). Můžete také spravovat jednotky propustnosti programově pomocí [rozhraní API centra událostí](event-hubs-api-overview.md).

Jednotky propustnosti jsou předem zakoupené a fakturují se za hodinu. Zakoupené jednotky propustnosti se účtují minimálně za jednu hodinu. Až 20 propustnost jednotky můžete zakoupili pro obor názvů Event Hubs a jsou sdíleny ve všech centrech událostí v daném oboru názvů.

Další jednotky propustnosti je možné zakoupit v blocích po 20 jednotkách propustnosti (maximálně 100) po domluvě s pracovníky podpory Azure. Kromě toho si můžete zakoupit bloky 100 jednotek propustnosti.

Doporučujeme vám, že můžete vyrovnávat jednotky propustnosti a oddíly, abyste dosáhli optimálního škálování. Škálování jednoho oddílu dovoluje maximálně jednu jednotku propustnosti. Počet jednotek propustnosti by měl být menší nebo roven počtu oddílů v centru událostí.

Podrobné informace o cenách služby Event Hubs najdete na stránce [Ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [kurz služby Event Hubs][Event Hubs tutorial]
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky centra událostí][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Ukázky centra událostí]: https://github.com/Azure/azure-event-hubs/tree/master/samples
