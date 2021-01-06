---
title: Přehled funkcí – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje podrobné informace o funkcích a terminologii Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 0730a5fa3abbc6b27cb96431125564a2475a90d1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955637"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkce a terminologie ve službě Azure Event Hubs

Azure Event Hubs je škálovatelná služba pro zpracování událostí, která ingestuje a zpracovává velké objemy událostí a dat s nízkou latencí a vysokou spolehlivostí. Podrobný přehled najdete v tématu [co je Event Hubs?](./event-hubs-about.md) .

Tento článek sestaví informace v [článku Přehled](./event-hubs-about.md)a poskytuje podrobnosti o technické a implementaci Event Hubs komponentách a funkcích.

> [!TIP]
> [Podpora protokolu pro klienty **Apache Kafka**](event-hubs-for-kafka-ecosystem-overview.md)  (verze >= 1,0) poskytuje koncové body sítě, které umožňují aplikacím postaveným na použití Apache Kafka s jakýmkoli klientem pro použití Event Hubs. Většinu stávajících aplikací Kafka je možné jednoduše znovu nakonfigurovat tak, aby odkazovaly na obor názvů centra událostí místo na zaváděcí Server clusteru Kafka. 
>
>Z hlediska nákladů, provozní úsilí a spolehlivosti je Azure Event Hubs skvělou alternativou k nasazení a provozování vlastních clusterů Kafka a Zookeeper a k tomu, aby nabídky Kafka jako služby nebyly nativní pro Azure. 
>
> Kromě toho, že máte stejné základní funkce jako zprostředkovatel Apache Kafka, získáte také přístup k funkcím centra událostí Azure, jako je automatické vytváření dávek a archivování prostřednictvím služby [Event Hubs Capture](event-hubs-capture-overview.md), automatického škálování a vyrovnávání zatížení, zotavení po havárii, neneutrální podpoře zón dostupnosti, flexibilní a zabezpečené integrace sítě a podpora více protokolů, včetně protokolu AMQP-over-WebSockets.


## <a name="namespace"></a>Obor názvů
Obor názvů Event Hubs poskytuje integrované síťové koncové body DNS a řadu funkcí řízení přístupu a správy integrace sítě, jako je [filtrování IP adres](event-hubs-ip-filtering.md), [koncový bod služby virtuální sítě](event-hubs-service-endpoints.md)a [privátní odkaz](private-link-service.md) a je kontejner správy pro jednu z několika instancí centra událostí (nebo témata v Kafka agilním).

## <a name="event-publishers"></a>Zdroje událostí

Každá entita, která odesílá data do centra událostí, je *vydavatelem událostí* (jako synonyma se používá u *výrobce událostí*). Vydavatelé událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1,0 nebo protokolu Kafka. Vydavatelé událostí používají ověřování založené na Azure Active Directory s tokeny JWT vydanými OAuth2 nebo tokenem sdíleného přístupového podpisu specifického pro centrum událostí, který získá přístup pro publikování.

### <a name="publishing-an-event"></a>Publikování události

Událost můžete publikovat prostřednictvím AMQP 1,0, protokolu Kafka nebo protokolu HTTPS. Služba Event Hubs poskytuje klientské knihovny pro [REST API](/rest/api/eventhub/) a [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)a na [cestách](event-hubs-go-get-started-send.md) pro publikování událostí do centra událostí. Pro jiné moduly runtime a platformy můžete použít libovolného klienta protokolu AMQP 1.0, například [Apache Qpid](https://qpid.apache.org/). 

Volba, jestli se použije protokol AMQP nebo HTTPS, závisí na konkrétním scénáři použití. Protokol AMQP vyžaduje nejen protokol TLS (Transport Level Security) nebo SSL/TLS, ale i vytvoření trvalého obousměrného soketu. AMQP má při inicializaci relace vyšší náklady na síť, ale protokol HTTPS vyžaduje pro každý požadavek další režii TLS. AMQP má významně vyšší výkon pro časté vydavatele a při použití s asynchronním kódem publikování může dosáhnout mnohem nižších latencí.

Události můžete publikovat jednotlivě nebo v dávce. Jedna publikace má omezení 1 MB, bez ohledu na to, zda se jedná o jednu událost nebo dávku. Události, které jsou větší než tato prahová hodnota, budou odmítnuty. 

Event Hubs propustnost se škáluje pomocí oddílů a přidělení jednotek propustnosti (viz níže). Osvědčeným postupem je, že vydavatelé zůstanou nevědomé konkrétního modelu dělení vybraného pro centrum událostí a jenom určují *klíč oddílu* , který se používá k konzistentnímu přiřazování souvisejících událostí ke stejnému oddílu.

![Klíče oddílu](./media/event-hubs-features/partition_keys.png)

Event Hubs zajišťuje, aby všechny události sdílející hodnotu klíče oddílu byly uloženy společně a doručeny v pořadí doručení. Pokud se klíče oddílů používají společně se zásadami zdroje, musí si identita zdroje a hodnota klíče oddílu odpovídat. V opačném případě dojde k chybě.

### <a name="event-retention"></a>Uchovávání událostí

Publikované události se odeberou z centra událostí na základě konfigurovatelné zásady uchovávání na základě časových limitů. Výchozí hodnota a nejkratší možné období uchování je 1 den (24 hodin). Pro Event Hubs Standard je maximální doba uchovávání 7 dní. V případě Event Hubs úrovně Dedicated je maximální doba uchování 90 dní.

> [!NOTE]
> Event Hubs je modul streamování událostí v reálném čase, který není určený pro použití namísto databáze nebo jako trvalé úložiště pro nekonečné uchovávání datových proudů událostí. 
> 
> Čím hlubší je historie datového proudu událostí, tím více budete potřebovat pomocné indexy pro vyhledání konkrétního historického řezu daného datového proudu. Kontrola datových částí a indexování událostí není v oboru funkcí Event Hubs (nebo Apache Kafka). Databáze a specializované analytické obchody a služby, jako je například [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) a [Azure synapse](../synapse-analytics/overview-what-is.md) jsou proto mnohem vhodnější pro ukládání historických událostí.
>
> [Event Hubs Capture](event-hubs-capture-overview.md) se integruje přímo s Azure Blob Storage a Azure Data Lake Storage a prostřednictvím této integrace taky umožňuje [přesměrovat události přímo do Azure synapse](store-captured-data-data-warehouse.md).
>
> Pokud chcete pro vaši aplikaci použít vzor typu [události](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) , měli byste sjednotit strategii snímků s omezeními uchovávání Event Hubs. Nevytvářejte záměr znovu sestavit materializovaná zobrazení z nezpracovaných událostí počínaje začátkem času. Měli byste se surely na tuto strategii, když je vaše aplikace v produkčním prostředí pro dobu, kdy se používá, a váš tvůrce projekce se při pokusu o zachycení až do nejnovějších a probíhajících změn musí rozniknout během let. 


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
> - [Rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
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