---
title: Přenos hybridního připojení jako obslužné rutiny události pro Azure Event Grid události
description: Popisuje, jak můžete použít Azure Relay Hybrid Connections jako obslužné rutiny událostí pro Azure Event Grid události.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "91270198"
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