---
title: Obory názvů spárovat Azure Service Bus | Microsoft Docs
description: Podrobnosti implementace spárované obor názvů a náklady
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159537"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Spárovat podrobnosti implementace obor názvů a náklady dopad

[PairNamespaceAsync] [ PairNamespaceAsync] metoda, použití [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instance, provádí viditelné úlohy vaším jménem. Vzhledem k tomu, že jsou náklady aspekty při použití funkce, je vhodné pochopit tyto úlohy tak, aby očekávané chování při Odehrává se. Rozhraní API zapojí následující automatické chování vaším jménem:

* Vytvoření nevyřízených položek fronty.
* Vytvoření [MessageSender] [ MessageSender] objekt, který komunikuje se fronty nebo témata.
* Při zasílání zpráv entity stane nedostupným, odešle příkaz ping zprávy do entity ve snaze rozpoznat, kdy se dané entity opět k dispozici.
* Volitelně vytváří sadu "čerpadel zpráva", přesunovat zprávy z fronty nevyřízených položek do primární front.
* Koordinuje ukončovací nebo chybující primárních a sekundárních [MessagingFactory] [ MessagingFactory] instance.

Na vysoké úrovni, tato funkce funguje takto: když primární entita je v pořádku, dojít k žádným změnám chování. Když [FailoverInterval] [ FailoverInterval] po uplynutí předem doba trvání a ne úspěšné primární entity vidí pošle za jiný přechodná [MessagingException] [ MessagingException] nebo [TimeoutException][TimeoutException], očekávejte toto chování:

1. Odešlete na primární entity operací jsou zakázány a systém pomocí příkazu ping primární entity dokud příkazy ping mohou být zajišťovány úspěšně.
2. Je vybrán náhodných nevyřízené položky fronty.
3. [BrokeredMessage] [ BrokeredMessage] objekty jsou směrovány do fronty zvolené nevyřízených položek.
4. Pokud se nezdaří operaci odeslání do zvolené nevyřízené položky fronty, této fronty pocházejí z otáčení a je vybraná novou frontu. Na všech uživatelů [MessagingFactory] [ MessagingFactory] instance Další informace o selhání.

Následující obrázky zobrazit v ní sekvenci. Nejprve odesílatel odešle zprávy.

![Spárované obory názvů][0]

Při selhání k odeslání do primární fronty začne odesílatel odesílání zpráv do fronty náhodně vybrané nevyřízených položek. Současně spustí úlohu příkazu ping.

![Spárované obory názvů][1]

V tomto okamžiku zprávy jsou stále v sekundární fronty a nebyly dodány do primární fronty. Jakmile primární fronta je v pořádku znovu, by měl běžet Trativod alespoň jeden proces. Trativod přináší zprávy ze všech různých front nevyřízených položek správné cílové entity (fronty a témata).

![Spárované obory názvů][2]

Zbývající část tohoto tématu popisuje konkrétní podrobnosti o fungování tyto údaje.

## <a name="creation-of-backlog-queues"></a>Vytvoření nevyřízených položek fronty
[SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] byl předán objekt [PairNamespaceAsync] [ PairNamespaceAsync] metoda označuje počet nevyřízených položek fronty, kterou chcete použít. Každou frontu nevyřízených položek je pak vytvořen s následujícími vlastnostmi explicitně nastavit (všechny ostatní hodnoty jsou nastaveny na [QueueDescription] [ QueueDescription] výchozí nastavení):

| Cesta | [primární obor názvů] / x-servicebus přenos / [index] kde [index] je hodnota [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| maxDeliveryCount |int. MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue, což |
| AutoDeleteOnIdle |TimeSpan.MaxValue, což |
| Trvání uzamčení |1 minuta |
| EnableDeadLetteringOnMessageExpiration |true (pravda) |
| EnableBatchedOperations |true (pravda) |

Například vytvořit první nevyřízených položek fronty pro obor názvů **contoso** jmenuje `contoso/x-servicebus-transfer/0`.

Při vytváření front, kód nejprve zkontroluje, zda existuje takový fronty. Pokud fronta neexistuje, je vytvářena fronta. Kód nemá vyčištění "výběr" nevyřízené položky fronty. Konkrétně Pokud aplikace s primární obor názvů **contoso** požadavky pět nevyřízené položky fronty, ale nevyřízené položky fronty s cestou `contoso/x-servicebus-transfer/7` existuje, této navíc nevyřízené položky fronty stále existuje, ale nepoužívá. Systém umožňuje explicitně navíc nevyřízené položky fronty existovat, které nebyla použita. Jako vlastník obor názvů je zodpovědná za vyčištění žádné nepoužité nebo nežádoucí nevyřízené položky fronty. Z důvodu pro toto rozhodnutí je, že Service Bus nemůže vědět, jaké účely existovat pro všechny fronty, které jsou v oboru názvů. Kromě toho pokud existuje s daným názvem fronty, ale nesplňuje předpokládané [QueueDescription][QueueDescription], pak jsou vaše důvodů vlastní pro změnu výchozího chování. Žádné záruky probíhají změny nevyřízené položky fronty podle vašeho kódu. Ujistěte se, že jste důkladně otestovat změny.

## <a name="custom-messagesender"></a>Vlastní MessageSender
Při odesílání, všechny zprávy projít interní [MessageSender] [ MessageSender] objekt, který se chovat normálně když všechno funguje a přesměruje na nevyřízené položky fronty při věcí "break." Po přijetí selhání bez přechodná, spustí časovač. Po [časový interval] [ TimeSpan] období skládající se z [FailoverInterval] [ FailoverInterval] provozuje během které se odesílají žádné úspěšné zprávy, hodnota vlastnosti převzetí služeb při selhání. V tomto okamžiku následujících akcí dojít u každé entity:

* Spustí úlohu ping každých [PingPrimaryInterval] [ PingPrimaryInterval] ke kontrole, pokud tato entita je k dispozici. Jakmile tato úloha úspěšná, všechny kód klienta, který používá entity okamžitě spustí, odesílání nových zpráv do primární oboru názvů.
* Bude mít za následek budoucí požadavky na odeslání této stejné entity z jiní odesílatelé [BrokeredMessage] [ BrokeredMessage] odesílána upravit tak, aby nacházejí ve frontě nevyřízených položek. Úpravy odebere některé vlastnosti z [BrokeredMessage] [ BrokeredMessage] objektu a ukládá je jinde. Následující vlastnosti jsou vymazána a přidají pod nový alias, povolení služby Service Bus a sady SDK ke zpracování zpráv jednotně:

| Původní název vlastnosti | Nový název vlastnosti |
| --- | --- |
| ID relace |x-ms-ID relace |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

Původní cílovou cestu jsou také uložená v zprávu jako vlastnost s názvem x-ms-path. Tento návrh umožňuje zprávy pro mnoho entity k společně existovat v jednom nevyřízené položky fronty. Vlastnosti jsou přeložen zpět Trativod.

Vlastní [MessageSender] [ MessageSender] objekt může dojít k potížím při zprávy přístupu limit 256 KB a využívána převzetí služeb při selhání. Vlastní [MessageSender] [ MessageSender] objekt ukládá zprávy pro všechny fronty a témata společně v nevyřízené položky fronty. Tento objekt zkombinuje zprávy z mnoha základní barvy společně v rámci nevyřízené položky fronty. Zpracovat Vyrovnávání zatížení mezi mnoho klientů, které mezi sebou neznáte, sadu SDK náhodně vybere jeden nevyřízených položek fronty pro každou [QueueClient] [ QueueClient] nebo [TopicClient] [ TopicClient] vytvoříte v kódu.

## <a name="pings"></a>Příkazy ping
Zprávu ping je prázdná [BrokeredMessage] [ BrokeredMessage] s jeho [ContentType] [ ContentType] vlastnost nastavena na hodnotu aplikace/vnd.ms-servicebus-ping a [TimeToLive] [ TimeToLive] hodnota 1 sekunda. Tento příkaz ping má jeden speciální vlastnosti v Service Bus: serveru nikdy přináší příkazu ping, když si vyžádá všechny volající [BrokeredMessage][BrokeredMessage]. Proto není nutné zjistěte, jak přijmout a tyto zprávy ignorovat. Každá entita (jedinečný fronta nebo téma) za [MessagingFactory] [ MessagingFactory] instance pro každý klient bude příkazu ping zkontrolujte dosažitelnost při jsou považovány za nedostupný. Ve výchozím nastavení k tomu dojde jednou za minutu. Zprávy ping se považují za regulární zpráv Service Bus a může mít za následek poplatky za zprávy a šířky pásma. Jakmile klienti zjišťovat, zda je k dispozici v systému, zastavte zprávy.

## <a name="the-syphon"></a>Trativod
Alespoň jeden spustitelný program v aplikaci by měl být aktivně běžícími Trativod. Trativod provádí s dlouhým přijímat dotazování, které trvá po 15 minut. Když jsou k dispozici všechny entity a máte 10 nevyřízené položky fronty, aplikace, který je hostitelem Trativod volání operace příjmu 40 časy za hodinu, 960 časy denně a časy 28 800 bitů za 30 dní. Při Trativod je aktivně přesunu zprávy z nevyřízené položky do primární fronty, každá zpráva vyskytne následující poplatky (standardní poplatky za velikost zprávy a šířky pásma použít ve všech fázích):

1. Nevyřízené položky poslat.
2. Nevyřízené položky dostávat.
3. Odeslat na primární.
4. Přijímat z primární.

## <a name="closefault-behavior"></a>Zavřít nebo selhání chování
V rámci aplikace, který je hostitelem Trativod, jednou primární nebo sekundární [MessagingFactory] [ MessagingFactory] chyb nebo je uzavřena bez svým partnerem také se s chybou nebo, zavře a Trativod zjistí tento stav Trativod funguje. Pokud dalších [MessagingFactory] [ MessagingFactory] není uzavřený během 5 sekund Trativod závady stále otevřete [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Další postup
V tématu [asynchronní vzory a vysoká dostupnost pro zasílání zpráv] [ Asynchronous messaging patterns and high availability] pro podrobnou diskuzi o asynchronní zasílání zpráv Service Bus. 

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
