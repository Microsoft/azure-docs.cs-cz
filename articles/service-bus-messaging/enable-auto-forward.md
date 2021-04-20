---
title: Povolit automatické předávání pro Azure Service Bus fronty a odběry
description: Tento článek vysvětluje, jak povolit automatické předávání pro fronty a odběry pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku a programovacích jazyků (C#, Java, Python a JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ef22ae08485dc896c94858db4e422cf89a00ec1f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755227"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Povolit automatické předávání pro Azure Service Bus fronty a odběry
Funkce automatického předávání Service Bus umožňuje řetězit frontu nebo předplatné do jiné fronty nebo tématu, které je součástí stejného oboru názvů. Pokud je povoleno automatické předávání, Service Bus automaticky odebere zprávy, které jsou umístěny v první frontě nebo předplatném (zdroj), a umístí je do druhé fronty nebo tématu (cíl). Je stále možné odeslat zprávu cílové entitě přímo. Další informace najdete v tématu [zřetězení Service Bus entit s automatickým přesměrováním](service-bus-auto-forwarding.md). Tento článek ukazuje různé způsoby, jak povolit automatické předávání Service Busch front a předplatných. 

> [!IMPORTANT]
> Základní Service Bus úrovně nepodporuje funkci automatického předávání. Úroveň Standard a Premium tuto funkci podporuje. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Při vytváření **fronty** nebo **odběru** tématu v Azure Portal vyberte možnost **Přesměrovat zprávy do fronty nebo tématu** , jak je znázorněno v následujících příkladech. Pak určete, zda chcete zprávy předávané do fronty nebo tématu. V tomto příkladu je vybrána možnost **fronta** a je vybrána fronta (**MyQueue**) ze stejného oboru názvů.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Vytvoření fronty s povoleným automatickým přeposíláním
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Povolit automatické dopřední v době vytváření fronty":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Vytvoření předplatného pro téma s povoleným automatickým přeposíláním
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Povolit automatické dopřední v době vytváření předplatného":::

## <a name="using-azure-cli"></a>Použití Azure CLI
Chcete-li **vytvořit frontu s povoleným automatickým přesměrováním**, použijte [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) příkaz s `--forward-to` nastavením na název fronty nebo tématu, do kterého chcete zprávy předávat. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Chcete-li **vytvořit odběr pro téma s povoleným automatickým přeposíláním**, použijte [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) příkaz s `--forward-to` nastavením na název fronty nebo tématu, do kterého chcete zprávy předávat.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Chcete-li **vytvořit frontu s povoleným automatickým přesměrováním**, použijte [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) příkaz s `-ForwardTo` nastavením na název fronty nebo tématu, do kterého chcete zprávy předávat. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Chcete-li **vytvořit odběr pro téma s povoleným automatickým přeposíláním**, použijte [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) příkaz s `-ForwardTo` nastavením na název fronty nebo tématu, do kterého chcete zprávy předávat.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Chcete-li **vytvořit frontu s povoleným automatickým přeposíláním**, nastavte `forwardTo` v oddílu vlastnosti fronty na název fronty nebo tématu, do kterého chcete zprávy předávat. Další informace najdete v referenčních informacích k [šabloně Microsoft. ServiceBus Namespaces/Queue Template](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Chcete-li **vytvořit odběr tématu s povoleným automatickým přeposíláním**, nastavte `forwardTo` v oddílu vlastnosti fronty na název fronty nebo tématu, na které chcete zprávy předávat. Další informace najdete v tématu [Referenční dokumentace k oborům názvů Microsoft. ServiceBus/témata/předplatných](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "forwardTo": "myqueue2"
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