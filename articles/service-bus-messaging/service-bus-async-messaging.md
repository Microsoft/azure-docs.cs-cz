---
title: Asynchronní zasílání zpráv service bus | Dokumenty společnosti Microsoft
description: Zjistěte, jak Azure Service Bus podporuje asynchronismus prostřednictvím mechanismu úložiště a předávání s frontami, tématy a předplatnými.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761028"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchronní vzory zasílání zpráv a vysoká dostupnost

Asynchronní zasílání zpráv lze implementovat různými způsoby. S fronty, témata a předplatná Azure Service Bus podporuje asynchronismus prostřednictvím úložiště a dopředný mechanismus. V normální (synchronní) operace odesílat zprávy do fronty a témata a přijímat zprávy z fronty a odběry. Aplikace, které píšete, závisí na tom, zda jsou tyto entity vždy k dispozici. Když se změní stav entity z důvodu různých okolností, potřebujete způsob, jak poskytnout entitu s omezenou schopností, která může uspokojit většinu potřeb.

Aplikace obvykle používají vzory asynchronního zasílání zpráv k povolení několika scénářů komunikace. Můžete vytvářet aplikace, ve kterých mohou klienti odesílat zprávy do služeb, i když služba není spuštěna. Pro aplikace, které dochází k shluky komunikace, fronty může pomoci vyrovnat zatížení tím, že poskytuje místo pro ukládání do vyrovnávací paměti komunikace. Nakonec můžete získat jednoduchý, ale efektivní vyrovnávání zatížení distribuovat zprávy na příčce více počítačů.

Chcete-li zachovat dostupnost některé z těchto entit, zvažte řadu různých způsobů, ve kterých se tyto entity mohou jevit jako nedostupné pro systém trvalého zasílání zpráv. Obecně řečeno, vidíme, že entita přestane být k dispozici aplikacím, které píšeme, následujícími různými způsoby:

* Nelze odesílat zprávy.
* Nelze přijímat zprávy.
* Nelze spravovat entity (vytvářet, načítat, aktualizovat nebo odstraňovat entity).
* Nelze kontaktovat službu.

Pro každou z těchto chyb existují různé režimy selhání, které umožňují aplikaci pokračovat v práci na určité úrovni snížené schopnosti. Například systém, který může odesílat zprávy, ale nepřijímat je, může stále přijímat objednávky od zákazníků, ale nemůže tyto objednávky zpracovat. Toto téma popisuje potenciální problémy, které mohou nastat a jak jsou tyto problémy zmírnit. Service Bus zavedla řadu skutečnosti snižující závažnost rizika, které je nutné se rozhodnout pro a toto téma také popisuje pravidla, kterými se řídí použití těchto opt-in zmírnění.

## <a name="reliability-in-service-bus"></a>Spolehlivost v servisní sběrnici
Existuje několik způsobů, jak zpracovat problémy se zprávami a entitami, a existují pokyny, kterými se řídí vhodné použití těchto opatření ke zmírnění rizik. Chcete-li porozumět pokynům, musíte nejprve pochopit, co může v service busu selhat. Vzhledem k návrhu systémů Azure všechny tyto problémy mají tendenci být krátkodobé. Na vysoké úrovni se různé příčiny nedostupnosti zobrazují následovně:

* Omezení z externího systému, na kterém závisí service bus. Omezení dochází z interakcí s prostředky úložiště a výpočetní prostředky.
* Problém pro systém, na kterém závisí service bus. Například daná část úložiště může narazit na problémy.
* Selhání service bus u jednoho subsystému. V takovém případě výpočetní uzel může dostat do nekonzistentního stavu a musí restartovat sám, což způsobuje všechny entity slouží k vyrovnávání zatížení do jiných uzlů. To zase může způsobit krátké období zpracování pomalé zprávy.
* Selhání service bus v rámci datového centra Azure. Jedná se o "katastrofické selhání", během kterého je systém nedostupný po mnoho minut nebo několik hodin.

> [!NOTE]
> Termín **úložiště** může znamenat azure storage a SQL Azure.
> 
> 

Service Bus obsahuje řadu skutečnosti snižující závažnost rizika pro tyto problémy. Následující části popisují každý problém a jejich příslušné skutečnosti snižující závažnost rizika.

### <a name="throttling"></a>Throttling
S Service Bus omezení umožňuje kooperativní správu rychlosti zpráv. Každý jednotlivý uzel service bus obsahuje mnoho entit. Každá z těchto entit klade požadavky na systém z hlediska procesoru, paměti, úložiště a dalších omezujících vlastností. Pokud některý z těchto omezujících položek zjistí využití, které překračuje definované prahové hodnoty, service bus můžete odepřít daný požadavek. Volající obdrží [ServerBusyException][ServerBusyException] a opakování po 10 sekundách.

Jako zmírnění kódu musí číst chybu a zastavit všechny opakování zprávy po dobu nejméně 10 sekund. Vzhledem k tomu, že k chybě může dojít v částech aplikace zákazníka, očekává se, že každý kus nezávisle provede logiku opakování. Kód může snížit pravděpodobnost omezení povolením dělení na frontě nebo tématu.

### <a name="issue-for-an-azure-dependency"></a>Problém pro závislost Azure
Jiné součásti v rámci Azure mohou mít občas problémy se službou. Například při upgradu systému, který service bus používá, může tento systém dočasně zaznamenat snížené možnosti. Chcete-li obejít tyto typy problémů, Service Bus pravidelně zkoumá a implementuje skutečnosti snižující závažnost rizika. Objevují se vedlejší účinky těchto zmírnění. Například pro zpracování přechodných problémů s úložištěm service bus implementuje systém, který umožňuje operace odesílání zpráv pracovat konzistentně. Vzhledem k povaze zmírnění může doručení odeslané zprávy trvat až 15 minut, než se zobrazí v ohrožené frontě nebo předplatném a bude připravena na operaci příjmu. Obecně řečeno, většina entit nebude zacházet tento problém. Však vzhledem k počtu entit v Service Bus v rámci Azure, toto zmírnění je někdy potřeba pro malé podmnožiny zákazníků service bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Selhání sběrnice v jednom subsystému
Při jakékoli aplikaci mohou okolnosti způsobit, že vnitřní součást service bus bude nekonzistentní. Když to služba Service Bus zjistí, shromažďuje data z aplikace, aby pomohla diagnostikovat, co se stalo. Po shromáždění dat je aplikace restartována ve snaze vrátit je do konzistentního stavu. Tento proces probíhá poměrně rychle a výsledkem je, že entita, která se zdá být nedostupná po dobu až několika minut, je mnohem kratší, i když typické prostoje jsou mnohem kratší.

V těchto případech klientská aplikace generuje [Výjimku System.TimeoutException][System.TimeoutException] nebo [MessagingException.][MessagingException] Service Bus obsahuje zmírnění tohoto problému ve formě logiky opakování automatizovaného klienta. Jakmile je doba opakování vyčerpána a zpráva není doručena, můžete prozkoumat pomocí jiných uvedených v článku o [zpracování výpadků a katastrof][handling outages and disasters].

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy asynchronního zasílání zpráv v Service Bus, přečtěte si další podrobnosti o [zpracování výpadků a katastrof][handling outages and disasters].

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
