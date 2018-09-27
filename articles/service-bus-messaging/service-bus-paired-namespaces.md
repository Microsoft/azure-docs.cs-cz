---
title: Spárované obory názvů služby Azure Service Bus | Dokumentace Microsoftu
description: Podrobnosti implementace spárovaného oboru názvů a nákladů
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: spelluru
ms.openlocfilehash: ac663cc382fcacd4960843c25aa6c95191210116
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395198"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Spárované podrobnosti implementace oboru názvů a důsledky nákladů

[PairNamespaceAsync] [ PairNamespaceAsync] metodu, pomocí [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instance, provádí viditelné úlohy za vás. Vzhledem k tomu, že jsou úspory nákladů při použití funkce, je užitečné k pochopení těchto úloh tak, že očekávané chování, když k ní dojde. Rozhraní API se věnuje toto automatické chování za vás:

* Vytvoření nevyřízené položky fronty.
* Vytváření [MessageSender] [ MessageSender] objektu, která komunikuje front nebo témat.
* Jakmile entity pro zasílání zpráv není k dispozici, odešle příkaz ping zprávy k entitě za účelem zjištění, že dané entity opět k dispozici.
* Můžete také vytvoří sadu "čerpadel zprávy", který přesunout zprávy z fronty nevyřízených položek do primární fronty.
* Koordinuje uzavírací/chybující primárních a sekundárních [MessagingFactory] [ MessagingFactory] instancí.

Na vysoké úrovni, tato funkce funguje takto: primární entity je v pořádku, dojde k bez změny chování. Když [FailoverInterval] [ FailoverInterval] uplynutí doby trvání a ne úspěšné primární entity vidí pošle za nepřechodných [MessagingException] [ MessagingException] nebo [TimeoutException][TimeoutException], očekávejte toto chování:

1. Odešlete operace na primární entity jsou zakázané a systém odešle příkaz ping primární entity dokud příkazy ping pro zjištění může úspěšně doručit.
2. Je vybrána náhodná nevyřízených položek fronty.
3. [BrokeredMessage] [ BrokeredMessage] objekty jsou směrovány do fronty zvolené nevyřízených položek.
4. Pokud selže operace odeslání do zvolené nevyřízených položek fronty této fronty se načítají z otočení a je vybrán novou frontu. Na všech uživatelů [MessagingFactory] [ MessagingFactory] instance další chyby.

Na následujících obrázcích znázornění sekvence. Nejprve odesílatel odešle zprávy.

![Spárované obory názvů][0]

Při Chyba při odesílání do primární fronty začne odesílatel odesílání zpráv do fronty náhodně vybrané nevyřízených položek. Současně spustí úlohu ping.

![Spárované obory názvů][1]

V tomto okamžiku zprávy jsou nadále v sekundární fronty a nebyly dodány do primární fronty. Jakmile se primární fronta je opět v pořádku, by měl běžet Trativod alespoň jeden proces. Trativod doručuje zprávy ze všech různých front nevyřízených položek do správné cíl entit (fronty a témata).

![Spárované obory názvů][2]

Zbývající část tohoto tématu popisuje konkrétní podrobnosti o fungování těchto částí.

## <a name="creation-of-backlog-queues"></a>Vytvoření nevyřízené položky fronty
[SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] předán objekt [PairNamespaceAsync] [ PairNamespaceAsync] označuje počet – metoda Nevyřízené položky fronty, kterou chcete použít. Každá fronta nevyřízených položek se pak vytvoří s těmito vlastnostmi explicitně nastavit (všechny ostatní hodnoty jsou nastaveny na [QueueDescription] [ QueueDescription] výchozí nastavení):

| Cesta | [primární obor názvů] / x-servicebus přenos [index] kde [index] je hodnoty v [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int. Hodnota MaxValue |
| DefaultMessageTimeToLive |Hodnotu TimeSpan.MaxValue |
| AutoDeleteOnIdle |Hodnotu TimeSpan.MaxValue |
| Trvání uzamčení |1 minuta |
| EnableDeadLetteringOnMessageExpiration |true (pravda) |
| EnableBatchedOperations |true (pravda) |

Například pro obor názvů vytvořit první frontu nevyřízených položek **contoso** jmenuje `contoso/x-servicebus-transfer/0`.

Při vytváření fronty, kód nejprve zkontroluje, zda existuje takový fronty. Pokud fronta ještě neexistuje, vytvoří se do fronty. Kód není vyčistit "navíc" nevyřízených položek fronty. Konkrétně Pokud aplikace s primárním oborem názvů **contoso** požadavků pět nevyřízených položek fronty, ale nevyřízených položek fronty s cestou `contoso/x-servicebus-transfer/7` existuje, navíc nevyřízených položek fronty stále existuje, ale není použit. Systém explicitně povoluje dodatečné nevyřízených položek fronty existovat, která nebyla použita. Jako vlastník obor názvů zodpovídáte za čištění žádné nepoužité/nežádoucí nevyřízených položek fronty. Důvod pro toto rozhodnutí je, že Service Bus nemůže vědět, jaké účely existovat pro všechny fronty ve vašem oboru názvů. Kromě toho pokud fronty s daným názvem existuje, ale ne předpokládané [QueueDescription][QueueDescription], pak vaše důvody jsou vlastní Změna výchozího chování. Žádné záruky jsou vytvořené pro úpravy nevyřízených položek fronty ve vašem kódu. Ujistěte se, že důkladně otestovat provedené změny.

## <a name="custom-messagesender"></a>Vlastní MessageSender
Při odesílání, všechny zprávy projít interní [MessageSender] [ MessageSender] objekt, který se normálně chová, když všechno, co funguje a přesměruje do nevyřízených položek fronty při věci "break." Při přijetí nepřechodných selhání spuštění časovače. Po [TimeSpan] [ TimeSpan] skládající se z období [FailoverInterval] [ FailoverInterval] hodnotu vlastnosti, během které jsou odeslány žádné úspěšné zprávy, zabývá převzetí služeb při selhání. V tuto chvíli dojít následující akce pro každou entitu:

* Příkaz ping úkol spustí každých [PingPrimaryInterval] [ PingPrimaryInterval] ke kontrole, jestli je k dispozici entita. Po úspěšném tato úloha veškerý kód klienta, která používá entitu okamžitě spustí odesílání nových zpráv na primární obor názvů.
* Výsledkem bude budoucí požadavky na odeslání do stejné entity z jiných uživatelů [BrokeredMessage] [ BrokeredMessage] odesílané do upravit tak, aby se nacházejí ve frontě nevyřízených položek. Úpravy odebere z některé vlastnosti [BrokeredMessage] [ BrokeredMessage] objekt a uloží je jinde. Následující vlastnosti jsou vymazána a přidány pod nový alias, že Service Bus a sady SDK ke zpracování zpráv, které jsou rovnoměrně:

| Starý název vlastnosti | Nový název vlastnosti |
| --- | --- |
| ID relace |x-ms-ID relace |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

Původní cílovou cestu je také uložena v něm jako vlastnost s názvem x-ms-path. Toto řešení umožňuje zprávy pro entity koexistovat ve frontě jednu sadu nevyřízených položek. Vlastnosti jsou překládány zpět Trativod.

Vlastní [MessageSender] [ MessageSender] objekt může dojít k potížím při zprávy přiblíží maximální 256 KB a provozuje převzetí služeb při selhání. Vlastní [MessageSender] [ MessageSender] ukládá zprávy pro všechny fronty a témata společně ve frontách nevyřízených položek. Tento objekt používá zprávy z mnoha primárek společně v rámci nevyřízené položky fronty. Pro zpracování zátěže mezi počtu klientů, které mezi sebou není známo, sady SDK náhodně vybere jedna fronta nevyřízených položek pro každý [QueueClient] [ QueueClient] nebo [TopicClient] [ TopicClient] vytvoříte v kódu.

## <a name="pings"></a>Příkazy ping pro zjištění
Příkaz ping zpráva je prázdná [BrokeredMessage] [ BrokeredMessage] s jeho [ContentType] [ ContentType] nastavenou na application/vnd.ms-servicebus-ping a [TimeToLive] [ TimeToLive] hodnota 1 sekundu. Toto pingnutí charakteristiky jeden speciální ve službě Service Bus: server nikdy poskytuje odeslání příkazu ping, pokud jakýkoli volající požaduje [BrokeredMessage][BrokeredMessage]. Proto nikdy máte se naučíte přijímat a ignorovat tyto zprávy. Každá entita (jedinečný fronty nebo tématu) za [MessagingFactory] [ MessagingFactory] instance pro každý klient bude příkazu ping posuzované do nedostupný. Ve výchozím nastavení k tomu dochází jednou za minutu. Příkaz ping zprávy jsou považovány za běžné zprávy služby Service Bus a může vést k poplatky za šířku pásma a zprávy. Jakmile klienti zjišťovat, že systém je k dispozici, zastavte zprávy.

## <a name="the-syphon"></a>Trativod
Alespoň jeden spustitelný program v aplikaci by měl aktivně běžet Trativod. Trativod provádí dlouho dotazování přijímat, které trvá 15 minut. Když máte 10 nevyřízených položek fronty jsou k dispozici všechny entity, aplikace, který je hostitelem Trativod volá operace obdržení 40krát za hodinu, 960 časy za den a čas 28800 po uplynutí 30 dnů. Když Trativod aktivně přesouvá zprávy z nevyřízených položek do primární fronty, každá zpráva vyskytne následující poplatky (standardní velikost a šířky pásma platí ve všech fázích):

1. Odeslat do nevyřízených položek.
2. Příjem z nevyřízených položek.
3. Odeslat na primární.
4. Zobrazit z primárního serveru.

## <a name="closefault-behavior"></a>Zavřít nebo s chybou chování
V rámci aplikace, který je hostitelem Trativod, jednou primární nebo sekundární [MessagingFactory] [ MessagingFactory] závady nebo bude uzavřeno bez jeho partnerů, také chybou nebo uzavřen a Trativod zjistí tento stav Trativod funguje. Pokud se druhý [MessagingFactory] [ MessagingFactory] není uzavřený do 5 sekund Trativod chyb stále otevřít [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Další postup
Zobrazit [asynchronní zasílání zpráv schémata a vysoká dostupnost] [ Asynchronous messaging patterns and high availability] podrobnou diskuzi o asynchronní zasílání zpráv Service Bus. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
