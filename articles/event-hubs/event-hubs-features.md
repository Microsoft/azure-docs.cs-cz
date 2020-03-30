---
title: Přehled funkcí – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobnosti o funkcích a terminologii Azure Event Hubs.
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
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281480"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkce a terminologie ve službě Azure Event Hubs

Azure Event Hubs je škálovatelná služba zpracování událostí, která ingestuje a zpracovává velké objemy událostí a dat s nízkou latencí a vysokou spolehlivostí. Podívejte se [na co jsou centra událostí?](event-hubs-what-is-event-hubs.md)

Tento článek vychází z informací v [článku s přehledem](event-hubs-what-is-event-hubs.md)a poskytuje technické a implementační podrobnosti o součástech a funkcích centra událostí.

## <a name="namespace"></a>Obor názvů
Obor názvů Event Hubs poskytuje jedinečný kontejner oboru, na který odkazuje [jeho plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ve kterém vytvoříte jeden nebo více center událostí nebo témata Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs pro Apache Kafka

[Tato funkce](event-hubs-for-kafka-ecosystem-overview.md) poskytuje koncový bod, který umožňuje zákazníkům mluvit s event huby pomocí protokolu Kafka. Tato integrace poskytuje zákazníkům koncový bod Kafka. To umožňuje zákazníkům konfigurovat své stávající aplikace Kafka pro rozhovor s event huby, což poskytuje alternativu ke spuštění vlastních clusterů Kafka. Event Hubs pro Apache Kafka podporuje protokol Kafka 1.0 a novější. 

S touto integrací nemusíte spouštět clustery Kafka ani je spravovat pomocí Zookeeperu. To také umožňuje pracovat s některými z nejnáročnějších funkcí event hubů, jako je zachycení, automatické nafouknutí a zotavení po po havárii.

Tato integrace také umožňuje aplikacím, jako je Mirror Maker nebo framework jako Kafka Connect, pracovat bez clusteru s pouhými změnami konfigurace. 

## <a name="event-publishers"></a>Zdroje událostí

Jakákoli entita, která odesílá data do centra událostí, je producentem událostí nebo *vydavatelem událostí*. Vydavatelé událostí mohou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0 nebo Kafka 1.0 a novějšího. Zdroje událostí se v centru událostí identifikují pomocí tokenu sdíleného přístupového podpisu (SAS) a mohou mít jedinečnou identitu, nebo mohou používat společný token SAS.

### <a name="publishing-an-event"></a>Publikování události

Událost můžete publikovat prostřednictvím AMQP 1.0, Kafka 1.0 (a novější) nebo HTTPS. Centra událostí poskytuje [klientské knihovny a třídy](event-hubs-dotnet-framework-api-overview.md) pro publikování událostí do centra událostí z klientů .NET. Pro jiné moduly runtime a platformy můžete použít libovolného klienta protokolu AMQP 1.0, například [Apache Qpid](https://qpid.apache.org/). Události můžete publikovat samostatně nebo v dávce. Jedna publikace (instance dat události) má limit 1 MB, bez ohledu na to, zda se jedná o jednu událost nebo dávku. Publikování událostí větších než tato prahová hodnota má za následek chybu. Je osvědčeným postupem pro vydavatele, aby nevěděli o oddílech v centru událostí a zadali pouze *klíč oddílu* (zavedený v další části) nebo jejich identitu prostřednictvím tokenu SAS.

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

[Funkce Event Hubs Capture](event-hubs-capture-overview.md) umožňuje automaticky zachytit streamovaná data v rozbočovačích událostí a uložit je do vašeho výběru účtu úložiště objektů Blob nebo účtu Služby Azure Data Lake Service. Můžete povolit zachycení z portálu Azure a určit minimální velikost a časové okno k provedení sběru. Pomocí služby Event Hubs Capture zadáte vlastní účet a kontejner azure blob storage nebo účet Azure Data Lake Service, z nichž jeden se používá k ukládání zachycených dat. Zachycená data jsou zapisována ve formátu Apache Avro.

## <a name="partitions"></a>Oddíly
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokeny SAS

Centra událostí používají *sdílené přístupové podpisy*, které jsou k dispozici na úrovni oboru názvů a centra událostí. Token SAS, který se generuje z klíče SAS, je adresa URL, která je zašifrovaná pomocí hašovacího algoritmu SHA a zakódovaná ve specifickém formátu. Služba Event Hubs může znovu vygenerovat hash kód pomocí názvu klíče (zásady) a tokenu, a ověřit tak odesílatele. Tokeny SAS pro vydavatele událostí se obvykle vytvářejí pouze s oprávněními **pro odesílání** v určitém centru událostí. Tento mechanismus adresy URL a tokenu SAS slouží jako základ pro identifikaci zdroje, kterou představíme v části o zásadách zdroje. Další informace o práci s tokenem SAS naleznete v tématu o [ověřování u služby Service Bus pomocí sdíleného přístupového podpisu](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Příjemci událostí

Jakákoli entita, která čte data událostí z centra událostí, je *příjemcem události*. Všichni příjemci se ve službě Event Hubs připojují pomocí relace protokolu AMQP 1.0 a události se doručují tak, jak jsou postupně dostupné. Klient se na dostupnost dat nemusí dotazovat.

### <a name="consumer-groups"></a>Skupiny příjemců

Mechanismus publikování/odebírání ve službě Event Hubs umožňují *skupiny příjemců*. Skupina příjemců je zobrazení (stavu, pozice nebo posunu) celého centra událostí. Skupiny příjemců poskytují různým přijímajícím aplikacím oddělená zobrazení datového proudu událostí a umožňují jim nezávisle číst datový proud vlastním tempem a s použitím vlastních posunů.

V architektuře zpracování datového proudu se každá aplikace pro příjem dat rovná skupině příjemců. Pokud chcete zapisovat data událostí do dlouhodobého úložiště, pak je aplikace, která do úložiště zapisuje, skupinou příjemců. Komplexní zpracování událostí zase může provádět jiná, samostatná skupina příjemců. K oddílům můžete přistupovat pouze prostřednictvím skupiny příjemců. V centru událostí je vždy jedna výchozí skupina příjemců. Na standardní úrovni centra událostí můžete vytvořit až dvacet skupin příjemců.

V oddílu na skupinu spotřebitelů může být maximálně 5 souběžných čteček. **Doporučuje se však, že v oddílu na skupinu příjemců je pouze jeden aktivní přijímač**. V rámci jednoho oddílu každý čtenář obdrží všechny zprávy. Pokud máte více čteček na stejném oddílu, pak zpracovat duplicitní zprávy. Musíte to zpracovat v kódu, který nemusí být triviální. V některých scénářích je však platný přístup.


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

Pokud se čtenář z oddílu odpojí, začne při opětovném připojení číst od kontrolního bodu, který dříve zaslal poslední čtenář daného oddílu z této skupiny příjemců. Když se čtečka připojí, předá posun do centra událostí a určí umístění, ve kterém má být možné začít číst. Takto můžete vytváření kontrolních bodů použít jak k označování událostí jako „dokončených“, tak k zajištění ochrany pro případ, že nastane selhání u čtenářů spuštěných na různých strojích. Ke starším datům se je možné vrátit tak, že určíte nižší posun od tohoto kontrolního bodu. Díky tomuto mechanismu umožňuje vytváření kontrolních bodů nejen obnovu při selhání, ale i opakované přehrání datového proudu.

### <a name="common-consumer-tasks"></a>Běžné úlohy příjemce

Všichni spotřebitelé event hubů se připojují prostřednictvím relace AMQP 1.0, což je obousměrný komunikační kanál podporující stav. Každý oddíl má relaci AMQP 1.0, která usnadňuje transport událostí rozdělených do oddílů.

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

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs naleznete pod těmito odkazy:

- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky centra událostí][]

[Ukázky centra událostí]: https://github.com/Azure/azure-event-hubs/tree/master/samples
