---
title: Azure Service Bus vypršení platnosti zprávy | Microsoft Docs
description: Vypršení platnosti a doba do provozu Azure Service Busch zpráv
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 109ecc671b43365c433a626ff8d9fe55a5a626b5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310301"
---
# <a name="message-expiration-time-to-live"></a>Vypršení platnosti zpráv (hodnota TTL)

Datová část ve zprávě nebo příkaz nebo dotaz, který zpráva předává přijímači, je téměř vždy v souladu s některými formami konečného termínu vypršení platnosti na úrovni aplikace. Po uplynutí tohoto termínu se obsah už nedoručuje nebo požadovaná operace se už nespustí.

Pro vývojová a testovací prostředí, ve kterých jsou fronty a témata často používány v kontextu částečných spuštění aplikací nebo částí aplikace, je také žádoucí, aby byly automaticky uvolněny hromadné zkušební zprávy, aby mohl další testovací běh Spusťte čištění.

Vypršení platnosti pro jednotlivé zprávy lze ovládat nastavením vlastnosti systému [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , která určuje relativní dobu trvání. Vypršení platnosti se bude absolutní okamžitá, když je zpráva zařazená do fronty. V tomto okamžiku převezme vlastnost [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) hodnotu [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). Nastavení TTL (Time-to-Live) u zprostředkovaných zpráv se nevynutilo, když neaktivně nenaslouchá žádní klienti.

Po **ExpiresAtUtc** Instant se zprávy stanou neoprávněnými pro načtení. Vypršení platnosti nemá vliv na zprávy, které jsou momentálně uzamčené pro doručení; Tyto zprávy jsou stále zpracovávány normálně. Pokud zámek vyprší nebo dojde k opuštění zprávy, bude vypršení platnosti platit okamžitě.

I když je zpráva uzamčená, může být aplikace v držbě zprávy, jejíž platnost vypršela. Zda je aplikace ochotna pokračovat se zpracováním nebo se rozhodnete zrušit zprávu pro implementátora.

## <a name="entity-level-expiration"></a>Vypršení platnosti úrovně entity

Všechny zprávy odeslané do fronty nebo tématu podléhají výchozímu vypršení platnosti, které je nastaveno na úrovni entity s vlastností [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) a které lze také nastavit na portálu během vytváření a později upravit. Pro všechny zprávy odesílané do entity, kde [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) není explicitně nastavené, se použije výchozí doba platnosti. Výchozí hodnota vypršení platnosti funguje také jako strop pro hodnotu **TimeToLive** . Zprávy, které mají delší dobu **TimeToLive** , než je výchozí hodnota, se před zařazováním do fronty tiše upraví na hodnotu **defaultMessageTimeToLive** .

> [!NOTE]
> Výchozí hodnota [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) pro zprostředkované zprávy je [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) , pokud není uvedeno jinak.
>
> U entit zasílání zpráv (fronty a témata) je výchozí doba vypršení platnosti také [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) pro Service Bus úrovně Standard a Premium.  Pro úroveň Basic je výchozím časem vypršení platnosti 14 dní.

Zprávy s vypršenou platností můžete volitelně přesunout do [fronty](service-bus-dead-letter-queues.md) nedoručených zpráv nastavením vlastnosti [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) nebo zaškrtnutím příslušného pole na portálu. Pokud je možnost ponecháno zakázaná, zprávy s vypršenou platností se vynechává. Zprávy s vypršenou platností přesunuté do fronty nedoručených zpráv je možné odlišit od jiných nedoručených zpráv vyhodnocením vlastnosti [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) , kterou zprostředkovatel ukládá v části vlastnosti uživatele. v tomto případě je hodnota [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) .

Ve výše uvedeném případě, kdy je zpráva chráněná před vypršením platnosti, a pokud je příznak nastaven na entitě, zpráva se přesune do fronty nedoručených zpráv, protože zámek je zrušený nebo vyprší jeho platnost. Není však přesunuta, je-li zpráva úspěšně vyrovnána, což pak předpokládá, že aplikace ji úspěšně zpracovala, a to i v případě nominálního vypršení platnosti.

Kombinací [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) a automatického (a transakčního) nedoručených zpráv o vypršení platnosti je cenný nástroj pro zajištění důvěry v tom, jestli je pro zpracování jako konečný termín načtena úloha poskytnutá obslužné rutině nebo skupině obslužných rutin pod konečným termínem. je dosaženo.

Představte si například web, který potřebuje spolehlivě spouštět úlohy v back-endu s omezeným škálováním a občas se v nich vyskytují špičky provozu nebo se má v případě dostupnosti tohoto back-endu izolované. V běžném případě obslužná rutina na straně serveru pro odeslaná uživatelská data předává informace do fronty a následně obdrží odpověď potvrzující úspěšné zpracování transakce do fronty odpovědí. Pokud existuje špička provozu a obslužná rutina back-endu nemůže zpracovat své nevyřízené položky v čase, úlohy s vypršenou platností se vrátí do fronty nedoručených zpráv. Interaktivní uživatel může obdržet oznámení o tom, že požadovaná operace bude trvat trochu déle než obvykle, a požadavek je pak možné umístit do jiné fronty pro cestu zpracování, kde je konečný výsledek zpracování odeslán uživateli e-mailem. 


## <a name="temporary-entities"></a>Dočasné entity

Service Bus fronty, témata a odběry je možné vytvořit jako dočasné entity, které se automaticky odeberou, když se v zadaném časovém období nepoužijí.
 
Automatické čištění je užitečné ve scénářích vývoje a testování, ve kterých se entity vytvářejí dynamicky a nejsou vyčištěny po použití, kvůli nějakému přerušení testu nebo běhu ladění. Je také užitečné, když aplikace vytvoří dynamické entity, jako je například fronta odpovědí, pro příjem odpovědí zpět do procesu webového serveru nebo do jiného poměrně krátkodobého nenáročného objektu, kde je obtížné je spolehlivě spolehlivě vyčistit, když objekt instance zmizí.

Tato funkce je povolená pomocí vlastnosti [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) . Tato vlastnost je nastavená na dobu, po kterou musí být entita nečinná (nepoužívá se), než se automaticky odstraní. Minimální hodnota této vlastnosti je 5.
 
Vlastnost **autoDeleteOnIdle** musí být nastavena prostřednictvím operace Azure Resource Manager nebo prostřednictvím [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API klienta .NET Framework. Nemůžete ho nastavit na portálu.

## <a name="idleness"></a>Nečinnost

Tady je postup, který se považuje za nečinnost entit (fronty, témata a odběry):

- Fronty
    - Žádná odeslání  
    - Žádná přijetí  
    - Ve frontě nejsou žádné aktualizace.  
    - Žádné naplánované zprávy  
    - Bez procházení/prohlížení 
- Témata  
    - Žádná odeslání  
    - Žádné aktualizace tématu  
    - Žádné naplánované zprávy 
- Předplatná
    - Žádná přijetí  
    - Žádné aktualizace předplatného  
    - K předplatnému se nepřidala žádná nová pravidla.  
    - Bez procházení/prohlížení  
 


## <a name="next-steps"></a>Další postup

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
