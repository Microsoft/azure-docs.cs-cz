---
title: Porovnání front služeb Azure Storage a Service Bus
description: Analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Azure.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 31992aa2012009c51cbeae78010ae8ced65fc872
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928303"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Fronty úložiště a fronty Service Bus – porovnání a kontrast
Tento článek analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Microsoft Azure: fronty úložiště a fronty Service Bus. Pomocí těchto informací můžete učinit podrobnější rozhodnutí o tom, které řešení nejlépe vyhovuje vašim potřebám.

## <a name="introduction"></a>Úvod
Azure podporuje dva typy mechanismů fronty: **fronty úložiště** a **fronty Service Bus**.

**Fronty úložiště** jsou součástí infrastruktury [Azure Storage](https://azure.microsoft.com/services/storage/) . Umožňují ukládat velký počet zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování. Další informace najdete v tématu [co jsou Azure Storage fronty](../storage/queues/storage-queues-introduction.md).

**Fronty Service Bus** jsou součástí širší infrastruktury [zasílání zpráv Azure](https://azure.microsoft.com/services/service-bus/) , která podporuje řazení do fronty, publikování a odběru a pokročilejší způsoby integrace. Jsou navržené tak, aby se integroval aplikace nebo součásti aplikací, které můžou zahrnovat víc komunikačních protokolů, kontraktů dat, důvěřujících domén nebo síťových prostředí. Další informace o Service Bus frontách, tématech a předplatných najdete v tématu [Service Bus fronty, témata a](service-bus-queues-topics-subscriptions.md)předplatná.


## <a name="technology-selection-considerations"></a>Požadavky na výběr technologie
Fronty úložiště a Service Bus fronty mají mírně odlišnou sadu funkcí. V závislosti na potřebách konkrétního řešení si můžete vybrat buď jednu, nebo obě.

Při určování technologie zařazené do fronty vyhovuje účelu daného řešení, architekti a vývojářům řešení by měli tato doporučení vzít v úvahu. 

### <a name="consider-using-storage-queues"></a>Zvažte použití front úložiště
Jako architekt nebo vývojář řešení **byste měli zvážit použití front úložiště** v těchto případech:

* Vaše aplikace musí ukládat více než 80 gigabajtů zpráv ve frontě.
* Vaše aplikace chce sledovat průběh zpracování zprávy ve frontě. To je užitečné v případě, že pracovní proces zpracovává selhání zprávy. Další pracovník pak může tyto informace použít k pokračování z místa, kde předchozí pracovní proces skončil.
* Vyžadujete, aby byly v protokolech na straně serveru všechny transakce spouštěny na vašich frontách.

### <a name="consider-using-service-bus-queues"></a>Zvažte použití front Service Bus.
Jako architekt nebo vývojář řešení **byste měli zvážit použití Service Busch front** v těchto případech:

* Vaše řešení musí přijímat zprávy bez nutnosti cyklického dotazování fronty. Pomocí Service Bus můžete dosáhnout pomocí dlouhotrvajících protokolů založených na protokolu TCP, které Service Bus podporuje.
* Vaše řešení vyžaduje, aby fronta poskytovala zaručené doručování FIFO (First-in-first-out).
* Vaše řešení musí podporovat automatickou detekci duplicit.
* Chcete, aby aplikace zpracovala zprávy jako paralelní dlouhodobé proudy (zprávy jsou přidruženy ke streamu pomocí vlastnosti [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) ve zprávě). V tomto modelu každý uzel v náročné aplikaci soutěží na datové proudy, nikoli na zprávy. Když je datový proud přidělen k náročnému uzlu, uzel může prošetřit stav stavu datového proudu aplikace pomocí transakcí.
* Vaše řešení vyžaduje transakční chování a nedělitelnost při odesílání nebo přijímání více zpráv z fronty.
* Vaše aplikace zpracovává zprávy, které mohou překročit 64 KB, ale nezpůsobí přístup k limitu 256-KB.
* Vyřešíte požadavek na poskytování modelu přístupu na základě role do front a různá práva a oprávnění pro odesílatele a příjemce. Další informace najdete v následujících článcích:
    - [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
    - [Ověření z aplikace](authenticate-application.md)
* Velikost fronty nebude větší než 80 GB.
* Chcete použít protokol zasílání zpráv založený na standardu AMQP 1,0. Další informace o AMQP najdete v tématu [Přehled služby Service Bus AMQP](service-bus-amqp-overview.md).
* V konečném důsledku je jakákoli migrace z typu Point-to-Point na základě fronty na vzor zasílání zpráv pro publikování a odběr. Tento model umožňuje integraci dalších přijímačů (předplatitelů). Každý příjemce obdrží nezávislé kopie buď některých nebo všech zpráv odesílaných do fronty. 
* Vaše řešení pro zasílání zpráv musí podporovat záruku doručení "na nejvyšší úrovni", aniž byste museli sestavovat další součásti infrastruktury.
* Vaše řešení potřebuje publikovat a využívat dávky zpráv.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Porovnání front úložiště a Service Bus front
Tabulky v následujících částech poskytují logické seskupení funkcí fronty. Umožňují vám na první pohled porovnávat možnosti dostupné v Azure Storage frontách a Service Busch frontách.

## <a name="foundational-capabilities"></a>Základní možnosti
V této části jsou porovnávány některé základní možnosti služby Řízení front zpráv poskytované frontami úložiště a Service Bus frontami.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Záruka řazení |**Ne** <br/><br>Další informace najdete v první poznámce v části [Další informace](#additional-information) .</br> | **Yes – first-in-first-out (FIFO)**<br/><br>(pomocí [relací zpráv](message-sessions.md)) |
| Záruka na doručení |**Nejméně jednou** |Nejméně **jednou** (pomocí režimu příjmu PeekLock. Je to výchozí nastavení. <br/><br/>**Ve** více než jednou (pomocí režimu Receive ReceiveAndDelete) <br/> <br/> Další informace o různých [režimech příjmu](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Podpora atomických operací |**Ne** |**Ano**<br/><br/> |
| Chování při příjmu |**Bez blokování**<br/><br/>(hned se dokončí, pokud se nenalezne žádná nová zpráva.) |**Blokování s časovým limitem nebo bez něj**<br/><br/>(nabízí dlouhodobé cyklické dotazování nebo ["Comet techniku"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bez blokování**<br/><br/>(jenom prostřednictvím rozhraní API spravovaného rozhraním .NET) |
| Rozhraní API pro vložení stylu |**Ne** |**Ano**<br/><br/>[QueueClient.-Message](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) a [MessageSessionHandler.-Message](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) Sessions rozhraní .NET API. |
| Režim příjmu |**Náhled & zapůjčení** |**Náhled & zámek**<br/><br/>**Přijmout & odstranění** |
| Režim výhradního přístupu |**Na základě zapůjčení** |**Na základě zámku** |
| Doba trvání zapůjčení/zámku |**30 sekund (výchozí)**<br/><br/>**7 dní (maximum)** (můžete obnovit nebo vydat zapůjčení zprávy pomocí rozhraní [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API.) |**60 sekund (výchozí)**<br/><br/>Zámek zprávy můžete obnovit pomocí rozhraní [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| Přesnost zapůjčení nebo zámku |**Úroveň zprávy**<br/><br/>Každá zpráva může mít jinou hodnotu časového limitu, kterou pak můžete podle potřeby aktualizovat při zpracování zprávy pomocí rozhraní [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API. |**Úroveň fronty**<br/><br/>(u všech zpráv je použita přesnost zámku, ale můžete obnovit zámek pomocí rozhraní [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Dávková příjem |**Ano**<br/><br/>(explicitní určení počtu zpráv při načítání zpráv, až do maximálního počtu zpráv 32) |**Ano**<br/><br/>(implicitně povolení předběžného načtení vlastnosti nebo explicitní prostřednictvím použití transakcí) |
| Dávkové odeslání |**Ne** |**Ano**<br/><br/>(prostřednictvím použití transakcí nebo dávkování na straně klienta) |

### <a name="additional-information"></a>Další informace
* Zprávy ve frontách úložiště jsou obvykle nejdříve první, ale někdy můžou být mimo pořadí. Například když vyprší doba trvání viditelnosti zprávy, protože při zpracování zprávy došlo k chybě v klientské aplikaci. Po vypršení časového limitu viditelnosti se zpráva znovu zobrazí ve frontě, aby ji jiný pracovní proces vyřadí do fronty. V tomto okamžiku je možné nově zobrazovanou zprávu umístit do fronty pro opětovné vyřazení z fronty.
* Model garantované služby FIFO v Service Bus fronty vyžaduje použití relací zasílání zpráv. Pokud aplikace selže, zatímco zpracovává zprávu přijatou v režimu **náhledu & zámku** , při dalším přijímači fronty po dobu platnosti relace zasílání zpráv se po vypršení časového limitu TTL (Time-to-Live) zahájí zpráva, která selhala.
* Fronty úložiště jsou navržené tak, aby podporovaly standardní scénáře zařazování do fronty, například následující:
    - Oddělení součástí aplikace za účelem zvýšení škálovatelnosti a tolerance selhání
    - Vyrovnávání zatížení
    - Vytváření pracovních postupů procesu.
* Nekonzistence s ohledem na zpracování zpráv v kontextu relací Service Bus lze zabránit pomocí stavu relace k uložení stavu aplikace relativně ke způsobu zpracování sekvence zpráv relace a pomocí transakcí týkajících se nedostatku přijatých zpráv a aktualizace stavu relace. Tento druh funkce konzistence je někdy označený *přesně po zpracování* v produktech jiných dodavatelů. Jakékoli chyby transakcí způsobí, že se zprávy znovu dostanou a že jsou důvody, proč se termín neshoduje.
* Fronty úložiště poskytují jednotný a konzistentní programovací model napříč frontami, tabulkami a objekty blob – jak pro vývojáře, tak pro provozní týmy.
* Fronty Service Bus poskytují podporu pro místní transakce v kontextu jedné fronty.
* Režim **přijímání a odstraňování** podporovaný nástrojem Service Bus poskytuje možnost snížit počet operací zasílání zpráv (a související náklady) v systému Exchange pro zajištění nižšího objemu doručování.
* Fronty úložiště poskytují zapůjčení možností rozšiřování zapůjčení pro zprávy. Tato funkce umožňuje pracovním procesům udržovat krátká zapůjčení zpráv. Pokud tedy dojde k selhání pracovního procesu, může být zpráva znovu zpracována jiným pracovním procesem. Pracovník může také prodloužit zapůjčení zprávy v případě, že je potřebuje zpracovat delší dobu, než je aktuální zapůjčení.
* Fronty úložiště nabízejí časový limit viditelnosti, který můžete nastavit na enqueuing nebo v zařazování zpráv do fronty. Můžete také aktualizovat zprávu s různými hodnotami zapůjčení za běhu a aktualizovat různé hodnoty napříč zprávami ve stejné frontě. V metadatech fronty jsou definovány Service Bus vypršení časových limitů zámků. Můžete však obnovit zámek voláním metody [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* Maximální časový limit pro blokování operace přijímání v Service Bus frontách je 24 dní. Vypršení časového limitu založeného na REST ale mají maximální hodnotu 55 sekund.
* Dávkování na straně klienta, které poskytuje Service Bus, umožňuje klientovi fronty dávkovat více zpráv do jediné operace odeslání. Dávkování je k dispozici pouze pro operace asynchronního odeslání.
* V případě funkcí, jako je 200 až TB front úložiště (více při virtualizaci účtů) a neomezené fronty, je ideální platformou pro poskytovatele SaaS.
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
| Metriky úložiště |**Ano**<br/><br/>**Minutové metriky** poskytují metriky v reálném čase pro dostupnost, TPS, počty volání rozhraní API, počty chyb a další. Jsou všechny v reálném čase, agregované za minutu a nahlášeny během několika minut od toho, co se právě stalo v produkčním prostředí. Další informace najdete v tématu [o metrikách analýza úložiště](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ano**<br/><br/>(hromadné dotazy voláním [Getqueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Správa stavu |**Ne** |**Ano**<br/><br/>[Microsoft. ServiceBus. Messaging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Přeposílání zpráv |**Ne** |**Ano** |
| Vyprázdnit funkci Queue |**Ano** |**Ne** |
| Skupiny zpráv |**Ne** |**Ano**<br/><br/>(pomocí relací zasílání zpráv) |
| Stav aplikace na jednu skupinu zpráv |**Ne** |**Ano** |
| Vyhledávání duplicit |**Ne** |**Ano**<br/><br/>(konfigurovatelné na straně odesilatele) |
| Procházení skupin zpráv |**Ne** |**Ano** |
| Načítají se relace zpráv podle ID. |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Obě technologie pro řízení front umožňují, aby byla zpráva naplánována na doručení později.
* Při autopřesměrovávání do fronty je možné, že tisíce front mají na jednu frontu automatickou přeposílání zpráv do jediné fronty, ze které přijímající aplikace tuto zprávu spotřebovává. Tento mechanismus můžete použít k zajištění zabezpečení, řízení toku a izolace úložiště mezi jednotlivými vydavateli zpráv.
* Fronty úložiště poskytují podporu pro aktualizaci obsahu zpráv. Tuto funkci můžete použít pro trvalé informace o stavu a přírůstkové aktualizace průběhu do zprávy tak, aby se mohly zpracovat z posledního známého kontrolního bodu, a ne od začátku. U Service Bus front můžete pomocí relací zpráv povolit stejný scénář. Relace umožňují uložit a načíst stav zpracování aplikace (pomocí [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* Fronty Service Bus podporují [nedoručené dopisy](service-bus-dead-letter-queues.md). Může být užitečný pro izolaci zpráv, které splňují následující kritéria:
    - Zprávy nelze úspěšně zpracovat přijímající aplikací. 
    - Zprávy se nemohou dostat do svého cíle z důvodu vypršení platnosti vlastnosti TTL (Time to Live). Hodnota TTL určuje, jak dlouho zpráva zůstane ve frontě. Při Service Bus bude zpráva přesunuta do speciální fronty nazvané $DeadLetterQueue po vypršení časového limitu TTL.
* Chcete-li najít "poškozené" zprávy ve frontách úložiště při vyřazení zprávy, aplikace ověří vlastnost [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) zprávy. Pokud je **DequeueCount** větší než daná prahová hodnota, aplikace přesune zprávu do fronty nedoručených zpráv definované aplikací.
* Fronty úložiště umožňují získat podrobný protokol všech transakcí provedených proti frontě a agregované metriky. Obě tyto možnosti jsou užitečné pro ladění a porozumění způsobu, jakým vaše aplikace používá fronty úložiště. Jsou také užitečné pro vyladění výkonu aplikace a snížení nákladů na používání front.
* Relace zpráv podporované nástrojem Service Bus umožňují, aby byly zprávy patřící do logické skupiny přidružené k přijímači. Vytvoří spřažení podobné relaci mezi zprávami a jejich příslušnými přijímači. Tuto rozšířenou funkci můžete v Service Bus povolit nastavením vlastnosti [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) u zprávy. Přijímačé pak mohou naslouchat konkrétnímu ID relace a přijímat zprávy, které sdílejí zadaný identifikátor relace.
* Funkce Detekce duplicitních dat ve frontách Service Bus automaticky odstraní duplicitní zprávy odeslané do fronty nebo tématu, a to na základě hodnoty vlastnosti [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Kapacita a kvóty
V této části se porovnávají fronty úložiště a Service Bus fronty z perspektivy [kapacity a kvót](service-bus-quotas.md) , které mohou platit.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Maximální velikost fronty |**500 TB**<br/><br/>(omezeno na [jednu kapacitu účtu úložiště](../storage/common/storage-introduction.md#queue-storage)) |**1 GB až 80 GB**<br/><br/>(definováno při vytváření fronty a [Povolení dělení](service-bus-partitioning.md) – viz část "Další informace") |
| Maximální velikost zprávy |**64 kB**<br/><br/>(48 KB při použití kódování **Base64** )<br/><br/>Azure podporuje velké zprávy kombinováním front a objektů BLOB – v takovém bodě můžete každou položku zařadit do fronty až 200 GB. |**256 KB** nebo **1 MB**<br/><br/>(včetně záhlaví a textu, maximální velikost hlavičky: 64 KB).<br/><br/>Závisí na [úrovni služby](service-bus-premium-messaging.md). |
| Maximální hodnota TTL zprávy |**Infinite** (API-Version 2017-07-27 nebo novější) |**TimeSpan. max** |
| Maximální počet front |**Unlimited** |**10 000**<br/><br/>(obor názvů pro službu) |
| Maximální počet souběžných klientů |**Unlimited** |**5 000** |

### <a name="additional-information"></a>Další informace
* Service Bus vynutila omezení velikosti fronty. Maximální velikost fronty je určena při vytváření fronty. Může být v rozmezí 1 GB až 80 GB. Pokud velikost fronty dosáhne tohoto limitu, další příchozí zprávy se odmítnou a volající obdrží výjimku. Další informace o kvótách v Service Bus najdete v tématu [kvóty Service Bus](service-bus-quotas.md).
* Dělení na [úrovni Premium](service-bus-premium-messaging.md)se nepodporuje. Na úrovni Standard pro zasílání zpráv můžete vytvořit Service Bus fronty a témata ve velikosti 1 (výchozí), 2, 3, 4 nebo 5 GB. Když je zapnuté dělení, Service Bus vytvoří 16 kopií (16 oddílů) entity, každé zadané velikosti. Pokud vytvoříte frontu o velikosti 5 GB, přičemž 16 oddílů se změní na maximální velikost fronty (5 × 16) = 80 GB.  V [Azure Portal][Azure portal]můžete zobrazit maximální velikost rozdělené fronty nebo tématu.
* Pokud se ve frontách úložiště nejedná o zabezpečený obsah zprávy, musí být kódovaný v **kódování Base64** . Pokud zprávu zakódujete ve **formátu base64**, může být datová část uživatele až 48 kb namísto 64 KB.
* U Service Busch front se každá zpráva uložená ve frontě skládá ze dvou částí: záhlaví a tělo. Celková velikost zprávy nepřekračuje maximální velikost zprávy podporované vrstvou služby.
* Pokud klienti komunikují s Service Bus frontami přes protokol TCP, maximální počet souběžných připojení k jedné frontě Service Bus je omezený na 100. Toto číslo je sdíleno mezi odesílateli a přijímači. Pokud je dosažena tato kvóta, žádosti o další připojení budou odmítnuty a volající kód bude přijímat výjimku. Toto omezení není uloženo u klientů připojujících se k frontám pomocí rozhraní API založeného na REST.
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
* Rozhraní API pro zprostředkované zasílání zpráv Service Bus používají plně duplexní připojení TCP pro zlepšení výkonu ve srovnání s REST přes protokol HTTP a podporují standardní protokol AMQP 1,0.
* Názvy front úložiště můžou být 3-63 znaků dlouhé, můžou obsahovat malá písmena, číslice a spojovníky. Další informace najdete v tématu [pojmenování front a metadat](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Názvy front Service Bus mohou mít délku až 260 znaků a mají méně omezující pravidla pojmenování. Názvy front Service Bus můžou obsahovat písmena, číslice, tečky, spojovníky a podtržítka.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Tato část popisuje funkce pro ověřování a autorizaci podporované frontami úložiště a frontami Service Bus.

| Kritéria porovnání | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Ověřování |**Symetrický klíč** |**Symetrický klíč** |
| Model zabezpečení |Delegovaný přístup prostřednictvím tokenů SAS. |SAS |
| Federace zprostředkovatele identity |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Každý požadavek na jednu z technologií služby Řízení front zpráv musí být ověřen. Veřejné fronty s anonymním přístupem se nepodporují. Pomocí [SAS](service-bus-sas.md)můžete vyřešit tento scénář publikováním SAS pouze pro zápis, SAS jen pro čtení nebo i pomocí SAS s úplným přístupem.
* Schéma ověřování poskytované frontami úložiště zahrnuje použití symetrického klíče. Tento klíč je ověřovací kód zprávy založený na hodnotě hash (HMAC), vypočítaný pomocí algoritmu SHA-256 a zakódovaný jako řetězec **Base64** . Další informace o příslušném protokolu najdete v tématu [ověřování pro službu Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Fronty Service Bus podporují podobný model pomocí symetrických klíčů. Další informace najdete v tématu [ověřování pomocí sdíleného přístupového podpisu s Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Závěr
Díky lepšímu porozumění těmto dvěma technologiím si můžete učinit podrobnější rozhodnutí o tom, která technologie fronty se má použít, a kdy. Rozhodnutí o tom, kdy používat fronty úložiště nebo Service Bus fronty, je jasně závislá na mnoha faktorech. Tyto faktory můžou záviset na jednotlivých potřebách vaší aplikace a její architektury. 

Můžete chtít zvolit fronty úložiště z následujících důvodů:

- Pokud už vaše aplikace používá základní funkce Microsoft Azure 
- Pokud požadujete základní komunikaci a zasílání zpráv mezi službami 
- Potřebujete fronty, jejichž velikost může být větší než 80 GB.

Fronty Service Bus poskytují řadu pokročilých funkcí, jako jsou následující. To může být Upřednostňovaná volba, pokud vytváříte hybridní aplikaci nebo pokud vaše aplikace jinak vyžaduje tyto funkce.

- [Relace](message-sessions.md)
- [Transakce](service-bus-transactions.md)
- [Vyhledávání duplicit](duplicate-detection.md)
- [Automatické nedoručené písmeno](service-bus-dead-letter-queues.md)
- [Trvalé možnosti publikování a odběru](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Další kroky
Následující články poskytují další doprovodné materiály a informace o používání front úložiště nebo Service Bus front.

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat službu Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Osvědčené postupy pro zlepšení výkonu pomocí Service Bus zprostředkovaných zpráv](service-bus-performance-improvements.md)
* [Představení front a témat v Azure Service Bus (příspěvek na blogu)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Příručka pro vývojáře k Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Používání služby Řízení front v Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

