---
title: Asynchronní zasílání zpráv Service Bus | Dokumentace Microsoftu
description: Popis asynchronní zasílání zpráv Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 9bacce96e65a7aef611bec3ddae8b1872d5f9fae
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391459"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchronní schémata zasílání zpráv a vysoká dostupnost

Asynchronní zasílání zpráv je možné implementovat mnoha různými způsoby. Azure Service Bus pomocí front, témat a odběrů, podporuje asynchronism prostřednictvím úložiště a dopředné mechanismus. V běžném provozu (synchronní) odesílání zpráv do front a témat a příjem zpráv z front a odběrů. Aplikace, který napíšete závislé na tyto entity vždy nebudou dostupné. Při změně stavu entity z důvodu různých okolností, potřebujete poskytovat omezenou schopnost entity, která vyhovuje většině potřeb.

Aplikace obvykle používají asynchronní schémata zasílání zpráv povolit řadu scénářů komunikace. Můžete vytvářet aplikace, ve kterých klientech odesílat zprávy do služby, i v případě, že služba není spuštěná. Zkušenosti, které může pomoci nárůstům komunikace, frontu pro aplikace vyrovnání zátěže působící tím, že poskytuje místo, kde vyrovnávací paměť komunikace. Nakonec můžete získat nástroj pro vyrovnávání zatížení jednoduchý, ale efektivní k distribuci zpráv do více počítačů.

Aby bylo možné zachovat jeho dostupnost některý z těchto entit, vezměte v úvahu celou řadou různých způsobů, ve kterém můžete objeví tyto entity není k dispozici pro odolný systém zasílání zpráv. Obecně řečeno vidíme entity, přestanou být dostupné pro aplikace, kterou napíšeme následující různými způsoby:

* Nelze odesílat zprávy.
* Nelze přijímat zprávy.
* Není možné ji spravovat entity (vytvoření, načtení, aktualizovat nebo odstranit entity).
* Nepovedlo se kontaktovat službu.

Pro každou z těchto chyb existují různé selhání, které umožňují aplikaci i nadále provádět práci na určité úrovni omezenou schopností. Například systém, který může odesílat zprávy, ale není přijímat objednávky může stále přijímat od zákazníků, ale nemůže zpracovat tyto objednávky. Toto téma popisuje možné problémy, které se mohou vyskytnout, a jak se tyto problémy zmírnit. Service Bus má zavedly několika způsoby zmírnění rizik, které musí přihlašují, které toto téma také popisuje pravidla, kterými se řídí užívání těchto zmírnění rizik vyjádřit výslovný souhlas.

## <a name="reliability-in-service-bus"></a>Spolehlivost ve službě Service Bus
Existuje několik způsobů, jak zpracovat zprávu a entity problémy a jsou pokyny pro příslušnou užívání těchto způsoby zmírnění rizik. Informace o tom pokyny, musíte nejprve porozumět, co může selhat ve službě Service Bus. Z důvodu návrhu systémy pro Azure všechny tyto problémy jsou obvykle krátkodobou. Na vysoké úrovni různé příčiny nedostupnosti vypadat následovně:

* Omezení šířky pásma z externího systému, na kterém závisí služby Service Bus. Omezení šířky pásma dochází z interakce s prostředky úložiště a výpočetního výkonu.
* Problém pro systém, na kterém závisí služby Service Bus. Například určitá část úložiště, můžou mít potíže.
* Chyba služby Service Bus na jednoho subsystému. V takovém případě výpočetním uzlu můžete získat do nekonzistentního stavu a musí restartovat, způsobí všechny entity, které slouží k jiným uzlům Vyrovnávání zatížení. To zase může způsobit na krátkou dobu zpracování pomalé zprávy.
* Selhání služby Service Bus v rámci datového centra Azure. Toto je "závažného selhání" naruší systému nedostupný pro mnoho minut nebo několik hodin.

> [!NOTE]
> Termín **úložiště** může znamenat služby Azure Storage a SQL Azure.
> 
> 

