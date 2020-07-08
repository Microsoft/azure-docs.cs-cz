---
title: Funkce Azure Functions jako obslužná rutina události pro Azure Event Grid události
description: Popisuje, jak můžete používat Azure Functions jako obslužné rutiny událostí pro Event Grid události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800414"
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
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
