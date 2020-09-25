---
title: Funkce Azure Functions jako obslužná rutina události pro Azure Event Grid události
description: Popisuje, jak můžete používat Azure Functions jako obslužné rutiny událostí pro Event Grid události.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 87aeb78729dcc7bec9f193fab389e5c0952e63d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270306"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Funkce Azure Functions jako obslužná rutina události pro Event Grid události

Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede akci zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovával události a **Azure Functions** je jednou z nich. 

Pokud chcete reagovat na události z Event Grid, použijte **Azure Functions** v architektuře bez serveru. Při použití funkce Azure jako obslužné rutiny použijte Trigger Event Grid namísto obecného triggeru HTTP. Event Grid automaticky ověří aktivační události Event Grid. S obecnými aktivačními událostmi HTTP musíte implementovat [odpověď ověření](webhook-event-delivery.md) sami.

Další informace najdete v tématu [Event Grid trigger Azure Functions](../azure-functions/functions-bindings-event-grid.md) pro přehled použití triggeru Event Grid ve funkcích Functions.

## <a name="tutorials"></a>Kurzy

|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: zpracování událostí pomocí funkce](custom-event-to-function.md) | Odesílá vlastní událost do funkce pro zpracování. |
| [Kurz: automatizace změny velikosti nahraných imagí pomocí Event Grid](resize-images-on-storage-blob-upload-event.md) | Uživatelé nahrávají obrázky prostřednictvím webové aplikace do účtu úložiště. Když se vytvoří objekt BLOB úložiště, Event Grid pošle událost do aplikace Function App, která změní velikost nahraného obrázku. |
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří zachytávací soubor, Event Grid pošle událost do aplikace Function App. Aplikace načte zachytávací soubor a migruje data do datového skladu. |
| [Kurz: Azure Service Bus pro Azure Event Grid příklady integrace](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid posílá zprávy z Service Bus tématu do aplikace Function App a aplikace logiky. |

## <a name="rest-example-for-put"></a>Příklad REST (pro PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Povolit dávkování
Pro zajištění vyšší propustnosti povolte dávkování u předplatného. Pokud používáte Azure Portal, můžete nastavit maximální počet událostí na dávku a preferovanou velikost dávky v kilobajtech v době vytváření předplatného nebo po jeho vytvoření. 

Nastavení dávky můžete nakonfigurovat pomocí šablony Azure Portal, PowerShellu, rozhraní příkazového řádku nebo Správce prostředků. 

### <a name="azure-portal"></a>portál Azure
Při vytváření odběru v uživatelském rozhraní na stránce **vytvořit odběr události** přepněte na kartu **Pokročilé funkce** a nastavte hodnoty **maximálního počtu událostí na dávku** a **upřednostňovanou velikost dávky v kilobajtech**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Povolit dávkování v době vytváření předplatného":::

Tyto hodnoty pro existující předplatné můžete aktualizovat na kartě **funkce** na stránce **Event Grid tématu** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Povolit dávkování po vytvoření":::

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
V šabloně Azure Resource Manager můžete nastavit **maxEventsPerBatch** a **preferredBatchSizeInKilobytes** . Další informace najdete v referenčních informacích k [šabloně Microsoft. EventGrid eventSubscriptions](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
Pomocí příkazu [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) nebo [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) můžete nakonfigurovat nastavení související s dávkou pomocí následujících parametrů: `--max-events-per-batch` nebo `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) nebo [Update-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) můžete nakonfigurovat nastavení související s Batch pomocí následujících parametrů: `-MaxEventsPerBatch` nebo `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> Doručování událostí do funkce Azure Functions v **jiném tenantovi** se nepodporuje. 

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
