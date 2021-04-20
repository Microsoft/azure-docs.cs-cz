---
title: Povolit detekci duplicitních zpráv – Azure Service Bus
description: Tento článek vysvětluje, jak povolit detekci duplicitních zpráv pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku a programovacích jazyků (C#, Java, Python a JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755190"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Povolení Detekce duplicitních zpráv pro Azure Service Bus frontu nebo téma
Pokud povolíte zjišťování duplicitních dat pro frontu nebo téma, Azure Service Bus udržuje historii všech zpráv odeslaných do fronty nebo tématu pro konfiguraci časového intervalu. V průběhu tohoto intervalu nebude vaše fronta nebo téma ukládat žádné duplicitní zprávy. Povolení této vlastnosti zaručuje přesně jedno doručení v čase definovaném uživatelem. Další informace najdete v tématu [zjištění duplicit](duplicate-detection.md). Tento článek ukazuje různé způsoby, jak povolit detekci duplicitních zpráv pro Service Bus frontu nebo téma. 


> [!NOTE]
> - Základní vrstva Service Bus nepodporuje detekci duplicit. Úrovně Standard a Premium podporují detekci duplicit. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).
> - Po vytvoření fronty nebo tématu nelze povolit nebo zakázat zjišťování duplicit. Můžete to udělat jenom v době vytváření fronty nebo tématu. 

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Při vytváření **fronty** v Azure Portal vyberte možnost **Povolit detekci duplicit** , jak je znázorněno na následujícím obrázku. Při vytváření fronty nebo tématu můžete nakonfigurovat velikost okna zjišťování duplicitních dat. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Povolit detekci duplicit v době vytváření fronty":::

Při vytváření tématu v Azure Portal vyberte možnost **Povolit detekci duplicit** , jak je znázorněno na následujícím obrázku. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Povolit detekci duplicit v době vytváření tématu":::

Můžete také nakonfigurovat toto nastavení pro existující frontu nebo téma, pokud jste povolili detekci duplicit v době vytváření. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Aktualizovat velikost okna Detekce duplicitních dat pro existující frontu nebo téma
Chcete-li změnit velikost okna zjišťování duplicit pro existující frontu nebo téma, klikněte na stránce **Přehled** na možnost **změnit** pro **okno zjišťování duplicit**.  

#### <a name="queue"></a>Fronta
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Nastavit velikost okna detekce duplicit pro frontu":::

#### <a name="topic"></a>Téma
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Nastavení velikosti okna detekce duplicit pro téma":::


## <a name="using-azure-cli"></a>Použití Azure CLI
Pokud chcete **vytvořit frontu s povoleným** vyhledáváním duplicit, použijte [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) příkaz s `--enable-duplicate-detection` nastavením na `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Chcete-li **vytvořit téma s povoleným** vyhledáváním duplicit, použijte [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) příkaz s `--enable-duplicate-detection` nastavením na `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Výše uvedené příklady také nastavují velikost okna zjišťování duplicit pomocí `--duplicate-detection-history-time-window` parametru. Velikost okna je nastavená na jeden den. Výchozí hodnota je 10 minut a maximální povolená hodnota je sedm dní.

Chcete-li **aktualizovat frontu o novou velikost okna detekce**, použijte [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) příkaz s `--duplicate-detection-history-time-window` parametrem. V tomto příkladu je velikost okna aktualizována na sedm dní. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Podobně pokud chcete **aktualizovat téma s novou velikostí okna detekce**, použijte [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) příkaz s `--duplicate-detection-history-time-window` parametrem. V tomto příkladu je velikost okna aktualizována na sedm dní.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Použití Azure Powershell
Pokud chcete **vytvořit frontu s povoleným** vyhledáváním duplicit, použijte [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) příkaz s `-RequiresDuplicateDetection` nastavením na `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Chcete-li **vytvořit téma s povoleným** vyhledáváním duplicit, použijte [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) příkaz s `-RequiresDuplicateDetection` nastavením na `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

Výše uvedené příklady také nastavují velikost okna zjišťování duplicit pomocí `-DuplicateDetectionHistoryTimeWindow` parametru. Velikost okna je nastavená na jeden den. Výchozí hodnota je 10 minut a maximální povolená hodnota je sedm dní.

Chcete-li **aktualizovat frontu o novou velikost okna detekce**, přečtěte si následující příklad.  V tomto příkladu je velikost okna aktualizována na sedm dní.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Chcete-li **aktualizovat téma s novou velikostí okna detekce**, přečtěte si následující příklad. V tomto příkladu je velikost okna aktualizována na sedm dní.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Pokud chcete **vytvořit frontu s povolenou** duplicitou detekce, nastavte `requiresDuplicateDetection` na `true` v části vlastnosti fronty. Další informace najdete v referenčních informacích k [šabloně Microsoft. ServiceBus Namespaces/Queue Template](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). Zadejte hodnotu pro `duplicateDetectionHistoryTimeWindow` vlastnost pro nastavení velikosti okna zjišťování duplicit. V následujícím příkladu je nastaveno na jeden den.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Chcete-li **vytvořit téma s povoleným** vyhledáváním duplicit, nastavte `requiresDuplicateDetection` možnost `true` v části vlastnosti tématu. Další informace najdete v referenčních informacích k [šablonám oborů názvů a témat Microsoft. ServiceBus](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
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