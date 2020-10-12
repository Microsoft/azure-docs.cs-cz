---
title: Service Bus asynchronní zasílání zpráv | Microsoft Docs
description: Přečtěte si, jak Azure Service Bus podporuje asynchronism prostřednictvím úložiště a předávacího mechanismu s frontami, tématy a předplatnými.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 0ea2a2f51d29f1a65926509581df49b453bde59f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067541"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchronní vzorce zasílání zpráv a vysoká dostupnost

Asynchronní zasílání zpráv je možné implementovat různými způsoby. S frontami, tématy a odběry Azure Service Bus podporuje asynchronism prostřednictvím úložiště a mechanismu dopředné. Při normální (synchronní) operaci můžete odesílat zprávy do front a témat a přijímat zprávy z front a odběrů. Aplikace, které zapisujete, závisí vždy na dostupných entitách. Pokud se stav entity změní z různých důvodů, budete potřebovat způsob, jak poskytnout entitu se sníženou schopností, která může splnit většinu potřeb.

Aplikace obvykle využívají vzorce asynchronního zasílání zpráv k zajištění řady komunikačních scénářů. Můžete vytvářet aplikace, ve kterých mohou klienti odesílat zprávy službám, a to i v případě, že služba není spuštěná. Pro aplikace, které zajišťují shluky komunikace, může fronta zvýšit úroveň zátěže tím, že poskytuje místo pro komunikaci ve vyrovnávací paměti. Nakonec můžete získat jednoduchý, ale účinný nástroj pro vyrovnávání zatížení k distribuci zpráv napříč více počítači.

Aby se zachovala dostupnost kterékoli z těchto entit, vezměte v úvahu řadu různých způsobů, jak se tyto entity můžou zobrazovat jako nedostupné pro trvalý systém zasílání zpráv. Obecně řečeno, uvidíme, že entita se stane nedostupnou pro aplikace, které zapisujeme následujícími různými způsoby:

* Nelze odeslat zprávy.
* Nelze přijmout zprávy.
* Nelze spravovat entity (vytvořit, načíst, aktualizovat nebo odstranit entity).
* Nepovedlo se kontaktovat službu.

U každého z těchto selhání existují různé režimy selhání, které umožňují aplikaci pokračovat v práci na určité úrovni snížené možnosti. Například systém, který může odesílat zprávy, ale nepřijímá je, může stále přijímat objednávky od zákazníků, ale nemůže tyto objednávky zpracovat. Toto téma popisuje možné problémy, ke kterým může dojít, a o tom, jak jsou tyto problémy zmírňované. Service Bus zavedla řadu rizik, která musíte vyjádřit, a toto téma také popisuje pravidla pro používání těchto rizik.

## <a name="reliability-in-service-bus"></a>Spolehlivost v Service Bus
Existuje několik způsobů, jak zpracovat zprávy a problémy s entitami a že existují pokyny pro příslušné používání těchto rizik. Abyste porozuměli pokynům, musíte nejdřív porozumět tomu, co může v Service Bus selhat. Vzhledem k návrhům systémů Azure jsou všechny tyto problémy v úmyslu krátkodobě dlouhodobé. V nejvyšší úrovni se v různých příčinách nedostupnosti zobrazují tyto příčiny:

* Omezování z externího systému, na kterém Service Bus závisí. Omezování probíhá v interakcích s úložištěm a výpočetními prostředky.
* Problém pro systém, na kterém Service Bus závisí. Například daná část úložiště může narazit na problémy.
* Selhání Service Bus v jednom subsystému. V této situaci se může výpočetní uzel dostat do nekonzistentního stavu a musí se sám restartovat, což způsobí, že všechny entity slouží k vyrovnávání zatížení s ostatními uzly. To zase může způsobit krátkou dobu zpracování zpráv.
* Selhání Service Bus v datovém centru Azure. Toto je "závažná chyba", během které je systém nedosažitelný po dobu několika minut nebo několik hodin.

> [!NOTE]
> Termín **úložiště** může znamenat Azure Storage i SQL Azure.
> 
> 

Service Bus obsahuje několik rizik pro tyto problémy. V následujících částech najdete jednotlivé problémy a jejich příslušná zmírnění.

### <a name="throttling"></a>Throttling
U Service Bus omezování povoluje správu přenosů zpráv mezi družstvy. Každý jednotlivý Service Bus uzel domy mnoho entit. Každá z těchto entit zajišťuje požadavky na systém z pohledu PROCESORů, paměti, úložiště a dalších omezujících vlastností. Pokud některá z těchto omezujících vlastností detekuje použití, které překračuje definované prahové hodnoty, Service Bus může odepřít daný požadavek. Volající obdrží [výjimka serverbusyexception][ServerBusyException] a zopakuje pokus po 10 sekundách.

V takovém případě musí kód přečíst chybu a zastavit všechny opakované pokusy zprávy alespoň na 10 sekund. Vzhledem k tomu, že k chybě může dojít v rámci zákaznických aplikací, je očekáváno, že všechny části nezávisle spustí logiku opakování. Kód může snížit pravděpodobnost omezení tím, že povolí vytváření oddílů ve frontě nebo tématu.

### <a name="issue-for-an-azure-dependency"></a>Problém pro závislost Azure
Jiné komponenty v Azure můžou mít někdy problémy se službou. Například při upgradu systému, který používá Service Bus, může tento systém dočasně mít omezené možnosti. K řešení těchto typů problémů Service Bus pravidelně zkoumá a implementuje zmírnění rizik. Zobrazí se vedlejší účinky těchto rizik. Například pro zpracování přechodných problémů s úložištěm Service Bus implementuje systém, který umožňuje, aby operace odesílání zpráv byly konzistentně fungovat. Vzhledem k povaze zmírňování může odeslaná zpráva trvat až 15 minut, než se zobrazí v ovlivněné frontě nebo předplatném a bude připravená na operaci přijetí. Obecně řečeno, většina entit tyto potíže neprojeví. Nicméně s ohledem na počet entit v Service Bus v rámci Azure se toto zmírnění někdy vyžaduje pro malou podmnožinu zákazníků Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus selhání v jednom subsystému
U jakékoli aplikace můžou okolnosti způsobit, že se interní Komponenta Service Bus stane nekonzistentní. Když to Service Bus detekuje, shromažďuje data z aplikace, aby se mohla snadněji diagnostikovat, co se stalo. Po shromáždění dat se aplikace restartuje při pokusu o její vrácení do konzistentního stavu. K tomuto procesu dochází poměrně rychle a výsledkem je, že se entita zobrazuje jako nedostupná po dobu několika minut, ale typické časy jsou mnohem kratší.

V těchto případech klientská aplikace generuje výjimku [System. TimeoutException][System.TimeoutException] nebo [MessagingException][MessagingException] . Service Bus obsahuje zmírnění tohoto problému ve formě automatizované logiky opakování klienta. Po vyčerpání doby opakovaného pokusu a odeslání zprávy se vám podíváme na použití jiných uvedených v článku o [manipulaci s výpadky a haváriemi][handling outages and disasters].

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy asynchronního zasílání zpráv v Service Bus, přečtěte si další podrobnosti o [manipulaci s výpadky a haváriemi][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception?view=netcore-3.1
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
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero?view=netcore-3.1
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md