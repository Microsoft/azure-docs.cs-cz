---
title: Povolit relace Azure Service Bus zpráv | Microsoft Docs
description: Tento článek vysvětluje, jak povolit relace zpráv pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku a programovacích jazyků (C#, Java, Python a JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755189"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Povolení relací zpráv pro front-Azure Service Bus nebo pro předplatné
Azure Service Bus relace umožňují společné a seřazené zpracování neohraničených sekvencí souvisejících zpráv. Relace se dají použít v vzorcích **First in, First out (FIFO)** a **Request-response** . Další informace najdete v tématu [relace zpráv](message-sessions.md). Tento článek ukazuje různé způsoby, jak povolit relace pro Service Bus frontu nebo odběr. 

> [!IMPORTANT]
> - Základní Service Bus úrovně nepodporuje relace. Úrovně Standard a Premium podporují relace. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).
> - Po vytvoření fronty nebo předplatného nemůžete povolit nebo zakázat relace zpráv. To můžete udělat jenom v době vytváření fronty nebo předplatného. 

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Při vytváření **fronty** v Azure Portal vyberte **Povolit relace** , jak je znázorněno na následujícím obrázku. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Povolit relaci v době vytváření fronty":::

Při vytváření odběru pro téma v Azure Portal vyberte **Povolit relace** , jak je znázorněno na následujícím obrázku. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Povolit relaci v době vytváření předplatného":::

## <a name="using-azure-cli"></a>Použití Azure CLI
Chcete-li **vytvořit frontu s povolenými relacemi zpráv**, použijte [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) příkaz s `--enable-session` nastavením na `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Chcete-li **vytvořit odběr pro téma s povolenou relací zpráv**, použijte [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) příkaz s `--enable-session` nastavením na `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Chcete-li **vytvořit frontu s povolenými relacemi zpráv**, použijte [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) příkaz s `-RequiresSession` nastavením na `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Chcete-li **vytvořit odběr pro téma s povolenou relací zpráv**, použijte [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) příkaz s `-RequiresSession` nastavením na `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Pokud chcete **vytvořit frontu s povolenými relacemi zpráv**, nastavte `requiresSession` možnost `true` v části vlastnosti fronty. Další informace najdete v referenčních informacích k [šabloně Microsoft. ServiceBus Namespaces/Queue Template](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Chcete-li **Vytvořit předplatné pro téma s povolenou relací zpráv**, nastavte `requiresSession` možnost `true` v části vlastnosti odběru. Další informace najdete v tématu [Referenční dokumentace k oborům názvů Microsoft. ServiceBus/témata/předplatných](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "requiresSession": true
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