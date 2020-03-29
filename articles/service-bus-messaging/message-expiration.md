---
title: Azure Service Bus – vypršení platnosti zprávy
description: Tento článek vysvětluje vypršení platnosti a čas pro spuštění zpráv Azure Service Bus. Po uplynutí této lhůty se zpráva již nedoručuje.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e86c92fa1cfb13929d5617502224f479709efdd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756330"
---
# <a name="message-expiration-time-to-live"></a>Vypršení platnosti zpráv (hodnota TTL)

Datová část ve zprávě nebo příkaz nebo dotaz, který zpráva předává příjemci, téměř vždy podléhá nějaké formě konečného termínu vypršení platnosti na úrovni aplikace. Po uplynutí této lhůty již není obsah doručen nebo požadovaná operace již není provedena.

Pro vývojová a testovací prostředí, ve kterých jsou fronty a témata často používány v kontextu částečných spuštění aplikací nebo částí aplikace, je také žádoucí, aby se automaticky shromažďovaly nevypozované testovací zprávy, aby mohl být další testovací běh začít čistit.

Vypršení platnosti pro každou jednotlivou zprávu lze řídit nastavením vlastnosti systému [TimeToLive,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) která určuje relativní dobu trvání. Vypršení platnosti se stane absolutní okamžik, když je zpráva zařazena do fronty do entity. V té době [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) vlastnost přebírá hodnotu [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**).](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) Nastavení time-to-live (TTL) na zprostředkované zprávy není vynuceno, pokud nejsou žádní klienti aktivně naslouchání.

Po **vypršení expiresAtUtc** instant, zprávy stanou nezpůsobilé pro načtení. Vypršení platnosti nemá vliv na zprávy, které jsou aktuálně uzamčeny pro doručení; tyto zprávy jsou stále zpracovávány normálně. Pokud vyprší platnost zámku nebo je zpráva opuštěna, vypršení platnosti se projeví okamžitě.

Zatímco zpráva je pod zámkem, aplikace může být v držení zprávy, která vypršela. Zda je aplikace ochotna pokračovat ve zpracování nebo se rozhodne zprávu opustit, je na implementátorovi.

## <a name="entity-level-expiration"></a>Vypršení platnosti na úrovni entity

Všechny zprávy odeslané do fronty nebo tématu podléhají výchozí vypršení platnosti, která je nastavena na úrovni entity s [výchozíVlastnostMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) a které lze také nastavit na portálu během vytváření a upravit později. Výchozí vypršení platnosti se používá pro všechny zprávy odeslané entitě, kde [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) není explicitně nastavena. Výchozí vypršení platnosti také funguje jako strop pro hodnotu **TimeToLive.** Zprávy, které mají delší expiraci **TimeToLive** než výchozí hodnota jsou tiše upraveny na výchozí Hodnotu **MessageTimeToLive** před zařazením do fronty.

> [!NOTE]
> Výchozí hodnota [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) pro zprostředkovanou zprávu je [TimeSpan.Max,](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) pokud není zadán jinak.
>
> Pro zasílání zpráv entity (fronty a témata), výchozí čas vypršení platnosti je také [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) pro service bus standardní a úrovně premium.  Pro základní úroveň je výchozí doba vypršení platnosti 14 dní.

Zprávy s ukončenou platností lze volitelně přesunout do [fronty nedoručených zpráv](service-bus-dead-letter-queues.md) nastavením vlastnosti [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) nebo zaškrtnutím příslušného políčka na portálu. Pokud je tato možnost zakázána, jsou zprávy s prošlou platností vynechány. Zprávy s prošlou platností přesunuté do fronty nedoručených zpráv lze odlišit od jiných nedoručených zpráv vyhodnocením [vlastnosti DeadletterReason,](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) kterou zprostředkovatel ukládá v části vlastností uživatele. hodnota je [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) v tomto případě.

Ve výše uvedeném případě, ve kterém je zpráva chráněna před vypršením platnosti, zatímco pod zámkem a pokud je nastaven příznak na entitě, zpráva je přesunuta do fronty nedoručených zpráv jako zámek je opuštěnnebo vyprší. Však není přesunuta, pokud je zpráva úspěšně vyrovnána, což pak předpokládá, že aplikace úspěšně zpracována, navzdory nominální vypršení platnosti.

Kombinace [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) a automatické (a transakční) dead-lettering po vypršení platnosti jsou cenným nástrojem pro stanovení důvěry v tom, zda je úloha poskytnutá obslužné rutině nebo skupině obslužných rutin v termínu načtena ke zpracování po dosažení konečného termínu.

Zvažte například web, který potřebuje spolehlivě provádět úlohy v back-endu omezeném škálovacím měřítkem a který občas dochází k špičkám provozu nebo chce být izolován proti epizodám dostupnosti tohoto back-endu. V běžném případě obslužná rutina na straně serveru pro odeslaná uživatelská data odešle informace do fronty a následně obdrží odpověď potvrzující úspěšné zpracování transakce do fronty odpovědí. Pokud je špička přenosu a obslužná rutina back-endu nemůže zpracovat své nevyřízené položky v čase, jsou vráceny úlohy s ukončenou platností ve frontě nedoručených zpráv. Interaktivní uživatel může být upozorněn, že požadovaná operace bude trvat o něco déle než obvykle a požadavek pak může být umístěn na jinou frontu pro cestu zpracování, kde je konečný výsledek zpracování odeslán uživateli e-mailem. 


## <a name="temporary-entities"></a>Dočasné subjekty

Fronty, témata a odběry služby Service Bus lze vytvořit jako dočasné entity, které jsou automaticky odebrány, pokud nebyly použity po určitou dobu.
 
Automatické vyčištění je užitečné ve scénářích vývoje a testování, ve kterých jsou entity vytvářeny dynamicky a nejsou po použití vyčištěny z důvodu přerušení testu nebo ladění. Je také užitečné, když aplikace vytvoří dynamické entity, jako je například fronta odpovědí, pro příjem odpovědí zpět do procesu webového serveru nebo do jiného objektu s relativně krátkou životností, kde je obtížné tyto entity spolehlivě vyčistit, když objekt instance zmizí.

Tato funkce je povolena pomocí vlastnosti [autoDeleteOnIdle.](/azure/templates/microsoft.servicebus/namespaces/queues) Tato vlastnost je nastavena na dobu, po kterou musí být entita nečinná (nevyužitá), než bude automaticky odstraněna. Minimální hodnota pro tuto vlastnost je 5.
 
Vlastnost **autoDeleteOnIdle** musí být nastavena prostřednictvím operace Azure Resource Manager nebo prostřednictvím rozhraní API client [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) klienta .NET Framework. Nemůžete to nastavit na portálu.

## <a name="idleness"></a>Nečinnosti

Co považovalo za nečinnost entit (fronty, témata a odběry):

- Fronty
    - Žádné odeslání  
    - Žádné příjemy  
    - Žádné aktualizace fronty  
    - Žádné naplánované zprávy  
    - Žádné procházení/náhled 
- Témata  
    - Žádné odeslání  
    - Žádné aktualizace tématu  
    - Žádné naplánované zprávy 
- Předplatná
    - Žádné příjemy  
    - Žádné aktualizace předplatného  
    - Do předplatného nebyla přidána žádná nová pravidla.  
    - Žádné procházení/náhled  
 


## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