Service Bus obsahuje řadu zmírnění těchto problémů. Následující části popisují jednotlivých problémů a jejich odpovídajících způsoby zmírnění rizik.

### <a name="throttling"></a>Throttling
Pomocí služby Service Bus omezování umožňuje správu míra kooperativní zprávy. Jednotlivých uzlů služby Service Bus jsou uloženy mnoho entit. Každé z těchto entit vytváří požadavky na systém z hlediska procesoru, paměti, úložiště a další charakteristiky. Když některý z těchto omezujících vlastností zjistí využití, která překročí definované prahové hodnoty, Service Bus můžete odepřít daného požadavku. Volající obdrží [ServerBusyException] [ ServerBusyException] a opakované pokusy po 10 sekundách.

Jako omezení rizik musí kód čtení chybu a zastavit všechny opakované pokusy zprávy alespoň 10 sekund. Protože k chybě může dojít napříč částí aplikace pro zákazníky, očekává se, že jednotlivé komponenty se spustí nezávisle na sobě logika opakovaných pokusů. Kód můžete snížit pravděpodobnost omezené tím, že dělení ve frontě nebo tématu.

### <a name="issue-for-an-azure-dependency"></a>Problém pro Azure závislost
Ostatní součásti v rámci Azure čas od času může mít problémy se službou. Například když probíhá upgrade systému, který používá Service Bus, systému může dočasně docházet k nižší možnosti. Pro tyto druhy problémů vyřešit, Service Bus pravidelně prověří a implementuje způsoby zmírnění rizik. Zobrazit vedlejší účinky tyto způsoby zmírnění rizik. Service Bus implementuje zpracování přechodné problémy s úložištěm, systému, který umožňuje operace odesílání zpráv do pracují konzistentně. Vzhledem k povaze omezení rizik odeslané zprávy může trvat až 15 minut se zobrazí v ovlivněných fronty nebo odběru a jsou připravená pro operace obdržení. Obecně řečeno většiny entit nesmí k tomuto problému dojde. Ale zadaný počet entit ve službě Service Bus v rámci Azure, toto omezení je někdy potřeba pro malou podmnožinu zákazníků služby Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus selhání jednoho subsystému
S libovolnou aplikací okolností způsobit vnitřní komponenta služby Service Bus nekonzistenci. Když to zjistí služby Service Bus, shromažďuje data z aplikace, které vám pomůže při diagnostice, co se stalo. Jakmile jsou data shromážděna, se aplikace restartuje se vrátit do konzistentního stavu. Tento proces probíhá poměrně rychle a výsledky v entitě uvedené nebudou k dispozici pro až za několik minut, ale typické dolů časy jsou mnohem kratší.

V těchto případech klientské aplikace generuje [System.TimeoutException] [ System.TimeoutException] nebo [MessagingException] [ MessagingException] výjimky. Service Bus obsahuje ke zmírnění tohoto problému v podobě automatické klienta logika opakovaných pokusů. Po vyčerpání doba opakování a zpráva se doručí, můžete prozkoumat pomocí dalších funkcí, jako [spárované obory názvů][paired namespaces]. Spárované obory názvů mají další upozornění, které jsou popsané v tomto článku.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Selhání služby Service Bus v rámci datového centra Azure
Nejpravděpodobnější příčiny selhání v datacentru Azure je neúspěšné nasazení upgradu služby Service Bus nebo závislý systém. Jako platformu vyvstává, se snížila pravděpodobnost, že tento typ selhání. Datacenter selhání může dojít také z důvodů, které zahrnují následující:

* Elektrické výpadek (napájení a generování power zmizí).
* Připojení (internet zalomení mezi klienty a Azure).

