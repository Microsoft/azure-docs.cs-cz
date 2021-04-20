---
title: Povolit dělení do oddílů v Azure Service Bus fronty a témata
description: Tento článek vysvětluje, jak povolit dělení do Azure Service Bus front a témat pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku a programovacích jazyků (C#, Java, Python a JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755187"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Povolení dělení pro Azure Service Bus frontu nebo téma
Service Bus oddíly umožňují rozdělit do oddílů fronty a témata nebo entity zasílání zpráv v rámci více zprostředkovatelů zpráv a úložišť pro zasílání zpráv. Dělení znamená, že celková propustnost entit rozdělená na oddíly již není omezena výkonem jediného zprostředkovatele zpráv nebo úložiště pro zasílání zpráv. Kromě toho dočasný výpadek úložiště pro zasílání zpráv nevykresluje rozdělenou frontu nebo téma jako nedostupné. Dělené fronty a témata můžou obsahovat všechny pokročilé funkce Service Bus, jako je podpora transakcí a relací. Další informace najdete v tématu [dělené fronty a témata](service-bus-partitioning.md). Tento článek ukazuje různé způsoby, jak povolit detekci duplicitních zpráv pro Service Bus frontu nebo téma. 

> [!IMPORTANT]
> - Dělení je k dispozici při vytváření entit pro všechny fronty a témata v jednotkách Basic a Standard. Pro SKU zasílání zpráv na úrovni Premium není k dispozici, ale všechny dříve existující rozdělené entity v oborech názvů Premium budou fungovat podle očekávání.
> - Možnost dělení v žádné existující frontě nebo tématu není možné změnit. Možnost lze nastavit pouze při vytváření fronty nebo tématu. 

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Při vytváření **fronty** v Azure Portal vyberte **Povolit dělení** , jak je znázorněno na následujícím obrázku. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Povolit dělení na oddíly v době vytváření fronty":::

Při vytváření tématu v Azure Portal vyberte **Povolit dělení** , jak je znázorněno na následujícím obrázku. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Povolit dělení na oddíly v době vytváření tématu":::

## <a name="using-azure-cli"></a>Použití Azure CLI
Pokud chcete **vytvořit frontu s povoleným rozdělením na oddíly**, použijte [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) příkaz s `--enable-partitioning` nastavením na `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Chcete-li **vytvořit téma s povoleným rozdělením na oddíly**, použijte [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) příkaz s `--enable-partitioning` nastavením na `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Pokud chcete **vytvořit frontu s povoleným rozdělením na oddíly**, použijte [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) příkaz s `-EnablePartitioning` nastavením na `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Chcete-li **vytvořit téma s povoleným rozdělením na oddíly**, použijte [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) příkaz s `-EnablePartitioning` nastavením na `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Pokud chcete **vytvořit frontu s povoleným rozdělením na oddíly**, nastavte `enablePartitioning` `true` v části vlastnosti fronty. Další informace najdete v referenčních informacích k [šabloně Microsoft. ServiceBus Namespaces/Queue Template](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Chcete-li **vytvořit téma s povoleným** vyhledáváním duplicit, nastavte `enablePartitioning` možnost `true` v části vlastnosti tématu. Další informace najdete v referenčních informacích k [šablonám oborů názvů a témat Microsoft. ServiceBus](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

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
            "enablePartitioning": true
          }
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