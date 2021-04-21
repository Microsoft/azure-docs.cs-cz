---
title: Azure Service Bus – počet zpráv
description: Načtěte počet zpráv uchovávaných ve frontách a odběrech pomocí Azure Resource Manager a rozhraní API Azure Service Bus NamespaceManager.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814587"
---
# <a name="get-message-counters"></a>Získat čítače zpráv
Tento článek ukazuje různé způsoby, jak u fronty nebo předplatného získat následující počty zpráv. Znalost počtu aktivních zpráv je užitečná při určování, zda fronta sestaví nevyřízené položky, které vyžadují více prostředků pro zpracování, než kolik je aktuálně nasazeno. 

| Čítač | Description |
| ----- | ---------- | 
| ActiveMessageCount | Počet zpráv ve frontě nebo předplatném, které jsou v aktivním stavu a připravené k doručení. |
| ScheduledMessageCount | Počet zpráv v naplánovaném stavu. |
| DeadLetterMessageCount | Počet zpráv ve frontě nedoručených zpráv |
| TransferMessageCount | Počet zpráv čekajících na přenos do jiné fronty nebo tématu. |
| TransferDeadLetterMessageCount | Počet zpráv, které se nepodařilo přenést do jiné fronty nebo tématu, a byly přesunuty do fronty nedoručených zpráv přenosu. |

Pokud aplikace chce škálovat prostředky na základě délky fronty, měla by to udělat se změřeným tempem. Získání čítačů zpráv je náročná operace uvnitř zprostředkovatele zpráv a jejich provádění je často přímo a nepříznivě ovlivňuje výkon entity.

> [!NOTE]
> Zprávy odeslané do tématu Service Bus jsou předávány do předplatných tohoto tématu. Proto je počet aktivních zpráv v samotném tématu 0, protože tyto zprávy byly úspěšně předány do předplatného. Získejte počet zpráv v předplatném a ověřte, že je větší než 0. I když vidíte zprávy v předplatném, jsou ve skutečnosti uloženy v úložišti, které vlastní téma. Pokud se podíváte na odběry, měli by mít nenulový počet zpráv (který pro celou entitu přidá až 323 MB místa).


## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Přejděte do svého oboru názvů a vyberte frontu. Čítače zpráv se zobrazí na stránce **Přehled** pro danou frontu.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Čítače zpráv na stránce s přehledem fronty":::

Přejděte do svého oboru názvů, vyberte téma a pak vyberte předplatné tématu. Čítače zpráv se zobrazí na stránce **Přehled** pro danou frontu.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Čítače zpráv na stránce s přehledem předplatného":::

## <a name="using-azure-cli"></a>Použití Azure CLI
Pomocí [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) příkazu získáte podrobnosti o počtu zpráv pro frontu, jak je znázorněno v následujícím příkladu. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Zde je ukázkový výstup:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Pomocí [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) příkazu získáte podrobnosti o počtu zpráv pro předplatné, jak je znázorněno v následujícím příkladu. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Pomocí PowerShellu můžete získat podrobnosti o počtu zpráv pro frontu následujícím způsobem:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Zde je ukázkový výstup:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

Podrobnosti o počtu zpráv pro předplatné získáte následujícím způsobem:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

Vrácený `MessageCountDetails` objekt má následující vlastnosti: `ActiveMessageCount` , `DeadLetterMessageCount` , `ScheduledMessageCount` , `TransferDeadLetterMessageCount` , `TransferMessageCount` . 

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si ukázky v jazyce podle vašeho výběru, abyste prozkoumali Azure Service Bus funkce. 

- [Azure Service Bus ukázek klientských knihoven pro Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus ukázek klientských knihoven pro Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus ukázek klientských knihoven pro JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus Ukázky klientské knihovny pro TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Ukázky Azure. Messaging. ServiceBus pro .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Vyhledejte ukázky pro starší klientské knihovny .NET a Java níže:
- [Ukázky Microsoft. Azure. ServiceBus pro .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Ukázky Azure-ServiceBus pro Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
