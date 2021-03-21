---
title: Centrum událostí jako obslužná rutina události pro Azure Event Grid události
description: Popisuje, jak můžete používat centra událostí jako obslužné rutiny událostí pro Azure Event Grid události.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005621"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Centrum událostí jako obslužná rutina události pro Azure Event Grid události
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede akci zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovávala události a **Azure Event Hubs** je jednou z nich. 

Použijte **Event Hubs** , když vaše řešení získá události z Event Grid rychleji než může zpracovávat události. Jakmile se události nacházejí v centru událostí, může vaše aplikace zpracovávat události z centra událostí podle vlastního plánu. Můžete škálovat zpracování událostí a zpracovávat příchozí události.

## <a name="tutorials"></a>Kurzy
Podívejte se na následující příklady: 

|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: směrování vlastních událostí do Azure Event Hubs pomocí Azure CLI](custom-event-to-eventhub.md) | Odesílá vlastní událost do centra událostí pro zpracování aplikací. |
| [Správce prostředků Šablona: Vytvoření vlastního tématu Event Grid a odeslání událostí do centra událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Správce prostředků šablonu, která vytvoří odběr pro vlastní téma. Odesílá události do Azure Event Hubs. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>Příklady REST (pro PUT)


### <a name="event-hub"></a>Centrum událostí

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Centrum událostí – doručení pomocí spravované identity

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
