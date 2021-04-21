---
title: Povolení nedoručených zpráv pro Azure Service Bus fronty a odběry
description: Tento článek vysvětluje, jak povolit nedoručené dopisy pro fronty a odběry pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku a programovacích jazyků (C#, Java, Python a JavaScript).
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819348"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Povolit nedoručené dopisy při vypršení platnosti zprávy pro Azure Service Bus fronty a odběry
Azure Service Bus fronty a odběry pro témata poskytují sekundární dílčí frontu nazvanou frontu nedoručených zpráv (DLQ). Fronta nedoručených zpráv nemusí být explicitně vytvořena a nelze ji odstranit ani spravovat nezávisle na hlavní entitě. Účelem fronty nedoručených zpráv je uchovávat zprávy, které nelze doručit žádnému příjemci, nebo zprávy, které nebylo možné zpracovat. Další informace najdete v tématu [přehled Service Bus front nedoručených zpráv](service-bus-dead-letter-queues.md). Tento článek ukazuje různé způsoby, jak povolit nedoručené dopisy pro Service Bus fronty a odběry. 

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Při vytváření **fronty** nebo **předplatného** pro téma v Azure Portal vyberte **Povolit nedoručené dopisy při vypršení platnosti zprávy** , jak je znázorněno v následujících příkladech. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Vytvoření fronty s povoleným nedoručeným dopisem
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Povolit v době vytváření fronty nedoručené dopisy":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Vytvoření předplatného s povoleným nedoručeným dopisem
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Povolit nedoručené dopisy v době vytváření předplatného":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Aktualizace nedoručených zpráv pro nastavení vypršení platnosti zprávy pro existující frontu
Na stránce **Přehled** pro frontu Service Bus vyberte aktuální hodnotu pro nastavení **nedoručených** zpráv pro vypršení platnosti zprávy. V následujícím příkladu je aktuální hodnota **zakázána**. V místním okně můžete povolit nebo zakázat nedoručená oznámení pro vypršení platnosti zprávy. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Povolení nedoručených zpráv při vypršení platnosti zprávy pro existující frontu":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Aktualizace nedoručených zpráv pro nastavení vypršení platnosti zprávy pro existující předplatné
Na stránce **Přehled** předplatného Service Bus vyberte aktuální hodnotu pro nastavení **nedoručených** zpráv pro vypršení platnosti zprávy. V následujícím příkladu je aktuální hodnota **zakázána**. V místním okně můžete povolit nebo zakázat nedoručená oznámení pro vypršení platnosti zprávy. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Povolení nedoručených zpráv při vypršení platnosti zprávy pro existující předplatné":::

## <a name="using-azure-cli"></a>Použití Azure CLI
Chcete-li **vytvořit frontu s nedoručenými oznámeními pro povolené vypršení platnosti zprávy**, použijte [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) příkaz s `--enable-dead-lettering-on-message-expiration` nastavením na `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Pokud chcete **pro existující frontu povolit nedoručené dopisy u nastavení vypršení platnosti zprávy**, použijte [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) příkaz s `--enable-dead-lettering-on-message-expiration` nastavením na `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Chcete-li **vytvořit odběr tématu s nedoručeným oznámením o povoleném vypršení platnosti zprávy**, použijte [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) příkaz s `--enable-dead-lettering-on-message-expiration` nastavením na `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Chcete-li povolit nedoručení pro **nastavení vypršení platnosti zprávy pro odběr tématu**, použijte [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) příkaz se `--enable-dead-lettering-on-message-expiration` sadou `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Chcete-li **vytvořit frontu s nedoručenými oznámeními pro povolené vypršení platnosti zprávy**, použijte [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) příkaz s `-DeadLetteringOnMessageExpiration` nastavením na `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Pokud chcete **pro existující frontu povolit nedoručená oznámení pro nastavení vypršení platnosti zprávy**, použijte [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) příkaz, jak je znázorněno v následujícím příkladu.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Chcete-li **Vytvořit předplatné pro téma s povoleným odhlášením pro vypršení platnosti zprávy**, použijte [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) příkaz s `-DeadLetteringOnMessageExpiration` nastavením na `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Postup **Povolení nedoručených zpráv pro nastavení vypršení platnosti zprávy pro existující předplatné** najdete v následujícím příkladu.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Chcete-li **vytvořit frontu s nedoručenými oznámeními v povoleném vypršení platnosti zprávy**, nastavte `deadLetteringOnMessageExpiration` v oddílu vlastnosti fronty na `true` . Další informace najdete v referenčních informacích k [šabloně Microsoft. ServiceBus Namespaces/Queue Template](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Chcete-li **Vytvořit předplatné pro téma s povoleným odhlášením v vypršení platnosti zprávy**, nastavte `deadLetteringOnMessageExpiration` v oddílu vlastnosti fronty na `true` . Další informace najdete v tématu [Referenční dokumentace k oborům názvů Microsoft. ServiceBus/témata/předplatných](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "deadLetteringOnMessageExpiration": true
              }
            }
          ]
        }
      ]
    }
  ]
}
```


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