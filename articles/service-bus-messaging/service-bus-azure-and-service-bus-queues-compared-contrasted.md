---
title: Fronty Azure Storage a fronty služby Service Bus porovnání a rozdíly | Dokumentace Microsoftu
description: Analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Azure.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c59d79a7c6ac0590861c99daa01438b184cd71ff
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852792"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Fronty služby Storage a fronty služby Service Bus – porovnání a rozdíly
Tento článek analyzuje rozdíly a podobnosti mezi dvěma typy front, které nabízí Microsoft Azure ještě dnes: Fronty služby Storage a fronty služby Service Bus. Tyto informace můžete použít ke srovnání příslušných technologií a pomůžou vám kvalifikovaněji se rozhodnout, které řešení nejlíp vyhovuje vašim potřebám.

## <a name="introduction"></a>Úvod
Azure podporuje dva typy mechanismů fronty: **Fronty úložiště** a **fronty služby Service Bus**.

**Fronty úložiště**, které jsou součástí [služby Azure storage](https://azure.microsoft.com/services/storage/) infrastruktury, funkce jednoduché rozhraní založené na protokolu REST GET/PUT/PEEK poskytují spolehlivé, trvalé zasílání zpráv v rámci a mezi službami.

**Fronty služby Service Bus** jsou součástí širší [zasílání zpráv Azure](https://azure.microsoft.com/services/service-bus/) infrastruktura, která podporuje služby Řízení front a publikování/odběr a další pokročilé integrace vzory. Další informace o Service Bus fronty a témata nebo předplatná, najdete v článku [Přehled služby Service Bus](service-bus-messaging-overview.md).

Přestože obě služby Řízení front technologie existovat současně, fronty úložiště byla zavedena Zaprvé, jako mechanismus pro ukládání vyhrazenou frontu postavené na služby Azure Storage. Fronty služby Service Bus jsou postavené na širší infrastruktura zasílání zpráv navržené tak, aby aplikace nebo součásti aplikace, které může zahrnovat více komunikační protokoly, kontrakty dat, důvěryhodnosti domén nebo síťových prostředích integrovat.

## <a name="technology-selection-considerations"></a>Důležité informace o výběr technologie
Fronty služby Storage a fronty služby Service Bus jsou implementace služby Microsoft Azure v současné době nabízena front. Každý má sadu mírně odlišné funkce, což znamená, že zvolte jeden z nich nebo obě, v závislosti na potřebách vaší konkrétní řešení nebo obchodní nebo technické potíže jsou řešení použít.

Při určování služby Řízení front technologii, která vyhovuje účel pro dané řešení, zvažte tato doporučení architekti a vývojáři řešení. Další podrobnosti najdete v další části.

Jako architekt řešení nebo vývojář **byste měli zvážit použití front služby Storage** při:

* Aplikace musí ukládat více než 80 GB zprávy ve frontě.
* Vaše aplikace vyžaduje můžete sledovat postup prací pro zpracování zprávy ve frontě. To je užitečné, pokud dojde k chybě pracovního procesu zpracovává zprávu. Následné pracovního procesu můžete pokračovat tam, kde předchozí pracovní proces přestali pak pomocí těchto informací.
* Vyžadujete protokoly na straně serveru všechny transakce pro vaši front.

Jako architekt řešení nebo vývojář **byste měli zvážit použití front služby Service Bus** při:

* Řešení musí být schopný přijímat zprávy bez nutnosti dotazovat fronty. Pomocí Service Bus toho lze dosáhnout pomocí s dlouhým intervalem dotazování přijímat operace, pomocí které Service Bus podporuje protokoly založené na TCP.
* Vaše řešení nevyžaduje fronty k poskytování garantovanou first-in-first-out (FIFO) objednané dodání.
* Řešení musí být schopni poskytovat podporu automatického zjišťování duplicit.
* Aplikace pro zpracování zpráv jako paralelní datové proudy dlouho běžící (zprávy jsou spojeny s pomocí datového proudu [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) vlastnost zprávy). V tomto modelu bojuje každý uzel v spotřebitelskou aplikací datových proudů, na rozdíl od zprávy. Když je datový proud náročné uzlu, uzlu můžete zkontrolovat stav stav datového proudu aplikace pomocí transakce.
* Vaše řešení nevyžaduje transakční chování a atomicitu při odesílání nebo přijímání více zpráv z fronty.
* Vaše aplikace zpracovává zprávy, které mohou být delší než 64 KB, ale bude omezovat přístup není pravděpodobné 256 KB.
* Využívání požadavek, poskytují model přístupu na základě rolí pro fronty a různá práva a oprávnění pro odesílateli a příjemci.
* Velikost fronty nebude zvětšovat větší než 80 GB.
* Chcete použít pro zasílání zpráv protokolu založené na standardech AMQP 1.0. Další informace o protokolu AMQP najdete v tématu [přehled AMQP služby Service Bus](service-bus-amqp-overview.md).
* Konečný výsledek migrace z komunikaci na základě fronty typu point-to-point do vzorce výměny zpráv, které umožňuje bezproblémovou integraci další příjemci (předplatitele), každý z nich obdrží nezávislých kopií některých nebo všech hlavních zprávy odeslané do fronty. Druhá možnost odkazuje na funkci Publikovat/odebírat nativně poskytované služby Service Bus.
* Řešení zasílání zpráv musí být schopni poskytovat podporu garance doručení "Na většinu-jednou" bez nutnosti vytváření komponent další infrastrukturu.
* Chcete mít možnost publikovat a využívat dávky zprávy.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porovnání front služby Storage a fronty služby Service Bus
Tabulky v následujících částech poskytují možnost logického seskupování funkce front a umožňují porovnat, na první pohled, možnosti, které jsou k dispozici ve frontách Azure Storage a fronty služby Service Bus.

## <a name="foundational-capabilities"></a>Základní možnosti
Tato část porovnává některých základních funkcí front zpráv poskytuje fronty úložiště a front služby Service Bus.

| Porovnání kritérií | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Řazení se zárukou |**Ne** <br/><br>Další informace najdete v tématu první poznámky v části "Další informace".</br> |**Ano - First-In-First-Out (FIFO)**<br/><br>(pomocí zasílání zpráv relací) |
| Garance doručení |**Na alespoň jedno** |**Na alespoň jedno**<br/><br/>**Jednou na většinu** |
| Podpora atomické operace |**Ne** |**Ano**<br/><br/> |
| Přijímat chování |**Neblokující**<br/><br/>(dokončení okamžitě pokud se nenajde žádné nové zprávy) |**Blokování s nebo bez časového limitu**<br/><br/>(nabízí dlouhý interval dotazování, nebo ["Comet techniku"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Neblokující**<br/><br/>(pomocí rozhraní .NET API režim pouze spravovaný) |
| Push – vizuální styl rozhraní API |**Ne** |**Ano**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) a **OnMessage** relace rozhraní .NET API. |
| Zobrazí režim |**Náhled & zapůjčení** |**Náhled & uzamčení**<br/><br/>**Zobrazit & Odstranit** |
| Režim výhradní přístup |**Na základě zapůjčení** |**Na základě zámku** |
| Doba trvání zapůjčení/Lock |**30 sekund (výchozí)**<br/><br/>**7 dní (maximální)** (můžete obnovit nebo uvolnit zapůjčení zprávy použijte [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) rozhraní API.) |**60 sekund (výchozí)**<br/><br/>Obnovení můžete provést pomocí uzamčení zprávy [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) rozhraní API. |
| Zapůjčení/Lock přesnosti |**Úroveň zprávy**<br/><br/>(každá zpráva může mít hodnotu jiný časový limit, který může aktualizovat podle potřeby při zpracování zprávy, [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) rozhraní API) |**Úroveň fronty**<br/><br/>(každá fronta má zámek přesnost použít na všechny zprávy, ale můžete obnovit pomocí zámku [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) rozhraní API.) |
| V dávce přijímat |**Ano**<br/><br/>(explicitní určení počtu zpráv při načítání zpráv až do maximálního počtu 32 zpráv) |**Ano**<br/><br/>(implicitně povolení před načtením vlastnosti nebo explicitně pomocí transakcí) |
| Dávkové odesílání |**Ne** |**Ano**<br/><br/>(pomocí transakce nebo dávkování na straně klienta) |

### <a name="additional-information"></a>Další informace
* Zprávy do fronty služby Storage jsou obvykle first-in-first-out, ale někdy může být mimo pořadí; například dobu trvání časového limitu viditelnost zpráva vypršení platnosti (třeba kvůli klientské aplikaci k chybám při zpracování). Když vyprší časový limit viditelnosti, zpráva se zobrazí znovu ve frontě pro jiný pracovní proces k jeho odstranění z fronty. V tu chvíli se nově viditelný zpráva může umístit ve frontě (vyjmutou znovu) za zprávu, která byla původně zařazených do fronty za ním.
* Zaručené vzor FIFO do fronty služby Service Bus vyžaduje použití relací zasílání zpráv. V případě, že aplikace dojde k chybě při zpracování zprávy dostali **Náhled & uzamčení** režimu při příštím fronty přijímač přijímá relaci zasílání zpráv, spustí zprávou o selhání po jeho time to live (TTL) období platnosti.
* Fronty služby Storage jsou navrženy pro podporu standardní řízení front zpráv scénáře, jako je oddělovací součásti aplikace chcete zvýšit škálovatelnost a odolnost pro selhání načtení vyrovnávání a postupy procesů sestavování buildů.
* Podpora fronty služby Service Bus *na alespoň jedno* garance doručení. Kromě toho *jednou na většinu* sémantické může podporovat používání stavu relace pro uložení stavu aplikace a použitím transakce atomicky přijímat zprávy a aktualizovat stav relace.
* Fronty úložiště poskytují jednotnou a konzistentní programovací model napříč front, tabulek a objektů blob – pro vývojáře a pro provozní týmy.
* Fronty Service Bus poskytují podporu pro místní transakce v rámci jediné fronty.
* **Přijme a odstraní** režim podporovaný ve službě Service Bus poskytuje možnost omezit zasílání zpráv počet operací (a související náklady) výměnou za sníženou doručování assurance.
* Fronty úložiště poskytují zapůjčení umožňuje rozšířit zapůjčení pro zprávy. To umožňuje pracovníkům udržovat krátký zapůjčení na zprávy. Proto pokud pracovní proces selže, zprávy mohou být rychle zpracovány znovu jinému pracovnímu procesu. Kromě toho pracovního procesu můžete rozšířit zapůjčení na zprávu, pokud je potřeba zpracovat je delší než aktuální doba zapůjčení.
* Fronty úložiště nabízejí viditelnost časový limit, který můžete nastavit po zařadit nebo vyřazování z fronty zpráv. Kromě toho můžete aktualizovat zprávu s hodnotami různých zapůjčení v době běhu a aktualizace rozdílné hodnoty napříč zprávy ve stejné frontě. Vypršení časového limitu zámku pro Service Bus jsou definované v metadatech fronty; však můžete obnovit zámek voláním [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metody.
* Maximální časový limit blokování přijímat provoz do front služby Service Bus je 24 dní. Založené na protokolu REST vypršení časového limitu však mají hodnotu maximální 55 sekund.
* Dávkování na straně klienta k dispozici ve službě Service Bus umožňuje klientovi fronty dávce více zpráv do jedné odesílání provozu. Dávkování je k dispozici pouze pro operace asynchronního odeslání.
* Funkce, jako je 200 TB horní mez front služby Storage (více při virtualizaci účty) a neomezený počet front usnadňují ideální platformu pro poskytovatele SaaS.
* Fronty úložiště poskytují flexibilní a výkonné delegovat mechanismus řízení přístupu.

## <a name="advanced-capabilities"></a>Pokročilé funkce
Tato část porovnává pokročilé funkce, která poskytuje fronty úložiště a front služby Service Bus.

| Porovnání kritérií | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Naplánované doručení |**Ano** |**Ano** |
| Automatické nedoručení |**Ne** |**Ano** |
| Tím zvyšuje její hodnotu time-to-live fronty |**Ano**<br/><br/>(přes místní aktualizace časový limit viditelnosti) |**Ano**<br/><br/>(k dispozici prostřednictvím vyhrazené funkce rozhraní API) |
| Zacházení s nezpracovatelnými podpora zpráv |**Ano** |**Ano** |
| Aktualizace na místě |**Ano** |**Ano** |
| Protokol transakce na straně serveru |**Ano** |**Ne** |
| Metriky úložiště |**Ano**<br/><br/>**Minutu metriky**: poskytuje v reálném čase metriky dostupnosti TPS, rozhraní API pro volání počty, počet chyb a informace v reálném čase (agregované za minutu a hlášená během několika minut od co se právě stalo v produkčním prostředí. Další informace najdete v tématu [o metrikách Storage Analytics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ano**<br/><br/>(hromadně dotazy voláním [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Správa stavu |**Ne** |**Ano**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatické předávání zpráv |**Ne** |**Ano** |
| Vyprázdnit frontu – funkce |**Ano** |**Ne** |
| Skupiny zpráv |**Ne** |**Ano**<br/><br/>(pomocí zasílání zpráv relací) |
| Stav aplikace na skupinu zpráv |**Ne** |**Ano** |
| Vyhledávání duplicit |**Ne** |**Ano**<br/><br/>(možnost konfigurace na straně odesílatele) |
| Procházení skupiny zpráv |**Ne** |**Ano** |
| Načítají se relace zpráv podle ID |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Obě služby Řízení front technologie povolit zprávu naplánovaných pro doručování později.
* Fronty automatickým přeposíláním umožňuje tisíce fronty a auto-forward své zprávy do jediné fronty, ze kterého využívá přijímající aplikaci zprávu. Můžete používat tento mechanismus k zajištění zabezpečení, řízení toku a izolovat úložiště mezi každému zdroji zprávy.
* Fronty úložiště poskytují podporu pro aktualizaci obsahu zprávy. Tuto funkci můžete použít pro zachování informací o stavu a průběhu přírůstkové aktualizace do zprávy tak, aby ji mohla zpracovat od posledního známého kontrolního bodu, místo od začátku. S frontami služby Service Bus můžete povolit stejný scénář prostřednictvím relace zpráv. Relace umožňují ukládání a načítání stavu zpracování aplikace (s použitím [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Dead písmem](service-bus-dead-letter-queues.md), což je jen podporované fronty služby Service Bus, může být užitečné pro izolování zpráv, který nemůže úspěšně zpracovat přijímající aplikace nebo když nelze zprávy dorazí do cíle z důvodu neplatné (time-to-live Vlastnosti TTL). Hodnota TTL určuje, jak dlouho zpráva zůstane ve frontě. Pomocí služby Service Bus přesune do speciální fronty s názvem $DeadLetterQueue, jakmile vyprší doba TTL zprávy.
* K vyhledání "poškozené" zprávy do fronty úložiště při vyřazování z fronty zprávy aplikace zkontroluje [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) vlastnost zprávy. Pokud **DequeueCount** je větší než dané prahové hodnoty, aplikace přesune zprávu do fronty služby definované aplikací "nedoručené zprávy".
* Fronty úložiště umožňují získat podrobný protokol všechny transakce pro fronty, jako dobře agregovaných metrik. Obě tyto možnosti jsou užitečné pro ladění a pochopení, jak vaše aplikace používá fronty úložiště. Jsou také užitečná pro vaše aplikace optimalizace výkonu a snížení nákladů na použití fronty.
* Pojem "relace zpráv" podporované ve službě Service Bus umožňuje zprávy, které patří do určité logické skupiny má být přidružena k dané příjemce, který potom vytvoří relaci jako spřažení mezi zpráv a jejich odpovídajících příjemců. Můžete ho povolit rozšířené funkce ve službě Service Bus tím, že nastavíte [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) vlastnosti ve zprávě. Příjemci můžete naslouchání ID konkrétní relace a přijímat zprávy, které sdílejí identifikátor zadaná relace.
* Funkce detekce duplicitních podporuje fronty služby Service Bus automaticky odebere duplicitní zprávy odeslané do fronty nebo tématu podle hodnoty [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) vlastnost.

## <a name="capacity-and-quotas"></a>Kapacity a kvót
Tato část porovnává fronty služby Storage a fronty služby Service Bus z hlediska [kapacity a kvót](service-bus-quotas.md) , který může použít.

| Porovnání kritérií | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Maximální velikost fronty |**500 TB**<br/><br/>(omezeno na [jednotné kapacitě účtu úložiště](../storage/common/storage-introduction.md#queue-storage)) |**1 GB až 80 GB**<br/><br/>(definované při vytvoření fronty a [povolení dělení](service-bus-partitioning.md) – naleznete v části "Další informace") |
| Maximální velikost zprávy |**64 KB**<br/><br/>(48 KB při použití **Base64** kódování)<br/><br/>Azure podporuje velké zprávy kombinací fronty a objekty BLOB – v tomto okamžiku je možné zařadit do fronty až 200 GB pro jednu položku. |**256 KB** nebo **1 MB**<br/><br/>(včetně záhlaví a text záhlaví maximální velikost: 64 KB).<br/><br/>Závisí [úroveň služby](service-bus-premium-messaging.md). |
| Maximální hodnota TTL zprávy |**Nekonečné** (od verze rozhraní api 2017-07-27) |**TimeSpan.Max** |
| Maximální počet front |**Unlimited** |**10,000**<br/><br/>(za obor názvů služby) |
| Maximální počet souběžných klientů |**Unlimited** |**Unlimited**<br/><br/>(100 souběžných připojení omezení platí jenom pro komunikaci založené na protokolu TCP) |

### <a name="additional-information"></a>Další informace
* Service Bus vynucuje limitech velikosti fronty. Maximální velikost fronty je určen při vytváření fronty a může mít hodnotu mezi 1 a 80 GB. Když se dosáhne velikosti fronty nastavenou na vytvoření fronty další příchozí zprávy budou odmítnuty a volající kód bude přijímat výjimku. Další informace o kvótách ve službě Service Bus najdete v tématu [kvóty služby Service Bus](service-bus-quotas.md).
* Vytváření oddílů se nepodporuje v [úroveň Premium](service-bus-premium-messaging.md). Na úrovni Standard můžete vytvořit front služby Service Bus do velikosti 1, 2, 3, 4 nebo 5 GB (výchozí hodnota je 1 GB). Ve standardním úroveň s dělením povolené (což je výchozí hodnota), Service Bus vytváří 16 oddíly pro každý GB zadáte. V důsledku toho pokud vytvoříte frontu, která velikost je 5 GB, s 16 oddíly maximální velikost fronty se stane (5 * 16) = 80 GB. Zobrazí maximální velikost dělená fronta nebo téma zobrazením vstupu [webu Azure portal][Azure portal].
* V případě front úložiště, pokud není obsah zprávy XML typově bezpečný, pak musí být **Base64** kódování. Pokud jste **Base64**– kódování zprávy, datové části uživatele může být až 48 KB, namísto 64 KB.
* S frontami služby Service Bus, každé zprávy, uloženy ve frontě se skládá ze dvou částí: Hlavička a tělo. Celková velikost zprávy nesmí překročit maximální velikost podporovaná úroveň služby.
* Pokud klienti komunikují s frontami služby Service Bus přes protokol TCP, maximální počet souběžných připojení do jediné fronty služby Service Bus je omezený na 100. Toto číslo se sdílí mezi odesílateli a příjemci. Při dosažení této kvóty odeslání dalších žádostí o další připojení budou odmítnuty a volající kód bude přijímat výjimku. Toto omezení není uloženo na připojení klientů k fronty pomocí rozhraní REST API.
* Pokud požadujete víc než 10 000 front do jednoho oboru názvů služby Service Bus, můžete kontaktovat tým podpory Azure a požádat o zvýšení. Pokud chcete škálovat na více než 10 000 front službou Service Bus, můžete také vytvořit pomocí další obory názvů [webu Azure portal][Azure portal].

## <a name="management-and-operations"></a>Operace a Správa
Tato část obsahuje porovnání funkcí správy, poskytuje fronty úložiště a front služby Service Bus.

| Porovnání kritérií | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Protokol pro správu |**Rozhraní REST přes protokol HTTP/HTTPS** |**Rozhraní REST přes protokol HTTPS** |
| Modul runtime protokolu |**Rozhraní REST přes protokol HTTP/HTTPS** |**Rozhraní REST přes protokol HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP s TLS)** |
| .NET API |**Ano**<br/><br/>(.NET API klienta úložiště) |**Ano**<br/><br/>(.NET služby Service Bus rozhraní API) |
| Nativní kód C++ |**Ano** |**Ano** |
| Java API |**Ano** |**Ano** |
| PHP API |**Ano** |**Ano** |
| Rozhraní API Node.js |**Ano** |**Ano** |
| Podpora libovolného metadat |**Ano** |**Ne** |
| Fronty – pravidla pojmenování |**Až 63 znaků**<br/><br/>(Písmena v názvu fronty musí být malými písmeny.) |**Dlouhé až 260 znaků.**<br/><br/>(Fronta cesty a názvy jsou malá a velká písmena.) |
| Získání délky fronty – funkce |**Ano**<br/><br/>(Přibližná hodnotu, pokud platnost zprávy vyprší mimo interval TTL, ZÍSKÁ bez odstranění.) |**Ano**<br/><br/>(Bodu v čase, přesné hodnoty.) |
| Operace Peek – funkce |**Ano** |**Ano** |

### <a name="additional-information"></a>Další informace
* Fronty úložiště poskytují podporu pro libovolné atributy, které lze použít k označení fronty ve formě dvojice název/hodnota.
* Obě technologie fronty nabízejí možnost prohlížet zprávy bez nutnosti uzamknout, což může být užitečné při implementaci nástroj Průzkumník a prohlížeče fronty.
* Rozhraní .NET služby Service Bus pro zprostředkované zasílání zpráv rozhraní API využití plně duplexní připojení TCP za účelem vylepšení výkonu oproti REST přes protokol HTTP a podporují standardní protokol AMQP 1.0.
* Názvy front úložiště může být dlouhý 3 až 63 znaků, může obsahovat malá písmena, číslice a pomlčky. Další informace najdete v tématu [pojmenování front a Metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Názvy front služby Service Bus můžou být až 260 znaků a volnější pravidla pojmenování. Názvy front služby Service Bus může obsahovat písmena, číslice, tečky, spojovníky a podtržítka.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Tento oddíl popisuje funkce ověřování a autorizaci podporovaných front služby Storage a fronty služby Service Bus.

| Porovnání kritérií | Fronty úložiště | Fronty služby Service Bus |
| --- | --- | --- |
| Authentication |**Symetrický klíč** |**Symetrický klíč** |
| Model zabezpečení |Delegovaný přístup prostřednictvím tokeny SAS. |SAS |
| Poskytovatel federace identit |**Ne** |**Ano** |

### <a name="additional-information"></a>Další informace
* Každý požadavek buď zařazování do fronty technologií se musí ověřit. Veřejné fronty s anonymního přístupu nejsou podporovány. Pomocí [SAS](service-bus-sas.md), je-li vyřešit tento scénář publikování pouze pro zápis SAS, sdílený přístupový podpis jen pro čtení nebo dokonce úplný přístup SAS.
* Schéma ověřování zadané pomocí úložiště front zahrnuje použití symetrický klíč, který je základě algoritmu hash kód (metoda HMAC Message Authentication), vypočtenému pomocí algoritmu SHA-256 a kódováním **Base64** řetězec. Další informace o příslušných protokolu naleznete v tématu [ověřování pro služby Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Fronty Service Bus podporují podobné modelu pomocí symetrických klíčů. Další informace najdete v tématu [sdíleného přístupu podpis ověřování pomocí služby Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Závěr
Na základě získání lepší představu o dvě technologie, budou mít možnost provádět kvalifikovaněji se rozhodnout na technologii fronty, která chcete použít a kdy. Rozhodnutí o použití úložiště fronty nebo fronty Service Bus, jasně závisí na řadě faktorů. Tyto faktory mohou silně závisí na individuálních potřeb vaší aplikace a jeho architektura. Pokud už vaše aplikace používá základní možnosti Microsoft Azure, můžete chtít raději k výběru fronty úložiště, zejména v případě, že budete potřebovat základní komunikaci a přenos zpráv mezi služby nebo nutnosti front, které může být větší než 80 GB.

Protože fronty Service Bus poskytují řadu pokročilých funkcí, jako jsou relace, transakce, vyhledávání duplicit, automatické dead-lettering a odolné funkce pbulikovat/odebírat, mohou být upřednostňovanou volbu Pokud vytváříte hybridní aplikace nebo pokud vaše aplikace jinak vyžaduje tyto funkce.

## <a name="next-steps"></a>Další postup
Následující články poskytují další doprovodné materiály a informace o používání front úložiště nebo fronty Service Bus.

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat službu Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Osvědčené postupy pro zlepšení výkonu pomocí služby Service Bus pro zprostředkované zasílání zpráv](service-bus-performance-improvements.md)
* [Úvod do fronty a témata ve službě Azure Service Bus (příspěvek na blogu)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Příručka pro vývojáře k Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Použití službou řazení front v Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