V obou případech havárie přírodních a lidmi vybudovaných způsobila problém. Chcete-li tento problém obejít a ujistěte se, že stále můžete odesílat zprávy, můžete použít [spárované obory názvů] [ paired namespaces] zpráv k odeslání na jiné místo, zatímco primární umístění se provádí v pořádku znovu povolit. Další informace najdete v tématu [osvědčené postupy pro aplikace využívající službu Service Bus výpadků a havárií izolační][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Spárované obory názvů
[Spárované obory názvů] [ paired namespaces] funkce podporuje scénáře, ve kterém entita služby Service Bus nebo nasazení v rámci datového centra k dispozici. Když k této události dochází zřídka, distribuované systémy stále musí být připravena ke zpracování nejhorší scénáře. Obvykle této události dojde, protože nějaký element, na kterém závisí služby Service Bus dochází k problému s krátkodobé. Zachování dostupnosti aplikace během výpadku, Service Bus uživatelé můžou dva samostatné obory názvů, pokud možno ve používat samostatná datová centra, k hostování svých entit pro zasílání zpráv. Zbývající část této části používá následující terminologií:

* Primární obor názvů: obor názvů, které vaše aplikace pracuje, pro odesílání a operace příjmu.
* Sekundární obor názvů: obor názvů, který slouží jako záložní pro primární obor názvů. Aplikace logiky nekomunikuje s Tento obor názvů.
* Interval převzetí služeb při selhání: množství času tak, aby přijímal normální selhání předtím, než aplikace se přepne z primárního oboru názvů sekundární obor.

Spárované obory názvů podporují *odeslat dostupnosti*. Posílejte zachovává dostupnost schopnost posílání zpráv. Můžete odeslat dostupnosti vaší aplikace musí splňovat následující požadavky:

1. Zprávy jsou přijímány pouze z primárního oboru názvů.
2. Zprávy odeslané do dané fronty nebo tématu může obdržet mimo pořadí.
3. Mimo pořadí může být doručování zpráv v rámci relace. Toto je konec od normální funkce relací. To znamená, že vaše aplikace používá relace logicky seskupovat zprávy.
4. Stav relace je zachováno pouze u primárního oboru názvů.
5. Primární fronta může do režimu online a začněte přijímat zprávy před sekundární fronty poskytuje všechny zprávy do primární fronty.

Následující části popisují rozhraní API, jak jsou implementované rozhraní API a ukázkový kód ukazuje, že používá funkci. Všimněte si, že jsou fakturační důsledky spojené s touto funkcí.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync rozhraní API
Spárované obory názvů funkce zahrnuje [PairNamespaceAsync] [ PairNamespaceAsync] metodu [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] třídy:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Po dokončení úlohy párování oboru názvů je také dokončena a připravena k provedení akce pro všechny [MessageReceiver][MessageReceiver], [QueueClient] [ QueueClient] , nebo [TopicClient] [ TopicClient] vytvořené pomocí [MessagingFactory] [ MessagingFactory] instance. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] je základní třídou pro různé druhy párování, která jsou k dispozici [MessagingFactory] [ MessagingFactory] objektu. V současné době pouze odvozená třída je jeden s názvem [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], který implementuje požadavky na dostupnost odeslat. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] obsahuje sadu konstruktory, které se vzájemně využívají. Prohlížení konstruktor s parametry, které nejvíce, můžete pochopit chování další konstruktory.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Tyto parametry mají následující význam:

