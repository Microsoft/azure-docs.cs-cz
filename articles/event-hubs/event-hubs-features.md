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
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157172"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkce a terminologii používané v Azure Event Hubs

Azure Event Hubs je škálovatelná služba, která ingestuje a zpracovává velké objemy událostí a dat, s nízkou latencí a vysokou spolehlivostí pro zpracování událostí. Podrobný přehled najdete v tématu [co je Event Hubs?](event-hubs-what-is-event-hubs.md) .

Tento článek sestaví informace v [článku Přehled](event-hubs-what-is-event-hubs.md)a poskytuje podrobnosti o technické a implementaci Event Hubs komponentách a funkcích.

## <a name="namespace"></a>Obor názvů
Obor názvů Event Hubs poskytuje jedinečný obor kontejneru, na který odkazuje jeho [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ve kterém můžete vytvořit jedno nebo několik témat centra událostí nebo Kafka témata. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs pro Apache Kafka

[Tato funkce](event-hubs-for-kafka-ecosystem-overview.md) poskytuje koncový bod, který zákazníkům umožňuje komunikovat s Event Hubs pomocí protokolu Kafka. Tato integrace poskytuje zákazníkům koncovým bodem v systému Kafka. To umožňuje zákazníkům konfigurace stávající aplikace Kafka ke komunikaci s Event Hubs, poskytuje alternativu ke spuštění vlastní clustery Kafka. Event Hubs pro Apache Kafka podporuje protokol Kafka 1.0 nebo novější. 

Díky této integraci nemusíte spouštět clustery Kafka ani je spravovat pomocí Zookeeper. To také umožňuje pracovat s některými z nejnáročnějších funkce Event Hubs jako zachycení, automatické rozšiřování a zotavení po havárii Geo.

Tato integrační také umožňuje aplikací, jako je Tvůrce zrcadlení nebo architekturu jako třeba připojit Kafka pracoval clusterless pouze změny konfigurace. 

## <a name="event-publishers"></a>Zdroje událostí

Každá entita, která odesílá data do centra událostí, je výrobcem událostí nebo *vydavatelem události*. Zdroje událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0 nebo Kafka 1.0 nebo novější. Zdroje událostí se v centru událostí identifikují pomocí tokenu sdíleného přístupového podpisu (SAS) a mohou mít jedinečnou identitu, nebo mohou používat společný token SAS.

### <a name="publishing-an-event"></a>Publikování události

Můžete publikovat události pomocí protokolu AMQP 1.0, Kafka, 1.0 (nebo novější) nebo HTTPS. Event Hubs poskytuje [klientské knihovny a třídy](event-hubs-dotnet-framework-api-overview.md) pro publikování událostí do centra událostí od klientů rozhraní .NET. Pro jiné moduly runtime a platformy můžete použít libovolného klienta protokolu AMQP 1.0, například [Apache Qpid](https://qpid.apache.org/). Události můžete publikovat samostatně nebo v dávce. Jedna publikace (instance dat události) je stanovený limit 1 MB, bez ohledu na to, zda se jedná o jedinou událost nebo dávku. Publikování události větší než tato prahová hodnota dojde k chybě. Nejvhodnějším postupem pro zdroje je nezajímat se o oddíly v centru událostí a specifikovat pouze *klíč oddílu* (představíme v další části) nebo svoji identitu prostřednictvím tokenu SAS.

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

Služba [Event Hubs Capture](event-hubs-capture-overview.md) vám umožní automaticky zachytit streamovaná data v Event Hubs a uložit je na základě vašeho výběru buď účtu úložiště BLOB, nebo účtu služby Azure Data Lake. Můžete povolit sběr dat z webu Azure portal a zadejte časové okno k provedení sběru a minimální velikost. Pomocí funkce Event Hubs Capture, můžete zadat vlastní účet služby Azure Blob Storage a kontejneru nebo účtu služby Azure Data Lake, jedním z nich se používá k ukládání zachycená data. Zachycená data je zapsána ve formátu Apache Avro.

## <a name="partitions"></a>Oddíly
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokeny SAS

Služba Event Hubs využívá *sdílené přístupové podpisy*, které jsou dostupné na úrovni oboru názvů a centra událostí. Token SAS, který se generuje z klíče SAS, je adresa URL, která je zašifrovaná pomocí hašovacího algoritmu SHA a zakódovaná ve specifickém formátu. Služba Event Hubs může znovu vygenerovat hash kód pomocí názvu klíče (zásady) a tokenu, a ověřit tak odesílatele. Za normálních okolností se tokeny SAS pro zdroje událostí vytváří jen s oprávněními pro **odesílání** na konkrétní centrum událostí. Tento mechanismus adresy URL a tokenu SAS slouží jako základ pro identifikaci zdroje, kterou představíme v části o zásadách zdroje. Další informace o práci s tokenem SAS naleznete v tématu o [ověřování u služby Service Bus pomocí sdíleného přístupového podpisu](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Příjemci událostí

*Příjemcem událostí* je každá entita, která čte data událostí z centra událostí. Všichni příjemci se ve službě Event Hubs připojují pomocí relace protokolu AMQP 1.0 a události se doručují tak, jak jsou postupně dostupné. Klient se na dostupnost dat nemusí dotazovat.

### <a name="consumer-groups"></a>Skupiny příjemců

Mechanismus publikování/odebírání ve službě Event Hubs umožňují *skupiny příjemců*. Skupina příjemců je zobrazení (stavu, pozice nebo posunu) celého centra událostí. Skupiny příjemců poskytují různým přijímajícím aplikacím oddělená zobrazení datového proudu událostí a umožňují jim nezávisle číst datový proud vlastním tempem a s použitím vlastních posunů.

V architektuře zpracování datového proudu se každá aplikace pro příjem dat rovná skupině příjemců. Pokud chcete zapisovat data událostí do dlouhodobého úložiště, pak je aplikace, která do úložiště zapisuje, skupinou příjemců. Komplexní zpracování událostí zase může provádět jiná, samostatná skupina příjemců. K oddílům můžete přistupovat pouze prostřednictvím skupiny příjemců. V centru událostí je vždy jedna výchozí skupina příjemců. Na standardní úrovni centra událostí můžete vytvořit až dvacet skupin příjemců.

U oddílu na skupinu příjemců může existovat maximálně 5 souběžných čtecích zařízení. doporučuje se ale **, aby v oddílu na skupinu příjemců byl jenom jeden aktivní přijímač**. V rámci jednoho oddílu každý Čtenář přijímá všechny zprávy. Pokud máte několik čtenářů do stejného oddílu, můžete proces duplicitní zprávy. Musíte se o to postarají ve vašem kódu, který nemusí být triviální. Je však platný přístup v některých scénářích.


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
* Text
* Uživatelské vlastnosti
* Systémové vlastnosti

Správa posunu je na vás.

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs naleznete pod těmito odkazy:

- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky Event Hubs][]

[Ukázky Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
