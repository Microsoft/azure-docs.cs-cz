---
title: Počet zpráv Azure Service Bus | Microsoft Docs
description: Načtěte počet zpráv Azure Service Bus.
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
ms.openlocfilehash: e6524fe056ee2a1d81c9cccf257008b2369352b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197727"
---
# <a name="message-counters"></a>Čítače zpráv

Počet zpráv držených ve frontách a odběry s použitím Azure Resource Manager a Service Bus můžete načíst [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API v rozhraní .NET Framework SDK.

Pomocí prostředí PowerShell můžete získat počet následujícím způsobem:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Podrobnosti o počtu zpráv

Znalost počet active zpráv je užitečný při určování, zda vytvoří frontu nevyřízených položek, který vyžaduje více prostředků pro zpracování, než co aktuálně nasazená. Následující podrobnosti čítače jsou k dispozici v [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) třídy:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): zpráv ve frontě nebo předplatné, které jsou v aktivním stavu a připravena k doručení.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): zprávy do fronty nedoručených zpráv.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): zprávy v naplánované stavu.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): zprávy, které se nezdařilo přenos do jiného fronta nebo téma a byla přesunuta do fronty nedoručených zpráv přenosu.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): zprávy čekající na vyřízení přenos do jiného fronta nebo téma.

Pokud aplikace chce škálovat prostředky na základě délky fronty, ho měli tak učinit s velmi měřená rychle. Získání čítače zpráv je náročná operace uvnitř zprostředkovatele zpráv a její provedení často přímo a nepříznivě ovlivňuje výkon entity.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)