* *secondaryNamespaceManager*: inicializovali [NamespaceManager] [ NamespaceManager] instance pro sekundární obor názvů, který [PairNamespaceAsync] [ PairNamespaceAsync] metodu můžete použít k nastavení sekundární obor názvů. Obor názvů správce se používá získat seznam front v oboru názvů a ujistěte se, že existují požadované nevyřízených položek fronty. Tyto fronty neexistují, jsou vytvořeny. [NamespaceManager] [ NamespaceManager] vyžaduje schopnost vytvořit token se **spravovat** deklarací identity.
* *messagingFactory*: [MessagingFactory] [ MessagingFactory] instance pro sekundární obor názvů. [MessagingFactory] [ MessagingFactory] objektu se používá k odesílání a, pokud [EnableSyphon] [ EnableSyphon] je nastavena na **true**, příjem zpráv z fronty nevyřízených položek.
* *backlogQueueCount*: počet nevyřízených položek fronty vytvořit. Tato hodnota musí být aspoň 1. Při odesílání zpráv do nevyřízených položek, jeden z těchto front náhodně zvolí. Pokud nastavíte hodnotu na 1, pak pouze jedna fronta někdy slouží. Když k tomu dojde, do jednoho protokolu nevyřízených položek fronty generuje chyby klienta není možné jiné nevyřízených položek fronty a odeslání vaší zprávy se pravděpodobně nezdaří. Doporučujeme tuto hodnotu nastavíte na některé větší hodnotu a výchozí hodnota 10. Toto můžete změnit na hodnotu vyšší nebo nižší v závislosti na tom, kolik dat vaše aplikace odešle za den. Každá fronta nevyřízených položek může obsahovat až 5 GB zprávy.
* *failoverInterval*: dobu, během kterého bude přijímat selhání u primárního oboru názvů před přepnutím jakékoli jedné entity na sekundární obor názvů. Na základě entity entity dojde k převzetí služeb při selhání. Entity v jednoho oboru názvů často žijí v různých uzlech v rámci služby Service Bus. Selhání v jedné entitě neznamená selhání v jiném. Nastavte tuto hodnotu na [System.TimeSpan.Zero] [ System.TimeSpan.Zero] převzetí služeb při selhání do sekundární lokality okamžitě po první, nepřechodných selhání. Minimalizovaly chyby aktivující časovače převzetí služeb při selhání jsou všechny [MessagingException] [ MessagingException] ve kterém [IsTransient] [ IsTransient] vlastnost má hodnotu false, nebo [ System.TimeoutException][System.TimeoutException]. Ostatní výjimky, jako například [UnauthorizedAccessException] [ UnauthorizedAccessException] nezpůsobí převzetí služeb při selhání, protože indikuje, že klient není nakonfigurován správně. A [ServerBusyException] [ ServerBusyException] nemá příčina převzetí služeb při selhání protože správné vzor je 10 sekund počkat zprávu odešlete znovu.
* *enableSyphon*: Určuje, že tato konkrétní párování by měl také syphon zprávy ze sekundární obor názvů zpět do primárního oboru názvů. Obecně platí, nastavte tuto hodnotu na aplikace, které odesílání zpráv **false**; aplikace, které přijímají zprávy by měla tuto hodnotu nastavit na **true**. Důvodem je, že často, jsou méně příjemci zprávy než odesílatelé zpráv. V závislosti na počtu příjemců můžete mít jednu aplikaci instanci zpracování Trativod povinností. Použití mnoha příjemci má vliv na fakturaci pro každou frontu nevyřízených položek.

Chcete-li použít kód, vytvořit primární [MessagingFactory] [ MessagingFactory] instance, sekundární [MessagingFactory] [ MessagingFactory] instance, sekundární [ NamespaceManager] [ NamespaceManager] instance a [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instance. Volání může být stejně snadné jako následující:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Pokud úkol vrácený [PairNamespaceAsync] [ PairNamespaceAsync] metoda dokončí, všechno je nastavené a připravené k použití. Předtím, než je úloha vrácena, můžete pravděpodobně nebyly dokončeny všechny práce na pozadí potřebné pro párování pracovat přímo. V důsledku toho by neměl start, zasílání zpráv, dokud se vrátí úkol. Pokud došlo k všechny chyby, jako jsou chybné přihlašovací údaje nebo nepodařilo vytvořit nevyřízené položky fronty, tyto výjimky budou vyvolány po dokončení úkolu. Jakmile vrátí úkol, ověřte, že fronty byly nalezeny nebo vytvořených zkoušení produktu [BacklogQueueCount] [ BacklogQueueCount] vlastnost na vaše [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instance. Pro předchozí kód, který operaci se zobrazí takto:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy asynchronního zasílání zpráv ve službě Service Bus, přečtěte si další podrobnosti o [spárované obory názvů][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
