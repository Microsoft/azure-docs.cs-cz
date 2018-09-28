---
title: Vypršení platnosti zpráv Azure Service Bus | Dokumentace Microsoftu
description: Vypršení platnosti a TTL zpráv Azure Service Bus
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: c1c38370ae508dffc6888dd7e94514406b8da327
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405262"
---
# <a name="message-expiration-time-to-live"></a>Vypršení platnosti zpráv (hodnota TTL)

Datová část uvnitř zprávy, nebo příkaz nebo dotaz, která přenáší zprávy pro příjemce, je téměř vždy v souladu s určitou formu lhůta vypršení platnosti úrovni aplikace. Po ukončení už se daný obsah doručuje nebo požadovaná operace je již spuštěn.

Pro vývojová a testovací prostředí, ve kterých front a témat se často používají v souvislosti s částečnou spuštění aplikace nebo součásti aplikace je také vhodné pro izolované testovací zprávy automaticky uvolněn z paměti tak, aby další testovací běh může Vyčištění zahájeno.

Vypršení platnosti pro všechny jednotlivé zprávy se dá nastavit podle nastavení [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) vlastnost systému, který určuje relativní dobu trvání. Vypršení platnosti je absolutní rychlé, pokud je zpráva ve frontě do entity. V tu chvíli [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) vlastnost přebírá hodnotu [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Posledních **ExpiresAtUtc** rychlé, zprávy způsobilé pro načtení. Vypršení platnosti nemá vliv na zprávy, které jsou momentálně uzamčena pro dodávku; Tyto zprávy jsou zpracovány stále normálně. Pokud vyprší platnost zámku, nebo opuštění zprávy, vypršení platnosti se projeví okamžitě.

Zatímco zprávy je pod správou zámku, aplikace může být ve vlastnictví zprávy, kterému vypršela platnost. Určuje, zda je aplikace chce pokračovat se zpracováním nebo zvolí možnost opustit zprávu záleží implementátora.

## <a name="entity-level-expiration"></a>Vypršení platnosti úrovni entity

Všechny zprávy odeslané do fronty nebo tématu se vztahují výchozí vypršení platnosti, který je nastaven na entitu úroveň díky [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) vlastnost a která lze také nastavit na portálu během vytváření a upravit později. Výchozí vypršení platnosti se používá pro všechny zprávy odeslané do entity kde [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) není explicitně nastavena. Vypršení platnosti výchozí slouží taky jako horní mez pro **TimeToLive** hodnotu. Zprávy, které mají delší **TimeToLive** vypršení platnosti, než je výchozí hodnota tiše upraveny tak, aby **defaultMessageTimeToLive** hodnotu před zařazených do fronty.

Zprávy s vypršenou platností můžete volitelně můžete přesunout do [fronty nedoručených zpráv](service-bus-dead-letter-queues.md) nastavením [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) vlastnost nebo zaškrtnutím příslušného políčka na portálu. Pokud je možnost zakázáno, se zahodí zprávy s vypršenou platností. Zprávy s vypršenou platností přesunout do fronty nedoručených zpráv dají rozlišovat od jiných dead lettered zpráv podle hodnocení [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) vlastnost, která ukládá zprostředkovatele v části Vlastnosti uživatele; hodnota je [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) v tomto případě.

V případě výše uvedené, ve kterém je zpráva chráněný před vypršení platnosti při uzamčení a pokud je příznak nastaven na entitu zpráva bude přesunuta do fronty nedoručených zpráv zámek opuštění nebo vypršení platnosti. Však nebyl přesunut, pokud zpráva se úspěšně řeší, což předpokládá, že aplikace byla úspěšně zpracována, přestože nominální vypršení platnosti.

Kombinace [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) a automatické (a transakční) dead-lettering na vypršení platnosti jsou cenným nástrojem ke zřízení důvěru v Určuje, zda je pro načíst úlohu na obslužnou rutinu nebo skupinu obslužných rutin pod konečný termín zpracování v podobě konečného termínu je dosaženo.

Představte si třeba webovou stránku, která je potřeba spolehlivě spouštět úlohy na back-end omezené škálování a které občas prostředí provoz špičky nebo chce být izolována dostupnost epizody tento back-end. V případě regulární obslužné rutiny na straně serveru pro data uživatelů odeslaných vložení informací do fronty a následně obdrží odpověď potvrzení úspěšné zpracování transakce do fronty odpovědí. Pokud je prudký nárůst přenosů a back-endu obslužné rutiny nelze zpracovat své nevyřízené položky v čase, vypršela platnost úlohy, které budou vráceny do fronty nedoručených zpráv. Interaktivní uživatel můžete být upozorněni, že požadovaná operace bude trvat déle než obvykle a žádost pak lze do jiné fronty pro zpracování cesta kde konečné zpracování výsledku je uživateli odeslána e-mailem. 

## <a name="temporary-entities"></a>Dočasné entity

Fronty služby Service Bus, témat a předplatných lze vytvořit jako dočasné entit, které se automaticky odeberou, když nebyly použity na zadanou dobu.
 
Automatické vyčištění je užitečné pro vývojové a testovací scénáře, ve kterých entity dynamicky se vytvářejí a nejsou vyčištění po použití, z důvodu některých přerušení testu nebo spuštění ladění. Je také užitečné, pokud aplikace vytvoří dynamický entitami, jako je například frontu odpovědí pro příjem odpovědí zpět do procesu webového serveru nebo do jiného objektu relativně krátkodobé, kde je obtížné spolehlivě vyčištění těchto entit při objektu instance zmizí.

Aby byla povolená funkce pomocí [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) vlastnost, která je nastavena na dobu trvání, pro kterou má být entity nečinnosti (nepoužívané) předtím, než se automaticky odstraní. Minimální doba trvání je 5 minut.
 
**AutoDeleteOnIdle** prostřednictvím operace Azure Resource Manageru nebo přes klienta rozhraní .NET Framework musí být nastavena vlastnost [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API. Nejde ji nastavit na portálu.


## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)