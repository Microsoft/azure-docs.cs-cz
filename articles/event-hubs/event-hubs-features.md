---
title: Přehled funkcí – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje podrobné informace o funkcích a terminologii Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9e004b3a8a9dd454eae5a20564a1ab74a26b66d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936227"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkce a terminologie ve službě Azure Event Hubs

Azure Event Hubs je škálovatelná služba pro zpracování událostí, která ingestuje a zpracovává velké objemy událostí a dat s nízkou latencí a vysokou spolehlivostí. Podrobný přehled najdete v tématu [co je Event Hubs?](./event-hubs-about.md) .

Tento článek sestaví informace v [článku Přehled](./event-hubs-about.md)a poskytuje podrobnosti o technické a implementaci Event Hubs komponentách a funkcích.

## <a name="namespace"></a>Obor názvů
Obor názvů Event Hubs poskytuje jedinečný obor kontejneru, na který odkazuje jeho [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ve kterém můžete vytvořit jedno nebo několik témat centra událostí nebo Kafka témata. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs pro Apache Kafka

[Tato funkce](event-hubs-for-kafka-ecosystem-overview.md) poskytuje koncový bod, který zákazníkům umožňuje komunikovat s Event Hubs pomocí protokolu Kafka. Tato integrace poskytuje zákazníkům koncový bod Kafka. Díky tomu můžou zákazníci nakonfigurovat své stávající aplikace Kafka, aby se mohli mluvit na Event Hubs, takže můžou spouštět vlastní clustery Kafka. Event Hubs pro Apache Kafka podporuje protokol Kafka 1,0 a novější. 

Díky této integraci nemusíte spouštět clustery Kafka ani je spravovat pomocí Zookeeper. To vám taky umožní pracovat s některými nejnáročnějšími funkcemi Event Hubs jako je zachycení, automatické rozploché a geografické zotavení po havárii.

Tato integrace také umožňuje aplikacím, jako je například reKafkaer nebo architektura, jako je například, připojit k pracovním clusterům bez jakýchkoli změn konfigurace. 

## <a name="event-publishers"></a>Zdroje událostí

Každá entita, která odesílá data do centra událostí, je výrobcem událostí nebo *vydavatelem události*. Vydavatelé událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1,0 nebo Kafka 1,0 a novějších. Zdroje událostí se v centru událostí identifikují pomocí tokenu sdíleného přístupového podpisu (SAS) a mohou mít jedinečnou identitu, nebo mohou používat společný token SAS.

### <a name="publishing-an-event"></a>Publikování události

Událost můžete publikovat přes AMQP 1,0, Kafka 1,0 (a novější) nebo HTTPS. Event Hubs poskytuje [klientské knihovny a třídy](./event-hubs-dotnet-framework-getstarted-send.md) pro publikování událostí do centra událostí od klientů rozhraní .NET. Pro jiné moduly runtime a platformy můžete použít libovolného klienta protokolu AMQP 1.0, například [Apache Qpid](https://qpid.apache.org/). Události můžete publikovat samostatně nebo v dávce. Jedna publikace (instance dat události) má omezení 1 MB bez ohledu na to, zda se jedná o jednu událost nebo dávku. Publikování událostí větších než tato prahová hodnota způsobí chybu. Osvědčeným postupem je, aby vydavatelé nevěděli o oddílech v centru událostí a zadali jenom *klíč oddílu* (zavedený v další části), nebo jejich identitu prostřednictvím tokenu SAS.

Volba, jestli se použije protokol AMQP nebo HTTPS, závisí na konkrétním scénáři použití. Protokol AMQP vyžaduje nejen protokol TLS (Transport Level Security) nebo SSL/TLS, ale i vytvoření trvalého obousměrného soketu. AMQP má při inicializaci relace vyšší náklady na síť, ale protokol HTTPS vyžaduje pro každý požadavek další režii TLS. AMQP má pro často používané zdroje vyšší výkon.

![Klíče oddílu](./media/event-hubs-features/partition_keys.png)

Služba Event Hubs zajišťuje, aby se všechny události, které sdílejí hodnotu klíče oddílu, v pořádku doručily do stejného oddílu. Pokud se klíče oddílů používají společně se zásadami zdroje, musí si identita zdroje a hodnota klíče oddílu odpovídat. V opačném případě dojde k chybě.

### <a name="publisher-policy"></a>Zásady zdroje

Služba Event Hubs umožňuje podrobnou kontrolu nad zdroji událostí prostřednictvím *zásad zdroje*. Zásady zdroje jsou běhové funkce, které byly navržené pro usnadnění kontroly nad velkým množstvím nezávislých zdrojů událostí. Zásady zdroje poskytují s použitím následujícího mechanismu každému zdroji vlastní identifikátor, který se používá při publikování událostí do centra událostí:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Názvy zdrojů není potřeba vytvářet dopředu, při publikování události ale musí odpovídat tokenu SAS, aby se zajistilo, že každý zdroj bude mít nezávislou identitu. Při použití zásad zdroje se každému názvu zdroje nastaví hodnota **PartitionKey** (Klíč oddílu). Aby vše správně fungovalo, musí tyto hodnoty odpovídat.

## <a name="capture"></a>Zachytávání

Služba [Event Hubs Capture](event-hubs-capture-overview.md) vám umožní automaticky zachytit streamovaná data v Event Hubs a uložit je na základě vašeho výběru buď účtu úložiště BLOB, nebo účtu služby Azure Data Lake. Můžete povolit zachytávání z Azure Portal a zadat minimální velikost a časový interval pro provedení zachycení. Pomocí služby Event Hubs Capture zadáte vlastní účet Azure Blob Storage a kontejner nebo účet služby Azure Data Lake, z nichž jedna se používá k uložení zachycených dat. Zachycená data se zapisují ve formátu Apache Avro.

## <a name="partitions"></a>Oddíly
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokeny SAS

Event Hubs používá *sdílené přístupové podpisy*, které jsou k dispozici na úrovni oboru názvů a centra událostí. Token SAS, který se generuje z klíče SAS, je adresa URL, která je zašifrovaná pomocí hašovacího algoritmu SHA a zakódovaná ve specifickém formátu. Služba Event Hubs může znovu vygenerovat hash kód pomocí názvu klíče (zásady) a tokenu, a ověřit tak odesílatele. Za normálních okolností se tokeny SAS pro vydavatele událostí vytvářejí jenom s oprávněními pro **odesílání** na konkrétní centrum událostí. Tento mechanismus adresy URL a tokenu SAS slouží jako základ pro identifikaci zdroje, kterou představíme v části o zásadách zdroje. Další informace o práci s tokenem SAS naleznete v tématu o [ověřování u služby Service Bus pomocí sdíleného přístupového podpisu](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Příjemci událostí

Každá entita, která čte data událostí z centra událostí, je *příjemce událostí*. Všichni příjemci se ve službě Event Hubs připojují pomocí relace protokolu AMQP 1.0 a události se doručují tak, jak jsou postupně dostupné. Klient se na dostupnost dat nemusí dotazovat.

### <a name="consumer-groups"></a>Skupiny uživatelů

Mechanismus publikování/odebírání ve službě Event Hubs umožňují *skupiny příjemců*. Skupina příjemců je zobrazení (stavu, pozice nebo posunu) celého centra událostí. Skupiny příjemců poskytují různým přijímajícím aplikacím oddělená zobrazení datového proudu událostí a umožňují jim nezávisle číst datový proud vlastním tempem a s použitím vlastních posunů.

V architektuře zpracování datového proudu se každá aplikace pro příjem dat rovná skupině příjemců. Pokud chcete zapisovat data událostí do dlouhodobého úložiště, pak je aplikace, která do úložiště zapisuje, skupinou příjemců. Komplexní zpracování událostí zase může provádět jiná, samostatná skupina příjemců. K oddílům můžete přistupovat pouze prostřednictvím skupiny příjemců. V centru událostí je vždy jedna výchozí skupina příjemců. Na standardní úrovni centra událostí můžete vytvořit až dvacet skupin příjemců.

U oddílu na skupinu příjemců může existovat maximálně 5 souběžných čtecích zařízení. doporučuje se ale **, aby v oddílu na skupinu příjemců byl jenom jeden aktivní přijímač**. Každý čtenář v rámci jednoho oddílu obdrží všechny zprávy. Pokud máte na stejném oddílu více čtenářů, budete zpracovávat duplicitní zprávy. To je třeba zpracovat v kódu, který nemusí být triviální. V některých scénářích se ale jedná o platný přístup.

Někteří klienti, kteří nabízejí sady Azure SDK, jsou inteligentní agenti pro zákazníky, kteří automaticky spravují podrobnosti o tom, že každý oddíl má jedno čtecí zařízení a že se z něj čtou všechny oddíly centra událostí. Díky tomu se váš kód může soustředit na zpracování událostí čtených z centra událostí, aby mohl ignorovat mnoho podrobností o těchto oddílech. Další informace najdete v tématu [připojení k oddílu](#connect-to-a-partition).

Následující příklady znázorňují konvenci identifikátoru URI pro skupinu příjemců:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

Následující obrázek znázorňuje architekturu zpracování datového proudu Event Hubs:

![Architektura Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Posuny datového proudu

*Posun* je pozice události v rámci oddílu. Posun si můžete představit jako kurzor na straně klienta. Posun je číslo bajtu události. Tento posun umožňuje příjemci události (čtenáři) určit bod v datovém proudu událostí, od kterého chce události začít číst. Posun můžete zadat v podobě časového razítka nebo hodnoty posunu. Příjemci si sami zodpovídají za uložení svých hodnot posunu mimo službu Event Hubs. Každá událost v rámci oddílu zahrnuje posun.

![Posun oddílu](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Vytváření kontrolních bodů

*Vytváření kontrolních bodů* je proces, pomocí kterého čtenáři označují nebo potvrzují svou pozici v rámci posloupnosti událostí v oddílu. Za vytváření kontrolních bodů zodpovídá příjemce. Proces probíhá na bázi oddílů ve skupinách příjemců. Taková zodpovědnost znamená, že si každý čtenář oddílu v každé skupině příjemců musí udržovat přehled o své aktuální pozici v datovém proudu událostí a může informovat službu, když bude považovat datový proud za dokončený.

Pokud se čtenář z oddílu odpojí, začne při opětovném připojení číst od kontrolního bodu, který dříve zaslal poslední čtenář daného oddílu z této skupiny příjemců. Když se čtenář připojí, předá posun do centra událostí, aby určil umístění, ve kterém se má začít číst. Takto můžete vytváření kontrolních bodů použít jak k označování událostí jako „dokončených“, tak k zajištění ochrany pro případ, že nastane selhání u čtenářů spuštěných na různých strojích. Ke starším datům se je možné vrátit tak, že určíte nižší posun od tohoto kontrolního bodu. Díky tomuto mechanismu umožňuje vytváření kontrolních bodů nejen obnovu při selhání, ale i opakované přehrání datového proudu.

> [!NOTE]
> Pokud používáte Azure Blob Storage jako úložiště kontrolního bodu v prostředí, které podporuje jinou verzi sady SDK pro úložiště objektů blob, než jaké jsou běžně dostupné v Azure, budete muset použít kód ke změně verze rozhraní API služby úložiště na konkrétní verzi podporovanou tímto prostředím. Pokud například používáte [Event Hubs v centru Azure Stack verze 2002](/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze služby úložiště je verze 2017-11-09. V takovém případě je nutné použít kód pro cílení na verzi rozhraní API služby úložiště na 2017-11-09. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v těchto ukázkách na GitHubu: 
> - [Rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) nebo  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Běžné úlohy příjemce

Všichni Event Hubs spotřebitelé se připojují prostřednictvím relace AMQP 1,0, obousměrného komunikačního kanálu s podporou stavu. Každý oddíl má relaci AMQP 1.0, která usnadňuje transport událostí rozdělených do oddílů.

#### <a name="connect-to-a-partition"></a>Připojení k oddílu

Při připojování k oddílům je běžné použití mechanismu leasingu ke koordinaci připojení čtenářů ke konkrétním oddílům. Díky tomu je možné, že každý oddíl ve skupině příjemců má jenom jeden aktivní čtecí modul. Vytváření kontrolních bodů, zapůjčení a Správa čtenářů se zjednodušují pomocí klientů v rámci Event Hubs SDK, které fungují jako inteligentní agenti zákazníků. Jsou to:

- [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) pro .NET
- [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) pro jazyk Java
- [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) pro Python
- [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) pro JavaScript/TypeScript

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
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky Event Hubs](event-hubs-samples.md)
