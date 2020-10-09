---
title: Porovnání front služeb Azure Storage a Service Bus
description: Analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a64000741de68518dd459b105a093ccf4cb6ab7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85337637"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Fronty úložiště a fronty Service Bus – porovnání a kontrast
Tento článek analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Microsoft Azure dnes: fronty úložiště a Service Bus fronty. Pomocí těchto informací můžete porovnat a obcházet příslušné technologie a dát jim k moc informací o tom, které řešení nejlépe vyhovuje vašim potřebám.

## <a name="introduction"></a>Úvod
Azure podporuje dva typy mechanismů fronty: **fronty úložiště** a **fronty Service Bus**.

**Fronty úložiště**, které jsou součástí infrastruktury [úložiště Azure](https://azure.microsoft.com/services/storage/) , poskytují jednoduché rozhraní Get/PUT a prohlížení založené na REST, které zajišťuje spolehlivé a trvalé zasílání zpráv v rámci služeb i mezi nimi.

**Fronty Service Bus** jsou součástí širší infrastruktury [zasílání zpráv Azure](https://azure.microsoft.com/services/service-bus/) , která podporuje zařazování do fronty a také pro publikování/odběr a pokročilejší způsoby integrace. Další informace o Service Bus frontách/tématech/předplatných najdete v tématu [přehled Service Bus](service-bus-messaging-overview.md).

I když obě technologie pro řazení současně existují, zavedly se nejprve fronty úložiště, jako vyhrazený mechanismus úložiště fronty založený na Azure Storagech službách. Fronty Service Bus jsou postaveny na širší infrastruktuře zasílání zpráv, která je určená k integraci aplikací nebo součástí aplikací, které můžou zahrnovat víc komunikačních protokolů, kontraktů dat, důvěřujících domén nebo síťových prostředí.

## <a name="technology-selection-considerations"></a>Požadavky na výběr technologie
Fronty úložiště i fronty Service Bus jsou implementace služby Řízení front zpráv, kterou momentálně nabízí Microsoft Azure. Každá má mírně odlišnou sadu funkcí, což znamená, že můžete zvolit jednu nebo druhou, nebo použít obojí, a to v závislosti na potřebách konkrétního řešení nebo technického problému.

Při určování, které technologie řízení front vyhovují účelu pro dané řešení, by si měli doporučení architekti a vývojáři řešení vzít v úvahu. Další podrobnosti najdete v další části.

Jako architekt nebo vývojář řešení **byste měli zvážit použití front úložiště** v těchto případech:

* Vaše aplikace musí ukládat více než 80 GB zpráv ve frontě.
* Vaše aplikace chce sledovat průběh zpracování zprávy uvnitř fronty. To je užitečné v případě, že pracovní proces zpracovává selhání zprávy. Další pracovní proces pak může tyto informace použít k pokračování z místa, kde předchozí pracovní proces skončil.
* Vyžadujete, aby byly v protokolech na straně serveru všechny transakce spouštěny na vašich frontách.

Jako architekt nebo vývojář řešení **byste měli zvážit použití Service Busch front** v těchto případech:

* Vaše řešení musí být schopné přijímat zprávy bez nutnosti dotazování fronty. V Service Bus se dá dosáhnout prostřednictvím operace přijímání dlouhého cyklického dotazování pomocí protokolů založených na protokolu TCP, které Service Bus podporuje.
* Vaše řešení vyžaduje, aby fronta poskytovala zaručené doručování FIFO (First-in-first-out).
* Vaše řešení musí být schopné podporovat automatickou detekci duplicit.
* Chcete, aby aplikace zpracovala zprávy jako paralelní dlouhodobé proudy (zprávy jsou přidruženy ke streamu pomocí vlastnosti [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) ve zprávě). V tomto modelu každý uzel v náročné aplikaci soutěží na datové proudy, nikoli na zprávy. Když je datový proud přidělen k náročnému uzlu, uzel může prošetřit stav stavu datového proudu aplikace pomocí transakcí.
* Vaše řešení vyžaduje transakční chování a nedělitelnost při odesílání nebo přijímání více zpráv z fronty.
* Vaše aplikace zpracovává zprávy, které mohou překročit 64 KB, ale nebude se nejspíš přiblíží limitu 256 KB.
* Vyřešíte požadavek na poskytování modelu přístupu na základě role do front a různá práva a oprávnění pro odesílatele a příjemce. Další informace najdete v následujících článcích:
    - [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
    - [Ověření z aplikace](authenticate-application.md)
* Velikost fronty nepřekročí 80 GB.
* Chcete použít protokol zasílání zpráv založený na standardu AMQP 1,0. Další informace o AMQP najdete v tématu [Přehled služby Service Bus AMQP](service-bus-amqp-overview.md).
* V konečném důsledku migrace z typu Point-to-Point na výměnu zpráv, který umožňuje bezproblémovou integraci dalších přijímačů (předplatitelů), každý z nich obdrží nezávislé kopie některých nebo všech zpráv odeslaných do fronty. Druhá z nich odkazuje na funkce publikování/odběru nativně poskytované Service Bus.
* Vaše řešení pro zasílání zpráv musí být schopné podporovat doručení "na nejvyšší úrovni", aniž byste museli sestavovat další součásti infrastruktury.
* Chcete mít možnost publikovat a používat dávky zpráv.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porovnání front úložiště a Service Bus front
Tabulky v následujících částech poskytují logické seskupení funkcí fronty a umožňují vám na první pohled porovnávat možnosti dostupné v Azure Storage frontách a Service Busch frontách.

## <a name="foundational-capabilities"></a>Základní možnosti
V této části jsou porovnávány některé základní možnosti služby Řízení front zpráv poskytované frontami úložiště a Service Bus frontami.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Záruka řazení |**Ne** <br/><br>Další informace naleznete v první poznámce v části "Další informace".</br> |**Yes – first-in-first-out (FIFO)**<br/><br>(pomocí relací zasílání zpráv) |
| Záruka na doručení |**Nejméně jednou** |Nejméně **jednou** (pomocí režimu příjmu PeekLock – Toto je výchozí nastavení). <br/><br/>**Ve** více než jednou (pomocí režimu Receive ReceiveAndDelete) <br/> <br/> Další informace o různých [režimech příjmu](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Podpora atomických operací |**Ne** |**Ano**<br/><br/> |
| Chování při příjmu |**Bez blokování**<br/><br/>(hned se dokončí, pokud se nenalezne žádná nová zpráva.) |**Blokování s časovým limitem/bez**<br/><br/>(nabízí dlouhodobé cyklické dotazování nebo ["Comet techniku"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bez blokování**<br/><br/>(jenom prostřednictvím rozhraní API spravovaného rozhraním .NET) |
| Rozhraní API pro vložení stylu |**Ne** |**Ano**<br/><br/>[QueueClient.-Message](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) a [MessageSessionHandler.-Message](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) Sessions rozhraní .NET API. |
| Režim příjmu |**Náhled & zapůjčení** |**Náhled & zámek**<br/><br/>**Přijmout & odstranění** |
| Režim výhradního přístupu |**Na základě zapůjčení** |**Na základě zámku** |
| Doba trvání zapůjčení/zámku |**30 sekund (výchozí)**<br/><br/>**7 dní (maximum)** (můžete obnovit nebo vydat zapůjčení zprávy pomocí rozhraní [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API.) |**60 sekund (výchozí)**<br/><br/>Zámek zprávy můžete obnovit pomocí rozhraní [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| Přesnost zapůjčení nebo zámku |**Úroveň zprávy**<br/><br/>(každá zpráva může mít jinou hodnotu časového limitu, kterou pak můžete podle potřeby aktualizovat při zpracování zprávy pomocí rozhraní [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API). |**Úroveň fronty**<br/><br/>(u všech zpráv je použita přesnost zámku, ale můžete obnovit zámek pomocí rozhraní [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Dávková příjem |**Ano**<br/><br/>(explicitní určení počtu zpráv při načítání zpráv, až do maximálního počtu zpráv 32) |**Ano**<br/><br/>(implicitně povolení předběžného načtení vlastnosti nebo explicitní prostřednictvím použití transakcí) |
| Dávkové odeslání |**Ne** |**Ano**<br/><br/>(prostřednictvím použití transakcí nebo dávkování na straně klienta) |

### <a name="additional-information"></a>Další informace
* Zprávy ve frontách úložiště jsou obvykle první, ale můžou být mimo pořadí. například když vyprší doba trvání časového limitu viditelnosti zprávy (například v důsledku selhání klientské aplikace během zpracování). Po vypršení časového limitu viditelnosti se zpráva znovu zobrazí ve frontě, aby ji jiný pracovní proces vyřadí do fronty. V tomto okamžiku může být nově zobrazená zpráva umístěna do fronty (aby se znovu zařadila do fronty) po zprávě, která byla po jejím původním zařazení do fronty.
* Model garantované služby FIFO v Service Bus fronty vyžaduje použití relací zasílání zpráv. V případě, že dojde k chybě aplikace při zpracování zprávy přijaté v režimu **náhledu & zámku** , bude při příštím přijímači fronty zasílání zpráv po vypršení časového limitu TTL (Time to Live) začínat chybovou zprávou.
* Fronty úložiště jsou navržené tak, aby podporovaly standardní scénáře řazení do fronty, jako je například odkládání součástí aplikace za účelem zvýšení škálovatelnosti a tolerance selhání, Vyrovnávání zatížení a vytváření pracovních postupů procesů.
* Nekonzistence s ohledem na zpracování zpráv v kontextu relací Service Bus lze zabránit pomocí stavu relace k uložení stavu aplikace relativně ke způsobu zpracování sekvence zpráv relace a pomocí transakcí týkajících se nedostatku přijatých zpráv a aktualizace stavu relace. Tento druh funkce konzistence je někdy označen *právě jednou při zpracování* v produktech jiných dodavatelů, ale chyby transakcí způsobí, že se zprávy znovu dostanou, a proto není přesně dostačující.
* Fronty úložiště poskytují jednotný a konzistentní programovací model napříč frontami, tabulkami a objekty blob – jak pro vývojáře, tak pro provozní týmy.
* Fronty Service Bus poskytují podporu pro místní transakce v kontextu jedné fronty.
* Režim **přijímání a odstraňování** podporovaný nástrojem Service Bus poskytuje možnost snížit počet operací zasílání zpráv (a související náklady) v systému Exchange pro zajištění nižšího objemu doručování.
* Fronty úložiště poskytují zapůjčení možností rozšiřování zapůjčení pro zprávy. Díky tomu mohou pracovníci udržovat krátká zapůjčení zpráv. V případě selhání pracovního procesu proto může být zpráva rychle zpracována jiným pracovním procesem. Kromě toho může pracovník prodloužit zapůjčení zprávy, pokud potřebuje zpracovat delší dobu, než je aktuální zapůjčení.
* Fronty úložiště nabízejí časový limit viditelnosti, který můžete nastavit na enqueuing nebo v zařazování zpráv do fronty. Kromě toho můžete v době běhu aktualizovat zprávu s různými hodnotami zapůjčení a aktualizovat různé hodnoty napříč zprávami ve stejné frontě. V metadatech fronty jsou definovány Service Bus vypršení časových limitů zámků. Můžete však obnovit zámek voláním metody [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* Maximální časový limit pro blokování operace přijímání v Service Bus frontách je 24 dní. Vypršení časového limitu založeného na REST ale mají maximální hodnotu 55 sekund.
* Dávkování na straně klienta, které poskytuje Service Bus, umožňuje klientovi fronty dávkovat více zpráv do jediné operace odeslání. Dávkování je k dispozici pouze pro operace asynchronního odeslání.
* Funkce, jako je 200 TB pro fronty úložiště (více při virtualizaci účtů) a neomezené fronty, usnadňují pro poskytovatele SaaS ideální platformu.
* Fronty úložiště poskytují flexibilní a výkonné mechanismy řízení přístupu.

## <a name="advanced-capabilities"></a>Pokročilé možnosti
Tato část porovnává rozšířené možnosti poskytované frontami úložiště a Service Bus frontami.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Naplánované doručení |**Ano** |**Ano** |
| Automatické nedoručené dopisy |**Ne** |**Ano** |
| Zvýšení hodnoty Time-to-Live pro frontu |**Ano**<br/><br/>(prostřednictvím místní aktualizace časového limitu viditelnosti) |**Ano**<br/><br/>(k dispozici prostřednictvím vyhrazené funkce rozhraní API) |
| Podpora nepoškozených zpráv |**Ano** |**Ano** |
| Místní aktualizace |**Ano** |**Ano** |
| Protokol transakcí na straně serveru |**Ano** |**Ne** |
| Metriky úložiště |**Ano**<br/><br/>**Minutová metrika**: poskytuje metriky v reálném čase pro dostupnost, TPS, počty volání rozhraní API, počty chyb a další, a to vše v reálném čase (agregované za minutu a nahlášené během několika minut od toho, co se právě stalo v produkčním prostředí). Další informace najdete v tématu [o metrikách analýza úložiště](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ano**<br/><br/>(hromadné dotazy voláním [Getqueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Řízení stavu |**Ne** |**Ano**<br/><br/>[Microsoft. ServiceBus. Messaging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatické předávání zpráv |**Ne** |**Ano** |
| Vyprázdnit funkci Queue |**Ano** |**Ne** |
| Skupiny zpráv |**Ne** |**Ano**<br/><br/>(pomocí relací zasílání zpráv) |
| Stav aplikace na jednu skupinu zpráv |**Ne** |**Ano** |
| Vyhledávání duplicit |**Ne** |**Ano**<br/><br/>(konfigurovatelné na straně odesilatele) |
| Procházení skupin zpráv |**Ne** |**Ano** |
| Načítají se relace zpráv podle ID. |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Obě technologie pro řízení front umožňují, aby byla zpráva naplánována na doručení později.
* Automatické předávání do fronty umožňuje, aby tisíce front automaticky předávaly zprávy do jediné fronty, ze které přijímající aplikace tuto zprávu spotřebovává. Tento mechanismus můžete použít k zajištění zabezpečení, řízení toku a izolace úložiště mezi jednotlivými vydavateli zpráv.
* Fronty úložiště poskytují podporu pro aktualizaci obsahu zpráv. Tuto funkci můžete použít pro trvalé informace o stavu a přírůstkové aktualizace průběhu do zprávy tak, aby se mohly zpracovat z posledního známého kontrolního bodu, a ne od začátku. U Service Bus front můžete pomocí relací zpráv povolit stejný scénář. Relace umožňují uložit a načíst stav zpracování aplikace (pomocí [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Nedoručené e-](service-bus-dead-letter-queues.md)maily, které jsou podporovány pouze pomocí Service Busch front, mohou být užitečné pro izolaci zpráv, které nelze úspěšně zpracovat přijímající aplikací nebo když zprávy nemůžou dorazit do svého cíle z důvodu vypršení platnosti vlastnosti TTL (Time to Live). Hodnota TTL určuje, jak dlouho zpráva zůstane ve frontě. Při Service Bus bude zpráva přesunuta do speciální fronty nazvané $DeadLetterQueue po vypršení časového limitu TTL.
* Chcete-li najít "poškozené" zprávy ve frontách úložiště při vyřazení zprávy, aplikace ověří vlastnost [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) zprávy. Pokud je **DequeueCount** větší než daná prahová hodnota, aplikace přesune zprávu do fronty nedoručených zpráv definované aplikací.
* Fronty úložiště vám umožňují získat podrobný protokol všech transakcí provedených proti frontě a agregované metriky. Obě tyto možnosti jsou užitečné pro ladění a porozumění způsobu, jakým vaše aplikace používá fronty úložiště. Jsou také užitečné pro vyladění výkonu aplikace a snížení nákladů na používání front.
* Koncept "relací zpráv" podporovaných nástrojem Service Bus umožňuje, aby zprávy patřící do určité logické skupiny byly přidruženy k danému příjemci, který zase vytváří spřažení mezi zprávami a jejich odpovídajícími příjemci. Tuto rozšířenou funkci můžete v Service Bus povolit nastavením vlastnosti [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) u zprávy. Přijímačé pak mohou naslouchat konkrétnímu ID relace a přijímat zprávy, které sdílejí zadaný identifikátor relace.
* Funkce detekce duplikace, kterou podporuje Service Bus fronty, automaticky odstraní duplicitní zprávy odeslané do fronty nebo tématu, a to na základě hodnoty vlastnosti [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Kapacita a kvóty
V této části se porovnávají fronty úložiště a Service Bus fronty z perspektivy [kapacity a kvót](service-bus-quotas.md) , které mohou platit.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Maximální velikost fronty |**500 TB**<br/><br/>(omezeno na [jednu kapacitu účtu úložiště](../storage/common/storage-introduction.md#queue-storage)) |**1 GB až 80 GB**<br/><br/>(definováno při vytváření fronty a [Povolení dělení](service-bus-partitioning.md) – viz část "Další informace") |
| Maximální velikost zprávy |**64 kB**<br/><br/>(48 KB při použití kódování **Base64** )<br/><br/>Azure podporuje velké zprávy kombinováním front a objektů BLOB – v takovém bodě můžete každou položku zařadit do fronty až 200 GB. |**256 KB** nebo **1 MB**<br/><br/>(včetně záhlaví a textu, maximální velikost hlavičky: 64 KB).<br/><br/>Závisí na [úrovni služby](service-bus-premium-messaging.md). |
| Maximální hodnota TTL zprávy |**Infinite** (od verze API-Version 2017-07-27) |**TimeSpan. max** |
| Maximální počet front |**Unlimited** |**10 000**<br/><br/>(obor názvů pro službu) |
| Maximální počet souběžných klientů |**Unlimited** |**Unlimited**<br/><br/>(100 souběžného počtu připojení se vztahuje jenom na komunikaci založenou na protokolu TCP). |

### <a name="additional-information"></a>Další informace
* Service Bus vynutila omezení velikosti fronty. Maximální velikost fronty je určena při vytváření fronty a může mít hodnotu mezi 1 a 80 GB. Pokud je dosaženo hodnoty velikosti fronty nastavené při vytváření fronty, budou odmítnuty další příchozí zprávy a volající kód bude přijímat výjimku. Další informace o kvótách v Service Bus najdete v tématu [kvóty Service Bus](service-bus-quotas.md).
* Vytváření oddílů není na [úrovni Premium](service-bus-premium-messaging.md)podporováno. Na úrovni Standard můžete vytvořit fronty Service Bus ve velikosti 1, 2, 3, 4 nebo 5 GB (výchozí hodnota je 1 GB). Na úrovni Standard s povoleným rozdělením na oddíly (což je výchozí nastavení) Service Bus vytvoří 16 oddílů pro každou zadanou GB. Pokud tedy vytvoříte frontu o velikosti 5 GB s 16 oddíly, maximální velikost fronty se změní na (5 × 16) = 80 GB. Maximální velikost vaší dělené fronty nebo tématu můžete zobrazit tak, že prohlížíte jeho položku v [Azure Portal][Azure portal].
* Pokud se ve frontách úložiště nejedná o zabezpečený obsah zprávy, musí být kódovaný v **kódování Base64** . Pokud zprávu zakódujete ve **formátu base64**, může být datová část uživatele až 48 kb namísto 64 KB.
* U Service Busch front se každá zpráva uložená ve frontě skládá ze dvou částí: záhlaví a tělo. Celková velikost zprávy nemůže překročit maximální velikost zprávy podporované vrstvou služby.
* Pokud klienti komunikují s Service Bus frontami přes protokol TCP, maximální počet souběžných připojení k jedné frontě Service Bus je omezený na 100. Toto číslo je sdíleno mezi odesílateli a přijímači. Pokud je dosažena tato kvóta, budou další požadavky na další připojení odmítnuty a volající kód obdrží výjimku. Toto omezení není uloženo u klientů připojujících se k frontám pomocí rozhraní API založeného na REST.
* Pokud potřebujete více než 10 000 front v jednom oboru názvů Service Bus, můžete kontaktovat tým podpory Azure a požádat o zvýšení. Pokud chcete škálovat více než 10 000 front pomocí Service Bus, můžete také vytvořit další obory názvů pomocí [Azure Portal][Azure portal].

## <a name="management-and-operations"></a>Správa a operace
V této části se porovnávají funkce správy poskytované frontami úložiště a Service Bus frontami.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Protokol pro správu |**REST přes HTTP/HTTPS** |**REST přes HTTPS** |
| Protokol za běhu |**REST přes HTTP/HTTPS** |**REST přes HTTPS**<br/><br/>**AMQP 1,0 Standard (TCP s TLS)** |
| .NET API |**Ano**<br/><br/>(Klientské rozhraní API pro úložiště .NET) |**Ano**<br/><br/>(Rozhraní .NET Service Bus API) |
| Nativní C++ |**Ano** |**Ano** |
| Java API |**Ano** |**Ano** |
| ROZHRANÍ PHP API |**Ano** |**Ano** |
| Node.js API |**Ano** |**Ano** |
| Libovolná Podpora metadat |**Ano** |**Ne** |
| Queue pravidla pojmenování |**Až 63 znaků dlouhé**<br/><br/>(Písmena v názvu fronty musí být malá.) |**Až 260 znaků dlouhé**<br/><br/>(U cest a názvů fronty se nerozlišují malá a velká písmena.) |
| Funkce Get Queue Length |**Ano**<br/><br/>(Přibližná hodnota, pokud vyprší platnost zpráv nad rámec hodnoty TTL bez odstranění.) |**Ano**<br/><br/>(Přesně, hodnota bodu v čase.) |
| Funkce prohlížet |**Ano** |**Ano** |

### <a name="additional-information"></a>Další informace
* Fronty úložiště poskytují podporu pro libovolné atributy, které se dají použít pro popis fronty, a to ve formě párů název/hodnota.
* Obě technologie front nabízejí možnost prohlížet zprávu, aniž by ji museli zamknout, což může být užitečné při implementaci Průzkumníka/nástroje fronty.
* Rozhraní API pro zprostředkované zasílání zpráv Service Bus .NET využívají plně duplexní připojení TCP pro lepší výkon v porovnání s REST přes protokol HTTP a podporují standardní protokol AMQP 1,0.
* Názvy front úložiště můžou být 3-63 znaků dlouhé, můžou obsahovat malá písmena, číslice a spojovníky. Další informace najdete v tématu [pojmenování front a metadat](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Názvy front Service Bus mohou mít délku až 260 znaků a mají méně omezující pravidla pojmenování. Názvy front Service Bus můžou obsahovat písmena, číslice, tečky, spojovníky a podtržítka.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Tato část popisuje funkce pro ověřování a autorizaci podporované frontami úložiště a frontami Service Bus.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Authentication |**Symetrický klíč** |**Symetrický klíč** |
| Model zabezpečení |Delegovaný přístup prostřednictvím tokenů SAS. |VEDE |
| Federace zprostředkovatele identity |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Každý požadavek na jednu z technologií služby Řízení front zpráv musí být ověřen. Veřejné fronty s anonymním přístupem se nepodporují. Pomocí [SAS](service-bus-sas.md)můžete vyřešit tento scénář publikováním SAS pouze pro zápis, SAS jen pro čtení nebo i pomocí SAS s úplným přístupem.
* Schéma ověřování poskytované frontami úložiště zahrnuje použití symetrického klíče, což je ověřovací kód zprávy založený na hodnotě hash (HMAC) počítaný pomocí algoritmu SHA-256 a zakódovaný jako řetězec **Base64** . Další informace o příslušném protokolu najdete v tématu [ověřování pro službu Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Fronty Service Bus podporují podobný model pomocí symetrických klíčů. Další informace najdete v tématu [ověřování pomocí sdíleného přístupového podpisu s Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Závěr
Získáním hlubšího porozumění těchto dvou technologií budete moci učinit rozhodnutí o tom, která technologie fronty se má použít, a kdy. Rozhodnutí o tom, kdy používat fronty úložiště nebo Service Bus fronty, je jasně závislá na mnoha faktorech. Tyto faktory můžou záviset na jednotlivých potřebách vaší aplikace a její architektury. Pokud již vaše aplikace používá základní funkce Microsoft Azure, můžete chtít zvolit fronty úložiště, zejména pokud požadujete základní komunikaci a zasílání zpráv mezi službami nebo potřebujete fronty, jejichž velikost může být větší než 80 GB.

Vzhledem k tomu, že Service Bus fronty poskytují řadu pokročilých funkcí, jako jsou například relace, transakce, zjišťování duplicit, automatické nedoručení a trvalé možnosti publikování a odběru, mohou být upřednostňovanou volbou, pokud vytváříte hybridní aplikaci nebo pokud vaše aplikace jinak vyžaduje tyto funkce.

## <a name="next-steps"></a>Další kroky
Následující články poskytují další doprovodné materiály a informace o používání front úložiště nebo Service Bus front.

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat službu Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Osvědčené postupy pro zlepšení výkonu pomocí Service Bus zprostředkovaných zpráv](service-bus-performance-improvements.md)
* [Představení front a témat v Azure Service Bus (příspěvek na blogu)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Příručka pro vývojáře k Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Používání služby Řízení front v Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

