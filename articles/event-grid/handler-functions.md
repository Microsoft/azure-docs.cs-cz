---
title: Použití funkce v Azure jako obslužné rutiny události pro Azure Event Grid události
description: Popisuje, jak můžete používat funkce vytvořené v a hostované Azure Functions jako obslužné rutiny událostí pro Event Grid události.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: beddc35f2dd8db974492d14aec27ce754a74737c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632508"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Použití funkce jako obslužné rutiny události pro Event Grid události

Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede akci zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovával události a **Azure Functions** je jednou z nich. 


Pokud chcete používat funkci v Azure jako obslužnou rutinu pro události, postupujte podle jednoho z těchto přístupů: 

-   Použijte [aktivační událost Event Grid](../azure-functions/functions-bindings-event-grid-trigger.md).  Jako **Typ koncového bodu** zadejte **funkci Azure** . Pak zadejte aplikaci funkcí a funkci, která bude zpracovávat události. 
-   Použijte [Trigger http](../azure-functions/functions-bindings-http-webhook.md).  Jako **Typ koncového bodu** zadejte **Webhook** . Pak zadejte adresu URL pro funkci, která bude zpracovávat události. 

Doporučujeme použít první přístup (Event Grid Trigger), protože má následující výhody oproti druhému přístupu:
-   Event Grid automaticky ověří aktivační události Event Grid. S obecnými aktivačními událostmi HTTP musíte implementovat [odpověď ověření](webhook-event-delivery.md) sami.
-   Event Grid automaticky upraví rychlost doručení událostí do funkce aktivované událostí Event Grid na základě pozorované míry, při které funkce může zpracovávat události. Tato funkce se shoduje s tím, že se chyby doručení, které vyplývají z neschopnosti funkce zpracovávat události, neschopnost zpracování událostí, protože počet zpracovaných událostí funkce se může v průběhu času měnit. Pro zvýšení efektivity při vysoké propustnosti povolte dávkování pro odběr událostí. Další informace najdete v tématu [Povolení dávkového](#enable-batching)zpracování.

    > [!NOTE]
    > V současné době nemůžete použít Trigger Event Grid pro aplikaci Function App, pokud je událost doručena ve schématu **CloudEvents** . Místo toho použijte Trigger HTTP.

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
V šabloně Azure Resource Manager můžete nastavit **maxEventsPerBatch** a **preferredBatchSizeInKilobytes** . Další informace najdete v referenčních informacích k [šabloně Microsoft. EventGrid eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
Pomocí příkazu [AZ eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) nebo [AZ eventgrid Event-Subscription Update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) můžete nakonfigurovat nastavení související s dávkou pomocí následujících parametrů: `--max-events-per-batch` nebo `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) nebo [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) můžete nakonfigurovat nastavení související s Batch pomocí následujících parametrů: `-MaxEventsPerBatch` nebo `-PreferredBatchSizeInKiloBytes` .

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) .
