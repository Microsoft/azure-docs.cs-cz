---
title: Přenos hybridního připojení jako obslužné rutiny události pro Azure Event Grid události
description: Popisuje, jak můžete použít Azure Relay Hybrid Connections jako obslužné rutiny událostí pro Azure Event Grid události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800884"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Přenos hybridního připojení jako obslužné rutiny události pro Azure Event Grid události
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovával události a **Azure Relay** je jednou z nich. 

Azure **Relay Hybrid Connections** slouží k posílání událostí do aplikací, které jsou v podnikové síti a nemají veřejně přístupný koncový bod.

## <a name="tutorials"></a>Kurzy
V následujícím kurzu najdete příklad použití hybridního připojení Azure Relay jako obslužné rutiny události. 

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: odeslání událostí do hybridního připojení](custom-event-to-hybrid-connection.md) | Odesílá vlastní událost do stávajícího hybridního připojení pro zpracování pomocí aplikace naslouchacího procesu. |

## <a name="rest-example-for-put"></a>Příklad REST (pro PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 