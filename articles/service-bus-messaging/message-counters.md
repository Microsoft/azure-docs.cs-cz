---
title: Azure Service Bus – počet zpráv
description: Načtěte počet zpráv uchovávaných ve frontách a odběrech pomocí Azure Resource Manager a rozhraní API Azure Service Bus NamespaceManager.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341273"
---
# <a name="message-counters"></a>Čítače zpráv

Počet zpráv ve frontách a odběrech můžete načíst pomocí Azure Resource Manager a rozhraní API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) v sadě SDK .NET Framework.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pomocí PowerShellu můžete tento počet získat následujícím způsobem:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Podrobnosti o počtu zpráv

Znalost počtu aktivních zpráv je užitečná při určování, zda fronta sestaví nevyřízené položky, které vyžadují více prostředků pro zpracování, než kolik je aktuálně nasazeno. Ve třídě [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) jsou k dispozici následující podrobnosti čítače:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): zprávy ve frontě nebo předplatném, které jsou v aktivním stavu a připravené k doručení.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): zprávy ve frontě nedoručených zpráv.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): zprávy v naplánovaném stavu.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): zprávy, které se nepodařilo přenést do jiné fronty nebo tématu, a byly přesunuty do fronty nedoručených zpráv přenosu.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): zprávy čekající na přenos do jiné fronty nebo tématu.

Pokud aplikace chce škálovat prostředky na základě délky fronty, měla by to udělat se změřeným tempem. Získání čítačů zpráv je náročná operace uvnitř zprostředkovatele zpráv a jejich provádění je často přímo a nepříznivě ovlivňuje výkon entity.

> [!NOTE]
> Zprávy odeslané do tématu Service Bus jsou předávány do předplatných tohoto tématu. Proto je počet aktivních zpráv v samotném tématu 0, protože tyto zprávy byly úspěšně předány do předplatného. Získejte počet zpráv v předplatném a ověřte, že je větší než 0. I když vidíte zprávy v předplatném, jsou ve skutečnosti uloženy v úložišti, které vlastní téma. 

Pokud se podíváte na odběry, měly by mít nenulový počet zpráv (které přidávají až 323MB prostoru pro celou entitu).

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
