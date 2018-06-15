---
title: Vypršení platnosti zprávy Azure Service Bus | Microsoft Docs
description: Vypršení platnosti a jeho hodnota time to live zpráv Azure Service Bus
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196764"
---
# <a name="message-expiration-time-to-live"></a>Vypršení platnosti zprávy (Time to Live)

Datová část uvnitř zprávu, nebo příkaz nebo dotaz, který zprávu přenese tak k příjemce, téměř vždy podléhá určitou formu termínu vypršení platnosti úrovni aplikace. Po termínu už daný obsah doručuje nebo je již provést požadovanou operaci.

Pro vývoj a testovací prostředí, ve kterých front a témat se často používají v rámci částečné spustí aplikací nebo částí aplikace je také žádoucí, aby mají být automaticky shromažďují tak, aby spustit další test paměti může izolované testovací zprávy spuštění čištění.

Vypršení platnosti pro jakékoli jednotlivé zprávy se dá nastavit podle nastavení [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) vlastnosti systému, která určuje relativní doba trvání. Doba vypršení platnosti změní absolutní rychlých, pokud zpráva je zařazených do fronty s entitou. V ten moment se [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) vlastnost přebírá hodnotu [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Posledních **ExpiresAtUtc** rychlých, zprávy způsobilé pro načtení. Doba vypršení platnosti nemá vliv na zprávy, které jsou aktuálně uzamčena pro doručení. Tyto zprávy jsou stále zpracovává normálně. Pokud vyprší platnost zámek nebo opuštění zprávy, doba vypršení platnosti se projeví okamžitě.

Při zprávy v rámci zámku aplikace může být u sebe zprávy s vypršenou platností. Jestli je ochotná pokračujte s zpracování nebo zvolí ukončil zpráva aplikace závisí implementátor.

## <a name="entity-level-expiration"></a>Vypršení platnosti úrovni entit

Všechny zprávy odeslané do fronty nebo tématu se vztahují výchozí vypršení platnosti, který je nastavený na entity úrovně s [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) vlastnost a který můžete také nastavit na portálu během vytváření a upravena později. Výchozí doba vypršení platnosti se používá pro všechny zprávy odeslané do entity kde [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) není explicitně nastavena. Výchozí doba vypršení platnosti taky funguje jako mezní hodnoty pro **TimeToLive** hodnotu. Zprávy, které mají delší **TimeToLive** vypršení platnosti, než je výchozí hodnota bezobslužně upraveny tak, aby **defaultMessageTimeToLive** hodnotu teprve pak ji bude zařazených do fronty.

Zprávy s vypršenou platností můžete volitelně přesunout do [frontu nedoručených zpráv](service-bus-dead-letter-queues.md) nastavením [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) vlastnost nebo zaškrtnutím příslušného políčka na portálu. Pokud je možnost zakázáno, jsou zprávy s vypršenou platností vyřadit. Zprávy s vypršenou platností přesunout do fronty nedoručených zpráv dají rozlišovat z dalších zpráv lettered zprávy vyhodnocením [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) vlastnost, která má zprostředkovatel ukládá v části Vlastnosti uživatele; hodnota je [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) v tomto případě.

V případě zmíněnými, ve kterém je zpráva chráněný před vypršení platnosti při pod zámku a pokud je nastavený příznak u entity zpráva bude přesunuta do fronty nedoručených zpráv jako lock opuštění nebo vypršení platnosti. Však není přesunut, pokud zpráva je úspěšně vyrovnány, která předpokládá, že aplikace byla úspěšně zpracována, přestože nominální vypršení platnosti.

Kombinace [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) a automatické (a transakcí) zpráv lettering na vypršení platnosti jsou cenným nástrojem ke zřízení důvěru v tom, jestli načtena úlohu na obslužnou rutinu nebo skupinu obslužných rutin pod konečného termínu pro je dosaženo zpracování jako konečného termínu.

Představte si třeba webové stránky, které je spolehlivě spuštění úlohy na back-end omezené škálování a která příležitostně prostředí provoz špičky nebo chce být izolovány proti díly dostupnosti tohoto back-end. V případě regulární serverové obslužná rutina pro odeslaná data doručí informace do fronty a následně obdrží odpověď potvrzení úspěšné zpracování transakce do fronty odpovědi. Pokud je provoz Špička a back-end obslužná rutina nemůže zpracovat jeho nevyřízených položek v čase, vrátí se vypršela platnost úlohy na frontu nedoručených zpráv. Požadovaná operace bude trvat déle než obvykle, a požadavek je možné potom zadat na jiné fronty pro zpracování cestu kde výsledek případné zpracování je uživateli zaslán e-mailem, můžete být upozorněni interaktivní uživatel. 

## <a name="temporary-entities"></a>Dočasné entity

Service Bus fronty, témata a odběry se dá vytvořit jako dočasné entity, které se automaticky odeberou, když se pro zadaném časovém období nebyly použity.
 
Automatické čištění je užitečné v vývojová a testovací scénáře, ve kterých entity dynamicky se vytvářejí a nejsou po použití, z důvodu některých přerušení testu nebo spuštění ladění vyčištěna. Je také užitečné, když aplikace vytvoří dynamické entitami, jako je například fronty odpovědi, pro příjem odpovědí zpět do procesu webového serveru nebo do jiného relativně krátkodobou objektu, kde je obtížné spolehlivě vyčištění tyto entity při objektu instance zmizí.

Tato funkce je povolena, pomocí [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) vlastnost, která je nastavena na hodnotu doby trvání, pro kterou má být entity nečinnosti (nepoužívané), než je automaticky odstraněna. Minimální doba je 5 minut.
 
**AutoDeleteOnIdle** prostřednictvím operace Azure Resource Manager nebo prostřednictvím klienta rozhraní .NET Framework, musí být nastavená vlastnost [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API. Nejde ji nastavit pomocí portálu.


## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)