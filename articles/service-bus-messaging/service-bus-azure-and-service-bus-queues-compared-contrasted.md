---
title: Porovnání front úložiště Azure a front service bus
description: Analyzuje rozdíly a podobnosti mezi dvěma typy front nabízených Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ffa98e511053edc75fd0e6f25f7b0e21ee9ddda0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414530"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Fronty úložiště a fronty service bus – porovnání a porovnání
Tento článek analyzuje rozdíly a podobnosti mezi dvěma typy front, které dnes nabízí Microsoft Azure: fronty úložiště a fronty service bus. Pomocí těchto informací můžete porovnat a porovnat příslušné technologie a být schopni učinit informovanější rozhodnutí o tom, které řešení nejlépe vyhovuje vašim potřebám.

## <a name="introduction"></a>Úvod
Azure podporuje dva typy mechanismů fronty: **fronty úložiště** a fronty **Service Bus**.

**Fronty úložiště**, které jsou součástí infrastruktury [úložiště Azure,](https://azure.microsoft.com/services/storage/) jsou vybaveny jednoduchým rozhraním GET/PUT/PEEK založeným na rest, které poskytuje spolehlivé a trvalé zasílání zpráv v rámci služeb a mezi nimi.

**Fronty service bus** jsou součástí širší infrastruktury [zasílání zpráv Azure,](https://azure.microsoft.com/services/service-bus/) která podporuje řazení do fronty, publikování a přihlášení k odběru a pokročilejší vzory integrace. Další informace o frontách/tématech/odběrech služby Service Bus naleznete v [přehledu služby Service Bus](service-bus-messaging-overview.md).

Zatímco obě technologie řazení do fronty existují souběžně, fronty úložiště byly zavedeny jako první, jako vyhrazený mechanismus úložiště fronty postavený na službách Azure Storage. Fronty service bus jsou postaveny na širší infrastruktuře zasílání zpráv určené k integraci aplikací nebo součástí aplikací, které mohou zahrnovat více komunikačních protokolů, kontraktů dat, důvěryhodných domén nebo síťových prostředí.

## <a name="technology-selection-considerations"></a>Aspekty výběru technologií
Fronty úložiště i fronty service bus jsou implementace služby řízení front zpráv aktuálně nabízené microsoft azure. Každý z nich má mírně odlišnou sadu funkcí, což znamená, že si můžete vybrat jednu nebo druhou, nebo použít obojí, v závislosti na potřebách vašeho konkrétního řešení nebo obchodního / technického problému, který řešíte.

Při určování, která technologie řazení do fronty odpovídá účelu pro dané řešení, by architekti a vývojáři řešení měli zvážit tato doporučení. Další podrobnosti naleznete v další části.

Jako architekt/vývojář řešení **byste měli zvážit použití front úložiště,** když:

* Aplikace musí uložit více než 80 GB zpráv ve frontě.
* Aplikace chce sledovat průběh zpracování zprávy uvnitř fronty. To je užitečné, pokud dojde k chybě pracovníka zpracovávajícího zprávu. Následný pracovník pak může tyto informace použít k pokračování od místa, kde předchozí pracovník skončil.
* Vyžadujete protokoly na straně serveru všech transakcí provedených proti frontám.

Jako architekt/vývojář řešení **byste měli zvážit použití front service bus,** když:

* Vaše řešení musí být schopen přijímat zprávy bez nutnosti dotazování fronty. Pomocí service bus, toho lze dosáhnout pomocí operace příjem dlouho dotazování pomocí protokolů založených na protokolu TCP, který podporuje service bus.
* Vaše řešení vyžaduje fronty poskytnout zaručené první in-first-out (FIFO) objednané doručení.
* Vaše řešení musí být schopno podporovat automatické vyhledávání duplicit.
* Chcete, aby aplikace zpracovávat zprávy jako paralelní dlouhotrvající datové proudy (zprávy jsou přidruženy k datovému proudu pomocí [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) vlastnost na zprávu). V tomto modelu každý uzel v náročné aplikace soutěží o datové proudy, na rozdíl od zpráv. Když je datový proud přidělen náročnému uzlu, uzel může zkontrolovat stav stavu datového proudu aplikace pomocí transakcí.
* Vaše řešení vyžaduje transakční chování a nedělitosti při odesílání nebo přijímání více zpráv z fronty.
* Aplikace zpracovává zprávy, které mohou překročit 64 kB, ale pravděpodobně se nepřiblíží limitu 256 kB.
* Zabýváte se požadavkem na poskytnutí modelu přístupu na základě rolí do front a s různými právy a oprávněními pro odesílatele a příjemce. Další informace najdete v těchto článcích:
    - [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
    - [Ověření z aplikace](authenticate-application.md)
* Velikost fronty nepřekročí 80 GB.
* Chcete použít protokol zasílání zpráv založený na standardech AMQP 1.0. Další informace o službě AMQP naleznete v tématu [Service Bus AMQP Overview](service-bus-amqp-overview.md).
* Můžete si představit případnou migraci z komunikace typu point-to-point založené na frontě na vzor výměny zpráv, který umožňuje bezproblémovou integraci dalších příjemců (odběratelů), z nichž každý obdrží nezávislé kopie některých nebo všech zpráv odeslaných do fronty. Ten odkazuje na možnost publikování/odběru nativně poskytované Service Bus.
* Vaše řešení zasílání zpráv musí být schopno podporovat záruku doručení "At-Most-Once" bez nutnosti vytvářet další součásti infrastruktury.
* Chcete mít možnost publikovat a používat dávky zpráv.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porovnání front úložiště a front service bus
Tabulky v následujících částech poskytují logické seskupení funkcí fronty a umožňují porovnat na první pohled funkce dostupné ve frontách služby Azure Storage i ve frontách Service Bus.

## <a name="foundational-capabilities"></a>Základní schopnosti
Tato část porovnává některé základní možnosti řazení do fronty poskytované fronty úložiště a fronty service bus.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Záruka objednávání |**Ne** <br/><br>Další informace naleznete v první poznámce v části Další informace.</br> |**Ano - první-in-first-out (FIFO)**<br/><br>(pomocí e-mailových relací) |
| Záruka dodání |**Alespoň jednou** |**At-Least-Once** (pomocí peeklock přijímacího režimu - to je výchozí) <br/><br/>**At-Most-Once** (pomocí receiveAndDelete režimu příjmu) <br/> <br/> Další informace o různých [režimech příjmu](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Podpora atomového provozu |**Ne** |**Ano**<br/><br/> |
| Chování příjmu |**Neblokující**<br/><br/>(dokončí se okamžitě, pokud není nalezena žádná nová zpráva) |**Blokování s/bez časového času**<br/><br/>(nabízí dlouhé hlasování, nebo ["Kometa technika"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Neblokující**<br/><br/>(pouze pomocí rozhraní API spravovaného rozhraní .NET) |
| Rozhraní API ve stylu push |**Ne** |**Ano**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) a [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) relací .NET API. |
| Režim příjmu |**Náhled & zapůjčení** |**Peek & zámek**<br/><br/>**Příjem & odstranit** |
| Režim exkluzivního přístupu |**Na základě leasingu** |**Na základě zámku** |
| Doba trvání zapůjčení/zámku |**30 sekund (výchozí)**<br/><br/>**7 dní (maximálně)** (Zapůjčení zprávy můžete obnovit nebo uvolnit pomocí rozhraní [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API.) |**60 sekund (výchozí)**<br/><br/>Zámek zprávy můžete obnovit pomocí rozhraní [REnewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| Přesnost zapůjčení/zámku |**Úroveň zprávy**<br/><br/>(každá zpráva může mít jinou hodnotu časového času, kterou pak můžete podle potřeby aktualizovat při zpracování zprávy pomocí [rozhraní UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API) |**Úroveň fronty**<br/><br/>(každá fronta má přesnost zámku použitou pro všechny jeho zprávy, ale můžete obnovit zámek pomocí [rozhraní REnewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Dávkové přijetí |**Ano**<br/><br/>(explicitně určující počet zpráv při načítání zpráv, maximálně 32 zpráv) |**Ano**<br/><br/>(implicitně povolení pre-fetch vlastnost nebo explicitně pomocí transakcí) |
| Dávkové odeslání |**Ne** |**Ano**<br/><br/>(pomocí transakcí nebo dávkování na straně klienta) |

### <a name="additional-information"></a>Další informace
* Zprávy ve frontách úložiště jsou obvykle první v prvním ven, ale někdy mohou být mimo pořadí; například když vyprší doba trvání časového limitu viditelnosti zprávy (například v důsledku selhání klientské aplikace během zpracování). Po vypršení časového limitu viditelnosti se zpráva znovu zobrazí ve frontě, aby ji jiný pracovník vyřadil z fronty. V tomto okamžiku může být nově viditelná zpráva umístěna do fronty (chcete-li znovu dequeued) po zprávě, která byla původně zařazena do fronty za ním.
* Zaručený vzor FIFO ve frontách service bus vyžaduje použití relací zasílání zpráv. V případě, že dojde k chybě aplikace při zpracování zprávy přijaté v režimu **Peek & Lock,** při příštím přijetí relace zasílání zpráv ve frontě příjemce, začne s chybnou zprávu po vypršení doby jeho time-to-live (TTL) období.
* Fronty úložišť jsou navrženy tak, aby podporovaly standardní scénáře řazení do fronty, jako je oddělení součástí aplikace za účelem zvýšení škálovatelnosti a tolerance pro selhání, vyrovnávání zatížení a pracovní postupy procesu vytváření.
* Nekonzistence s ohledem na zpracování zpráv v kontextu relace service bus lze vyhnout pomocí stavu relace k uložení stavu aplikace vzhledem k průběhu zpracování sekvenci zpráv relace a pomocí transakcí kolem vyrovnání přijatých zpráv a aktualizace stavu relace. Tento druh konzistence funkce je někdy označen *přesně jednou zpracování* v produktech jiných dodavatelů, ale selhání transakce samozřejmě způsobí zprávy, které mají být znovu doručeny, a proto termín není přesně adekvátní.
* Fronty úložiště poskytují jednotný a konzistentní programovací model napříč frontami, tabulkami a objekty BLOB – pro vývojáře i pro provozní týmy.
* Fronty služby Service Bus poskytují podporu pro místní transakce v kontextu jedné fronty.
* Režim **příjmu a odstranění** podporovaný službou Service Bus umožňuje snížit počet operací zasílání zpráv (a související náklady) výměnou za sníženou záruku doručení.
* Fronty úložiště poskytují zapůjčení s možností rozšířit zapůjčení pro zprávy. To umožňuje pracovníkům udržovat krátké zapůjčení zpráv. Pokud tedy dojde k chybě pracovníka, může být zpráva rychle zpracovat znovu jiným pracovníkem. Kromě toho pracovník může prodloužit zapůjčení zprávy, pokud potřebuje zpracovat déle než aktuální doba zapůjčení.
* Fronty úložiště nabízejí časový limit viditelnosti, který můžete nastavit při zavádění nebo dequeuing zprávy. Kromě toho můžete aktualizovat zprávu s různými hodnotami zapůjčení za běhu a aktualizovat různé hodnoty mezi zprávami ve stejné frontě. Časové výtky uzamčení služby Service Bus jsou definovány v metadatech fronty. můžete však obnovit zámek voláním [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metoda.
* Maximální časový limit pro blokování operace příjmu ve frontách service bus je 24 dní. Časové limity založené na rest mají však maximální hodnotu 55 sekund.
* Dávkování na straně klienta poskytované službou Service Bus umožňuje klientovi fronty dávkově více zpráv do jedné operace odeslání. Dávkování je k dispozici pouze pro operace asynchronního odesílání.
* Funkce, jako je 200 TB strop front úložiště (více při virtualizaci účtů) a neomezené fronty, aby bylo ideální platformu pro poskytovatele SaaS.
* Fronty úložiště poskytují flexibilní a výkonný delegovaný mechanismus řízení přístupu.

## <a name="advanced-capabilities"></a>Pokročilé možnosti
Tato část porovnává pokročilé možnosti poskytované fronty úložiště a fronty service bus.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Naplánované doručení |**Ano** |**Ano** |
| Automatické nedoručené písmo |**Ne** |**Ano** |
| Zvýšení hodnoty doby čekání na aktivní vysílání |**Ano**<br/><br/>(prostřednictvím aktualizace časového limitu viditelnosti na místě) |**Ano**<br/><br/>(poskytované prostřednictvím vyhrazené funkce API) |
| Podpora zpráv poison |**Ano** |**Ano** |
| Aktualizace na místě |**Ano** |**Ano** |
| Protokol transakcí na straně serveru |**Ano** |**Ne** |
| Metriky úložiště |**Ano**<br/><br/>**Minute Metriky:** Poskytuje metriky v reálném čase pro dostupnost, TPS, počet volání rozhraní API, počet chyb a další, to vše v reálném čase (agregované za minutu a hlášeny během několika minut od toho, co se právě stalo v produkčním prostředí. Další informace najdete v tématu [About Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ano**<br/><br/>(hromadné dotazy voláním [GetQueues)](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues) |
| Správa stavu |**Ne** |**Ano**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatické předávání zpráv |**Ne** |**Ano** |
| Vymazat frontu |**Ano** |**Ne** |
| Skupiny zpráv |**Ne** |**Ano**<br/><br/>(pomocí e-mailových relací) |
| Stav aplikace pro skupinu zpráv |**Ne** |**Ano** |
| Vyhledávání duplicit |**Ne** |**Ano**<br/><br/>(konfigurovatelné na straně odesílatele) |
| Procházení skupin zpráv |**Ne** |**Ano** |
| Načítání relací zpráv podle ID |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Obě technologie řazení do fronty umožňují, aby byla zpráva naplánována k doručení později.
* Automatické přeposílání fronty umožňuje tisícům front automaticky předávat zprávy do jedné fronty, ze které přijímající aplikace zprávu spotřebovává. Tento mechanismus můžete použít k dosažení zabezpečení, tok řízení a izolovat úložiště mezi jednotlivými vydavateli zpráv.
* Fronty úložiště poskytují podporu pro aktualizaci obsahu zpráv. Tuto funkci můžete použít pro uchování informací o stavu a přírůstkové aktualizace průběhu do zprávy tak, aby mohly být zpracovány z posledního známého kontrolního bodu, namísto spuštění od začátku. S fronty Service Bus, můžete povolit stejný scénář pomocí relace zpráv. Relace umožňují uložit a načíst stav zpracování aplikace (pomocí [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Nedoručené písmo](service-bus-dead-letter-queues.md), které je podporováno pouze fronty service bus, může být užitečné pro izolaci zpráv, které nemohou být úspěšně zpracovány přijímající aplikací nebo pokud zprávy nemohou dosáhnout svého cíle z důvodu vypršení platnosti time-to-live (TTL) vlastnost. Hodnota TTL určuje, jak dlouho zůstane zpráva ve frontě. S Service Bus zpráva bude přesunuta do zvláštní fronty s názvem $DeadLetterQueue po vypršení období TTL.
* Chcete-li najít "poison" zprávy ve frontách úložiště, při dequeuing zprávu aplikace zkontroluje [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) vlastnost zprávy. Pokud **DequeueCount** je větší než daná prahová hodnota, aplikace přesune zprávu do fronty definované aplikací "nedoručené písmeno".
* Fronty úložiště umožňují získat podrobný protokol všech transakcí provedených proti frontě, stejně jako agregované metriky. Obě tyto možnosti jsou užitečné pro ladění a pochopení, jak vaše aplikace používá fronty úložiště. Jsou také užitečné pro ladění výkonu aplikace a snížení nákladů na používání front.
* Koncept "relace zpráv" podporované Service Bus umožňuje zprávy, které patří do určité logické skupiny, které mají být spojeny s daným příjemcem, což vytváří relace jako spřažení mezi zprávami a jejich příslušné příjemce. Tuto rozšířenou funkci můžete povolit v service bus nastavením [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) vlastnost na zprávu. Příjemci pak mohou naslouchat na konkrétní ID relace a přijímat zprávy, které sdílejí zadaný identifikátor relace.
* Funkce zjišťování duplicit podporovaná frontami služby Service Bus automaticky odebere duplicitní zprávy odeslané do fronty nebo tématu na základě hodnoty [vlastnosti MessageId.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)

## <a name="capacity-and-quotas"></a>Kapacita a kvóty
Tato část porovnává fronty úložiště a fronty service bus z hlediska [kapacity a kvót,](service-bus-quotas.md) které mohou platit.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Maximální velikost fronty |**500 TB**<br/><br/>(omezeno na [kapacitu jednoho účtu úložiště)](../storage/common/storage-introduction.md#queue-storage) |**1 GB až 80 GB**<br/><br/>(definováno při vytvoření fronty a [povolení dělení](service-bus-partitioning.md) – viz část "Další informace") |
| Maximální velikost zprávy |**64 kB**<br/><br/>(48 KB při použití kódování **Base64)**<br/><br/>Azure podporuje velké zprávy kombinací front a objektů BLOB – v tomto okamžiku můžete zařadit do fronty až 200 GB pro jednu položku. |**256 KB** nebo **1 MB**<br/><br/>(včetně záhlaví i textu, maximální velikost záhlaví: 64 KB).<br/><br/>Závisí na [úrovni služby](service-bus-premium-messaging.md). |
| Maximální zpráva TTL |**Nekonečné** (od api-verze 2017-07-27) |**ČasSpan.Max** |
| Maximální počet front |**Unlimited** |**10 000**<br/><br/>(na obor názvů služby) |
| Maximální počet souběžných klientů |**Unlimited** |**Unlimited**<br/><br/>(Limit souběžného připojení 100 se vztahuje pouze na komunikaci založenou na protokolu TCP) |

### <a name="additional-information"></a>Další informace
* Service Bus vynucuje omezení velikosti fronty. Maximální velikost fronty je určena při vytváření fronty a může mít hodnotu mezi 1 a 80 GB. Pokud je dosaženo hodnoty velikosti fronty nastavené při vytváření fronty, budou další příchozí zprávy odmítnuty a volající kód obdrží výjimku. Další informace o kvótách v service bus naleznete v [tématu Kvóty sběrnice](service-bus-quotas.md).
* Dělení není podporováno ve [vrstvě Premium](service-bus-premium-messaging.md). Na úrovni Standard můžete vytvořit fronty service bus u velikostí 1, 2, 3, 4 nebo 5 GB (výchozí hodnota je 1 GB). Ve standardní úrovni s povoleným dělením (což je výchozí hodnota) service bus vytvoří 16 oddílů pro každý zadaný GB. Pokud jako takové vytvoříte frontu, která má velikost 5 GB, s 16 oddíly maximální velikost fronty se stane (5 * 16) = 80 GB. Maximální velikost rozdělené fronty nebo tématu najdete v jeho vstupu na [webu Azure Portal][Azure portal].
* S fronty úložiště, pokud obsah zprávy není xml-safe, pak musí být **Base64** kódován. Pokud **base64**-kódovat zprávu, datová část uživatele může být až 48 KB, namísto 64 KB.
* S fronty Service Bus každá zpráva uložená ve frontě se skládá ze dvou částí: záhlaví a tělo. Celková velikost zprávy nesmí překročit maximální velikost zprávy podporovanou úrovní služby.
* Pokud klienti komunikují s frontami služby Service Bus prostředpou protokol TCP, je maximální počet souběžných připojení k jedné frontě služby Service Bus omezen na 100. Toto číslo je sdíleno mezi odesílateli a příjemci. Pokud je dosaženo této kvóty, následné požadavky na další připojení budou odmítnuty a volající kód obdrží výjimku. Toto omezení není uloženo klientům, kteří se připojují k frontám pomocí rozhraní API založeného na rest.
* Pokud požadujete více než 10 000 front v jednom oboru názvů Service Bus, můžete kontaktovat tým podpory Azure a požádat o zvýšení. Chcete-li škálovat více než 10 000 front pomocí služby Service Bus, můžete také vytvořit další obory názvů pomocí [portálu Azure][Azure portal].

## <a name="management-and-operations"></a>Řízení a provoz
Tato část porovnává funkce správy poskytované fronty úložiště a fronty service bus.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Protokol správy |**REST přes HTTP/HTTPS** |**REST přes HTTPS** |
| Protokol runtime |**REST přes HTTP/HTTPS** |**REST přes HTTPS**<br/><br/>**Standard AMQP 1.0 (TCP s TLS)** |
| .NET API |**Ano**<br/><br/>(ROZHRANÍ API klienta úložiště.NET) |**Ano**<br/><br/>(ROZHRANÍ API sběrnice služby.NET) |
| Nativní C++ |**Ano** |**Ano** |
| Java API |**Ano** |**Ano** |
| PHP API |**Ano** |**Ano** |
| Node.js API |**Ano** |**Ano** |
| Podpora libovolných metadat |**Ano** |**Ne** |
| Pravidla pojmenování fronty |**Až 63 znaků**<br/><br/>(Písmena v názvu fronty musí být malá.) |**Až 260 znaků**<br/><br/>(Cesty fronty a názvy nerozlišují malá a velká písmena.) |
| Funkce získání délky fronty |**Ano**<br/><br/>(Přibližná hodnota, pokud platnost zpráv vyprší nad rámec hodnoty TTL, aniž by byly odstraněny.) |**Ano**<br/><br/>(Přesná hodnota v čase.) |
| Peek funkce |**Ano** |**Ano** |

### <a name="additional-information"></a>Další informace
* Fronty úložiště poskytují podporu pro libovolné atributy, které lze použít pro popis fronty ve formě párů název/hodnota.
* Obě technologie fronty nabízejí možnost nahlédnout zprávu bez nutnosti zamknout, což může být užitečné při implementaci aplikace průzkumník fronty nebo nástroje prohlížeče.
* Service Bus .NET zprostředkované zasílání zpráv rozhraní API využít plně duplexní připojení TCP pro lepší výkon ve srovnání s REST přes HTTP a podporují standardní protokol AMQP 1.0.
* Názvy front úložiště mohou mít dlouhý počet 3 až 63 znaků, mohou obsahovat malá písmena, čísla a pomlčky. Další informace naleznete [v tématu Naming Queues and Metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Názvy front služby Service Bus mohou mít až 260 znaků a mají méně omezující pravidla pojmenování. Názvy front služby Service Bus mohou obsahovat písmena, čísla, tečky, pomlčky a podtržítka.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Tato část popisuje funkce ověřování a autorizace podporované frontami úložiště a frontami služby Service Bus.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Authentication |**Symetrický klíč** |**Symetrický klíč** |
| Model zabezpečení |Delegovaný přístup prostřednictvím tokenů SAS. |Sas |
| Federace zprostředkovatele identity |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Každý požadavek na některou z technologií řazení do fronty musí být ověřen. Veřejné fronty s anonymním přístupem nejsou podporovány. Pomocí [SAS](service-bus-sas.md), můžete řešit tento scénář publikováním SAS jen pro zápis, Jen pro čtení SAS nebo dokonce plně přístupSAS.
* Schéma ověřování poskytované fronty úložiště zahrnuje použití symetrického klíče, což je kód ověřování zpráv založený na algoritmu hash (HMAC), vypočítaný pomocí algoritmu SHA-256 a kódovaný jako řetězec **Base64.** Další informace o příslušném protokolu najdete v [tématu Ověřování pro službu Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Fronty service bus podporují podobný model pomocí symetrických klíčů. Další informace naleznete v [tématu Ověřování podpisů sdíleného přístupu pomocí služby Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Závěr
Získáním hlubšího pochopení těchto dvou technologií budete moci učinit informovanější rozhodnutí o tom, kterou technologii fronty použít a kdy. Rozhodnutí o tom, kdy použít fronty úložiště nebo fronty service bus jasně závisí na řadě faktorů. Tyto faktory mohou do značné míry záviset na individuálních potřebách vaší aplikace a její architektuře. Pokud vaše aplikace již používá základní funkce Microsoft Azure, můžete raději zvolit fronty úložiště, zejména pokud potřebujete základní komunikaci a zasílání zpráv mezi službami nebo potřebujete fronty, které mohou být větší než 80 GB.

Vzhledem k tomu, že fronty služby Service Bus poskytují řadu pokročilých funkcí, jako jsou relace, transakce, vyhledávání duplicit, automatické nedoručené písmo a trvalé možnosti publikování a odběru, mohou být upřednostňovanou volbou, pokud vytváříte hybridní aplikaci nebo pokud vaše aplikace jinak vyžaduje tyto funkce.

## <a name="next-steps"></a>Další kroky
Následující články obsahují další pokyny a informace o používání front úložiště nebo front service bus.

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Použití služby úložiště fronty](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Doporučené postupy pro zlepšení výkonu pomocí zasílání zpráv zprostředkovaných službou Service Bus](service-bus-performance-improvements.md)
* [Představujeme fronty a témata v Azure Service Bus (blogpost)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Průvodce pro vývojáře pro sběrnici Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Používání služby Řízení front v Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

