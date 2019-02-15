---
title: Asynchronní zasílání zpráv Service Bus | Dokumentace Microsoftu
description: Popis asynchronní zasílání zpráv Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301566"
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

V těchto případech klientské aplikace generuje [System.TimeoutException] [ System.TimeoutException] nebo [MessagingException] [ MessagingException] výjimky. Service Bus obsahuje ke zmírnění tohoto problému v podobě automatické klienta logika opakovaných pokusů. Po vyčerpání doba opakování a zpráva se doručí, můžete prozkoumat pomocí dalších uvedených v článku na [zvládání výpadků a havárií][handling outages and disasters].

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy asynchronního zasílání zpráv ve službě Service Bus, přečtěte si další podrobnosti o [zvládání výpadků a havárií][handling outages and disasters].

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
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
