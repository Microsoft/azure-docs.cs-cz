---
title: Fronty Azure Storage a fronty Service Bus porovnání a rozdíl od aktualizovaného | Microsoft Docs
description: Analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/08/2017
ms.author: sethm
ms.openlocfilehash: b1919037e3a112659a81e9207c842c279734fb48
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Fronty úložiště a fronty Service Bus - porovnání a na rozdíl od aktualizovaného
Tento článek analyzuje rozdíly a podobnosti mezi těmito dvěma typy front, které nabízí Microsoft Azure ještě dnes: fronty úložiště a fronty Service Bus. Tyto informace můžete použít ke srovnání příslušných technologií a pomůžou vám kvalifikovaněji se rozhodnout, které řešení nejlíp vyhovuje vašim potřebám.

## <a name="introduction"></a>Úvod
Azure podporuje dva typy mechanismů fronty: **fronty úložiště** a **fronty Service Bus**.

**Fronty úložiště**, které jsou součástí [úložiště Azure](https://azure.microsoft.com/services/storage/) infrastruktury, funkce jednoduché rozhraní založené na REST GET nebo PUT/funkce Náhled, poskytuje spolehlivé, trvalé zasílání zpráv v rámci a mezi službami.

**Fronty služby Service Bus** jsou součástí širší [zasílání zpráv Azure](https://azure.microsoft.com/services/service-bus/) infrastruktury, který podporuje služby Řízení front a také publikování a přihlášení k odběru a další pokročilé integrace vzory. Další informace o Service Bus fronty nebo témata nebo předplatných najdete v tématu [Přehled služby Service Bus](service-bus-messaging-overview.md).

Přestože obě služby Řízení front technologie existují současně, fronty úložiště zavedených Zaprvé, jako mechanismus vyhrazené fronty úložiště postavená na služby úložiště Azure. Fronty služby Service Bus jsou postavená na širší infrastrukturu zasílání zpráv určené k integraci aplikací nebo součástí aplikace, které může span více komunikační protokoly, kontrakty dat, vztah důvěryhodnosti domén nebo prostředí sítě.

## <a name="technology-selection-considerations"></a>Důležité informace o výběru technologie
Fronty úložiště a fronty Service Bus jsou implementace zprávy služby Řízení front služba aktuálně nabízená Microsoft Azure. Každý má sadu mírně odlišné funkce, což znamená, můžete zvolit jeden z nich nebo obě, podle potřeb vaší konkrétní řešení nebo obchodní nebo technické problému, který se řešení použít.

Při určování, které front technologie vyhovuje účelu daného řešení, zvažte tato doporučení řešení architekty a vývojáře. Další podrobnosti najdete v další části.

Jako řešení architekt nebo vývojáře **měli byste zvážit použití fronty úložiště** při:

* Aplikace musí uložit více než 80 GB zpráv ve frontě.
* Aplikace se chce sledovat průběh zpracování zprávy uvnitř fronty. To je užitečné, pokud dojde k chybě pracovního procesu zpracovává zprávu. Následné pracovní potom můžete pomocí těchto informací pokračovat od kde skončil předchozí pracovního procesu.
* Vyžadujete protokoly straně server všechny transakce prováděné vůči vaší front.

Jako řešení architekt nebo vývojáře **měli byste zvážit použití fronty Service Bus** při:

* Řešení musí být schopný přijímat zprávy bez nutnosti dotazovat fronty. Službou Service Bus toho lze dosáhnout pomocí cyklického dotazování dlouho přijímat operace pomocí protokolů založených na protokolu TCP, které podporuje Service Bus.
* Řešení vyžaduje fronty k poskytování zaručenou první in-first-out (FIFO) seřazené doručení.
* Chcete symetrický prostředí v Azure a v systému Windows Server (privátní cloud). Další informace najdete v tématu [sběrnice služby pro Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).
* Řešení musí být schopné podporovat automatické zjišťování duplicitní.
* Má vaše aplikace zpracování zpráv jako paralelní dlouhodobé datové proudy (zprávy jsou spojeny pomocí datového proudu [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) vlastnost zprávy). V tomto modelu bojuje každý uzel v spotřebitelskou aplikací pro datové proudy a zprávy. Pokud datový proud je uděleno náročné uzel, uzel můžete zkontrolovat stav stav datového proudu aplikace použití transakcí.
* Řešení vyžaduje transakční chování a nedělitelnost při odesílání nebo přijímání více zpráv z fronty.
* Vaše aplikace zpracovává zprávy, které může být vyšší než 64 KB, ale omezí přístup je nepravděpodobné 256 KB.
* Práce s požadavky pro poskytnutí přístupu podle rolí modelu do fronty a různá práva nebo oprávnění pro odesílateli a příjemci.
* Velikost vašeho fronty nebude růst větší než 80 GB.
* Chcete použít zasílání zpráv protokolu založených na standardech protokolu AMQP 1.0. Další informace o protokolu AMQP najdete v tématu [přehled AMQP Service Bus](service-bus-amqp-overview.md).
* Představ případné migrace z komunikaci na základě fronty typu point-to-point do vzorce výměny zpráv, které umožňuje bezproblémovou integraci další příjemci (odběratelé), z nichž každý přijímá nezávislé kopie některých nebo všech zprávy odeslané do fronty. K tomu odkazuje na možnosti publikování a přihlášení k odběru nativně poskytované služby Service Bus.
* Řešení zasílání zpráv musí být schopné podporovat záruky doručení "Na většinu-jedno" bez nutnosti sestavit komponenty další infrastrukturu.
* Chcete mít možnost publikovat a využívat dávky zprávy.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porovnání fronty úložiště a fronty Service Bus
Tabulky v následujících částech poskytují možnost logického seskupování fronty funkcí a umožňují porovnání rychlý přehled možností dostupných v fronty Azure Storage a fronty Service Bus.

## <a name="foundational-capabilities"></a>Základní možnosti
Tato část porovná některé základní funkce služby Řízení front poskytované fronty úložiště a fronty Service Bus.

| Kritérií porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Řazení záruku |**Ne** <br/><br>Další informace najdete v tématu první Poznámka: v části "Další informace".</br> |**Ano - First-In-First-Out (FIFO)**<br/><br>(prostřednictvím zasílání zpráv relací) |
| Záruky doručení |**V aspoň jednou** |**V aspoň jednou**<br/><br/>**Jednou na většinu** |
| Podpora atomické operace |**Ne** |**Ano**<br/><br/> |
| Přijímat chování |**Bez blokování**<br/><br/>(dokončení okamžitě pokud se nenajde žádné nové zprávy) |**Blokování s nebo bez časového limitu**<br/><br/>(nabízí dlouhé dotazování, nebo ["Comet technika"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bez blokování**<br/><br/>(prostřednictvím rozhraní .NET spravované rozhraní API pouze) |
| Rozhraní API nabízené stylu |**Ne** |**Ano**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) a **OnMessage** relací .NET API. |
| Zobrazí režim |**Funkce Náhled & zapůjčení** |**Funkce Náhled & uzamčení**<br/><br/>**Přijímat & Odstranit** |
| Režim výhradní přístup |**Na základě zapůjčení** |**Na základě zámku** |
| Doba trvání zapůjčení/zámku |**30 sekund (výchozí)**<br/><br/>**7 dní (maximum)** (můžete obnovit nebo verzi zapůjčení zprávu pomocí [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) rozhraní API.) |**60 sekund (výchozí)**<br/><br/>Můžete obnovit pomocí zámek zprávy [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) rozhraní API. |
| Zapůjčení/uzamčení přesnost |**Úroveň zprávy**<br/><br/>(každá zpráva může mít hodnotu jinou vypršení časového limitu, která může aktualizovat podle potřeby při zpracování zprávy, pomocí [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) rozhraní API) |**Úroveň fronty**<br/><br/>(každá fronta má zámek přesností použít na všechny jeho zprávy, ale můžete obnovit pomocí zámku [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) rozhraní API.) |
| Zpracovat v dávce přijímat |**Ano**<br/><br/>(explicitně určit počet zpráv při načítání zpráv, maximálně 32 zprávy) |**Ano**<br/><br/>(implicitně povolení vlastnost před načtením nebo explicitně použitím transakcí) |
| Dávkové odeslání |**Ne** |**Ano**<br/><br/>(prostřednictvím transakcí nebo dávkování na straně klienta) |

### <a name="additional-information"></a>Další informace
* Zprávy do front úložiště jsou obvykle first-in-first-out, ale v některých případech může být mimo pořadí; například dobu trvání časového limitu viditelnost zpráva vypršení platnosti (například v důsledku chyb během zpracování klientské aplikace). Když vyprší časový limit viditelnosti, se zpráva zobrazí znovu ve frontě pro jinému pracovnímu procesu pro vyřazení ji z fronty. V tomto bodě nově viditelné zpráva může umístěny ve frontě (vyjmutou znovu) po zprávu, která byla původně zařazených do fronty za ním.
* Vzoru FIFO zaručenou fronty Service Bus vyžaduje použití relací zasílání zpráv. V případě, že aplikace spadne při zpracování zprávy přijaté v **prohlížet & Zamknout** režimu, při příštím příjemce fronta přijímá relaci zasílání zpráv, zahájí se zprávou o selhání po jeho time to live (TTL) období vyprší platnost.
* Fronty úložiště jsou navrženy pro podporu standardní služby Řízení front scénářů, jako například oddělovací součásti aplikace ke zvýšení škálovatelnost a odolnost proti selhání, vyrovnání a pracovní postupy procesů sestavování.
* Podpora fronty služby Service Bus *v aspoň jednou* záruku doručení. Kromě toho *jednou na většinu* sémantického může být podporován pomocí stavu relace pro uložení stavu aplikace a použití transakcí atomicky přijímat zprávy a aktualizovat stav relace.
* Fronty úložiště zadat programovací model jednotné a konzistentní napříč fronty, tabulky a objekty BLOB – pro vývojáře i pro operace týmy.
* Fronty služby Service Bus poskytuje podporu pro místní transakce v rámci jedné frontě.
* **Přijetí a odstranění** režim podporovaný Service Bus poskytuje schopnost snížit počet zasílání zpráv operací (a související náklady) za zajištění snížený doručení.
* Fronty úložiště poskytují možnost rozšířit zapůjčení pro zprávy zapůjčení. To umožňuje pracovníkům udržovat krátké zapůjčení na zprávy. Proto pokud dojde k chybě pracovní, můžete se rychle zpracovat zprávu znovu jinému pracovnímu procesu. Kromě toho pracovní můžete rozšířit zapůjčení na zprávu, pokud je třeba zpracovat delší než aktuální čas zapůjčení.
* Fronty úložiště nabízejí viditelnost vypršení časového limitu, můžete nastavit při enqueuing nebo vyřazení zprávy. Kromě toho můžete aktualizovat zprávu s hodnotami různých zapůjčení za běhu a aktualizovat různé hodnoty mezi zprávy ve frontě stejné. Překročení časového limitu zámku Service Bus jsou definované v metadatech fronty; lze však obnovit zámek voláním [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metoda.
* Maximální časový limit pro blokování přijímat operace v fronty Service Bus je 24 dní. Na základě REST vypršení časových limitů však mít maximální hodnotu 55 sekund.
* Dávkování na straně klienta poskytované služby Service Bus umožňuje klientovi fronty pro dávkové více zpráv do jednoho odeslání operace. Dávkování je k dispozici pouze pro operace asynchronní odesílání.
* Funkce jako je například 200 TB horní meze front úložiště (více při virtualizaci účty) a neomezená fronty vám ideální platformu pro poskytovatele SaaS.
* Zadejte fronty úložiště flexibilní a původce delegovaný mechanismu řízení přístupu.

## <a name="advanced-capabilities"></a>Rozšířené možnosti
Tato část porovná pokročilých funkcí poskytovaných fronty úložiště a fronty Service Bus.

| Kritérií porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Doručení naplánované |**Ano** |**Ano** |
| Automatické mrtvou lettering |**Ne** |**Ano** |
| Zvýšení hodnoty time to live fronty |**Ano**<br/><br/>(prostřednictvím místní aktualizace limit viditelnosti) |**Ano**<br/><br/>(poskytované prostřednictvím vyhrazené funkce rozhraní API) |
| Zacházení s nezpracovatelnými podpora zpráv |**Ano** |**Ano** |
| Aktualizace na místě |**Ano** |**Ano** |
| Protokol transakce na straně serveru |**Ano** |**Ne** |
| Metriky úložiště |**Ano**<br/><br/>**Minutu metriky**: poskytuje metriky v reálném čase pro dostupnost, TPS, rozhraní API volat počty, počty chyb a další v reálném čase (agregovat za minutu a jsou uvedeny během několika minut od co se právě stalo v produkčním prostředí. Další informace najdete v tématu [o Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ano**<br/><br/>(hromadné dotazy voláním [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Správa stavu |**Ne** |**Ano**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatické předávání zpráv |**Ne** |**Ano** |
| Vyprázdnění fronty – funkce |**Ano** |**Ne** |
| Zpráva skupiny |**Ne** |**Ano**<br/><br/>(prostřednictvím zasílání zpráv relací) |
| Stav aplikace na zprávy skupinu |**Ne** |**Ano** |
| Detekce duplicitních |**Ne** |**Ano**<br/><br/>(konfigurovat na straně odesílatele) |
| Procházení skupin zpráv |**Ne** |**Ano** |
| Načítání zpráv relací podle ID |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Obě služby Řízení front technologie povolit zprávu, která se naplánován pro odeslání na později.
* Automatické přeposílání fronty umožňuje tisíce fronty a jejich zprávy do jedné frontě, ze kterého má přijímající aplikace využívá zpráva automaticky předat dál. Můžete použít tento mechanismus k dosažení zabezpečení, řízení toku a izolovat úložiště mezi každou zprávu vydavatele.
* Fronty úložiště poskytuje podporu pro aktualizaci obsahu zprávy. Tuto funkci můžete použít pro zachování informací o stavu a průběhu přírůstkové aktualizace ve zprávě tak, že může být zpracována z poslední známé kontrolního bodu, místo od začátku. Pomocí fronty Service Bus můžete povolit stejné scénář prostřednictvím relace zprávy. Relace umožňují uložení a načtení stavu aplikace zpracování (s použitím [setstate –](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Mrtvých písmem](service-bus-dead-letter-queues.md), která je jen nepodporuje fronty Service Bus, může být užitečná pro izolace zprávy, které nelze úspěšně zpracovat přijímající aplikace nebo když zprávy nelze cílového umístění v důsledku vypršela platnost (time-to-live Vlastnost TTL). Hodnota TTL určuje, jak dlouho zůstane zprávu ve frontě. Službou Service Bus zpráva bude přesunuta do speciální fronty s názvem $DeadLetterQueue, když vyprší doba TTL.
* Najít "poškozených" zpráv do front úložiště při vyřazení zprávu aplikace prověří [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) vlastnost zprávy. Pokud **DequeueCount** je větší než danou prahovou hodnotu, aplikace přesune zprávu pro frontu definované aplikací "nedoručených zpráv".
* Fronty úložiště umožňují získat podrobný protokol všechny transakce prováděné vůči fronty jako i agregovaných metrik. Obě tyto možnosti jsou užitečné pro ladění a pochopení, jak vaše aplikace používá fronty úložiště. Také se hodí pro vaše aplikace optimalizace výkonu a snížení nákladů na použití front.
* Koncept "zpráva relace" Service Bus podporuje umožňuje zprávy, které patří do určité logické skupiny přiřazené dané příjemce, který pak vytvoří relace jako spřažení mezi zprávy a jejich odpovídajících příjemci. Můžete ho povolit rozšířené funkce v Service Bus nastavením [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) vlastnost ve zprávě. Příjemci můžete naslouchat na ID konkrétní relace a přijímat zprávy, které sdílejí identifikátor zadaná relace.
* Funkce zjišťování duplicitních nepodporuje fronty Service Bus automaticky odebere duplicitní zprávy odeslané do fronty nebo téma, na základě hodnoty z [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) vlastnost.

## <a name="capacity-and-quotas"></a>Kapacity a kvót
Tato část porovná fronty úložiště a fronty Service Bus z perspektivy [kapacity a kvót](service-bus-quotas.md) , uplatnit.

| Kritérií porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Maximální velikost fronty |**500 TB**<br/><br/>(omezený na [jednotné kapacitě účtu úložiště](../storage/common/storage-introduction.md#queue-storage)) |**1 GB až 80 GB**<br/><br/>(definován při vytvoření fronty a [povolení dělení](service-bus-partitioning.md) – najdete v části "Další informace") |
| Maximální velikost zprávy |**64 KB**<br/><br/>(48 KB při použití **Base64** kódování)<br/><br/>Azure podporuje velké zprávy fronty a objekty BLOB – v tomto okamžiku je možné zařadit kombinují až 200 GB pro jednu položku. |**256 KB** nebo **1 MB**<br/><br/>(včetně záhlaví a text, velikost maximální záhlaví: 64 KB).<br/><br/>Závisí na [vrstvy služby](service-bus-premium-messaging.md). |
| Maximální hodnota TTL zprávy |**Nekonečné** (od verze api-version 2017-07-27) |**TimeSpan.Max** |
| Maximální počet front |**Unlimited** |**10,000**<br/><br/>(podle oboru názvů služby) |
| Maximální počet souběžných klientů |**Unlimited** |**Unlimited**<br/><br/>(100 limitu souběžných připojení se vztahuje pouze na komunikace na základě protokolu TCP) |

### <a name="additional-information"></a>Další informace
* Service Bus vynucuje omezení velikosti fronty. Maximální velikost fronty je zadána při vytvoření fronty a může mít hodnotu mezi 1 a 80 GB. Pokud je dosaženo hodnota velikosti fronty nastavit při vytváření fronty, další příchozí zprávy budou odmítnuty a výjimku dostane volající kód. Další informace o kvótách v Service Bus, najdete v části [Service Bus kvóty](service-bus-quotas.md).
* V [úrovně Standard](service-bus-premium-messaging.md), vytvořením front Service Bus na 1, 2, 3, 4 nebo 5 GB velikosti (výchozí hodnota je 1 GB). V úrovni Premium, můžete vytvořit fronty až do velikosti 80 GB. Ve verzi Standard úroveň, s dělení povolené (což je výchozí nastavení), Service Bus vytvoří 16 oddíly pro každý GB je zadat. Jako takový, když vytvoříte frontu, který je 5 GB velikost, s 16 oddíly maximální velikost fronty stane (5 * 16) = 80 GB. Zobrazí maximální velikost fronty oddílů nebo téma prohlížením jeho položku [portál Azure][Azure portal]. V úrovni Premium jsou za fronty vytvořit pouze 2 oddíly.
* V případě front úložiště, pokud není obsah zprávy XML bezpečné, pak musí být **Base64** kódování. Pokud jste **Base64**-kódování zprávy, uživatel může být až 48 KB místo 64 KB.
* Pomocí front Service Bus, každá zpráva uložený ve frontě se skládá ze dvou částí: hlavičku a text. Celková velikost zprávy nesmí překročit maximální velikost zprávy nepodporuje danou vrstvu služeb.
* Pokud klienti komunikují pomocí front Service Bus přes protokol TCP, maximální počet současných připojení ke jedné frontě Service Bus je omezeno na 100. Toto číslo je sdílena mezi odesílateli a příjemci. Pokud je dosaženo této kvóty, odeslání dalších žádostí o další připojení se odmítne a dostane výjimku volající kód. Toto omezení není vynucená pro připojení klientů k fronty pomocí rozhraní API založené na REST.
* Pokud potřebujete více než 10 000 front v jeden obor názvů Service Bus, můžete obraťte se na tým podpory Azure a požádejte o zvýšení limitu. Škálování 10 000 front se Service Bus, můžete také vytvořit další obory názvů pomocí [portál Azure][Azure portal].

## <a name="management-and-operations"></a>Operace a Správa
Tato část porovnává funkce správy poskytuje úložiště fronty a fronty Service Bus.

| Kritérií porovnání | Fronty úložiště | Fronty Service Bus |
| --- | --- | --- |
| Protokol pro správu |**REST přes protokol HTTP nebo HTTPS** |**REST přes protokol HTTPS** |
| Protokol modulu runtime |**REST přes protokol HTTP nebo HTTPS** |**REST přes protokol HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP with TLS)** |
| .NET API |**Ano**<br/><br/>(Klient úložiště .NET API) |**Ano**<br/><br/>(.NET sběrnice rozhraní API) |
| Nativní C++ |**Ano** |**Ano** |
| Java API |**Ano** |**Ano** |
| ROZHRANÍ API PHP |**Ano** |**Ano** |
| Rozhraní API Node.js |**Ano** |**Ano** |
| Podpora libovolný metadat |**Ano** |**Ne** |
| Pravidla pojmenování fronty |**Až 63 znaků.**<br/><br/>(Písmena v název fronty musí být malá písmena.) |**Až 260 znaků.**<br/><br/>(Fronty cesty a názvy jsou velká a malá písmena.) |
| Get – funkce délka fronty |**Ano**<br/><br/>(Přibližnou hodnotu, pokud zprávy vyprší mimo interval TTL, ZÍSKÁ bez odstraňuje.) |**Ano**<br/><br/>(Přesný, v okamžiku hodnota). |
| Prohlížení – funkce |**Ano** |**Ano** |

### <a name="additional-information"></a>Další informace
* Fronty úložiště poskytuje podporu pro libovolné atributy, které mohou být použity k popisu fronty, ve formě dvojic název hodnota.
* Obě technologie fronty nabízejí možnost prohlížet zprávy bez nutnosti uzamknout, může být užitečné při implementaci nástroj Prohlížeč/explorer fronty.
* Service Bus .NET pro zprostředkované zasílání zpráv rozhraní API využívají plně duplexní připojení TCP pro zlepšení výkonu při porovnání s ostatními prostřednictvím protokolu HTTP, a podporují standardní protokol AMQP 1.0.
* Názvy front úložiště může být 3 až 63 znaků dlouhý, může obsahovat malá písmena, číslice a pomlčky. Další informace najdete v tématu [pojmenování front a Metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Názvy fronty Service Bus může být až 260 znaků a musí mít méně přísná pravidla pojmenování. Názvy fronty Service Bus může obsahovat písmena, číslice, tečky, pomlčky a podtržítka.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Tato část pojednává o ověřování a autorizace funkcí podporovaných fronty úložiště a fronty Service Bus.

| Kritérií porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Authentication |**Symetrický klíč** |**Symetrický klíč** |
| Model zabezpečení |Delegovaný přístup prostřednictvím tokeny SAS. |SAS |
| Zprostředkovatel federaci identit |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Každý požadavek na jednu z front technologie musí být ověřeny. Anonymní přístup veřejné fronty nejsou podporovány. Pomocí [SAS](service-bus-sas.md), tento scénář lze vyřešit tak, že publikování pouze pro zápis SAS, SAS jen pro čtení nebo i SAS úplnému přístupu.
* Schéma ověřování zadaný úložiště fronty zahrnuje použití symetrický klíč, který je na základě hodnoty hash ověřování kódu metoda HMAC (Message), počítaný pomocí algoritmu SHA-256 a kódovaná jako **Base64** řetězec. Další informace o příslušných protokolu najdete v tématu [ověřování pro služby Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Fronty služby Service Bus podporují podobné model pomocí symetrických klíčů. Další informace najdete v tématu [ověřování sdíleného přístupového podpisu službou Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Závěr
Podle získat lepší představu o dvě technologie, bude mít možnost provádět více informované rozhodnutí o technologii fronty, kterou chcete použít a kdy. Rozhodnutí o použití úložiště fronty nebo fronty Service Bus jasně závisí na počtu faktorů. Tyto faktory mohou výraznou závisí na konkrétním potřebám vaší aplikace a jeho architektura. Pokud už vaše aplikace používá základní funkce Microsoft Azure, dáte možná přednost zvolit front úložiště, zvlášť pokud vyžadují základní komunikace a zasílání zpráv mezi služby nebo nutnost fronty, které může být větší než 80 GB velikost.

Protože fronty Service Bus poskytují počet pokročilé funkce, jako je například relace, transakce, duplicitní detekce, automatické zpráv lettering a odolná funkce pbulikovat/odebírat, mohou být upřednostňovanou volbou Pokud vytváříte hybridním aplikace nebo pokud vaše aplikace, jinak hodnota vyžaduje tyto funkce.

## <a name="next-steps"></a>Další postup
Následující články poskytují další pokyny a informace o použití úložiště fronty nebo fronty Service Bus.

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat fronty služby úložiště](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Osvědčené postupy pro zlepšení výkonu pomocí služby Service Bus zprostředkované zasílání zpráv](service-bus-performance-improvements.md)
* [Představení front a témat v Azure Service Bus (příspěvek na blogu)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Příručka pro vývojáře k Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Pomocí služby Řízení front v Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

