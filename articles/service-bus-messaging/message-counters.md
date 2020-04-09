---
title: Azure Service Bus – počet zpráv
description: Načtěte počet zpráv uchovávaných ve frontách a předplatných pomocí Azure Resource Manager a Azure Service Bus NamespaceManager API.
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
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891760"
---
# <a name="message-counters"></a>Čítače zpráv

Počet zpráv uchovávaných ve frontách a předplatných můžete načíst pomocí Správce prostředků Azure a rozhraní API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) v sdk rozhraní .NET Framework.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

S Prostředím PowerShell můžete získat počet takto:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Podrobnosti o počtu zpráv

Znalost počtu aktivních zpráv je užitečná při určování, zda fronta vytvoří nevyřízené položky, které vyžaduje více prostředků ke zpracování, než co bylo aktuálně nasazeno. Následující podrobnosti čítače jsou k dispozici ve třídě [MessageCountDetails:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Zprávy ve frontě nebo odběr, které jsou v aktivním stavu a připraven k doručení.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Zprávy ve frontě nedoručených zpráv.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Zprávy v naplánovaném stavu.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Zprávy, které se nezdařily převod do jiné fronty nebo tématu a byly přesunuty do fronty nedoručených zpráv přenosu.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Zprávy čekající na převod do jiné fronty nebo tématu.

Pokud aplikace chce škálovat prostředky na základě délky fronty, měla by tak učinit s měřeným tempem. Získání čítače zpráv je nákladné operace uvnitř zprostředkovatele zpráv a provádění často přímo a nepříznivě ovlivňuje výkon entity.

> [!NOTE]
> Zprávy, které jsou odesílány do tématu service bus jsou předány odběry pro toto téma. Takže počet aktivních zpráv na samotné téma je 0, protože tyto zprávy byly úspěšně předány k odběru. Získejte počet zpráv na předplatné a ověřte, že je větší než 0. I když se zobrazí zprávy v předplatném, jsou ve skutečnosti uloženy v úložišti vlastněném tématem. 

Pokud se podíváte na odběry, pak by měly nenulový počet zpráv (které přidávají až 323 MB místa pro celou entitu).

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
