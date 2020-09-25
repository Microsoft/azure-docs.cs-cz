---
title: Centrum událostí jako obslužná rutina události pro Azure Event Grid události
description: Popisuje, jak můžete používat centra událostí jako obslužné rutiny událostí pro Azure Event Grid události.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 5e6a84c1737c6b8a575f47576aeb1d3d9efae6eb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322559"
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

## <a name="message-properties"></a>Vlastnosti zprávy
Použijete-li **centrum událostí** jako obslužnou rutinu události pro události z Event Grid, jsou vlastnosti, které jste obdrželi v záhlavích zpráv: 

| Název vlastnosti | Popis |
| ------------- | ----------- | 
| AEG-Subscription-Name | Název odběru události |
| AEG – počet doručení | <p>Počet pokusů o provedení události.</p> <p>Příklad: "1"</p> |
| AEG-typ události | <p>Typ události</p><p> Příklad: Microsoft. Storage. blobCreated</p> | 
| AEG – metadata – verze | <p>Verze události v metadatech</p> <p>Příklad: "1".</p><p> Pro **Event Grid schéma událostí**Tato vlastnost představuje verzi metadat a pro **schéma cloudové události**představuje **verzi specifikace**. </p>|
| AEG-data-verze | <p>Verze dat události</p><p>Příklad: "1".</p><p>Pro **Event Grid schéma událostí**Tato vlastnost představuje verzi dat a pro **schéma cloudových událostí**se nepoužije.</p> |
| AEG-Output-Event-ID | ID události Event Grid |

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

> [!NOTE]
> Doručování událostí do centra událostí Azure v **jiném tenantovi** se nepodporuje. 

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
