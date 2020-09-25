---
title: Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události
description: Popisuje, jak můžete použít Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 00f937a66ff17c2d5f502fe976675c999ee02a58
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270164"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovával události a **Azure Service Bus** je jednou z nich. 

Můžete použít frontu nebo téma služby jako obslužnou rutinu pro události z Event Grid. 

## <a name="service-bus-queues"></a>Fronty služby Service Bus
Události v Event Grid můžete směrovat přímo na Service Bus fronty pro použití v ukládání do vyrovnávací paměti nebo v rámci scénářů řízení & příkazů v podnikových aplikacích.

V Azure Portal při vytváření odběru událostí vyberte **Service Bus Queue** jako typ koncového bodu a pak klikněte na **Vybrat koncový bod** a zvolte Service Busovou frontu.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Přidání obslužné rutiny fronty Service Bus pomocí rozhraní příkazového řádku

V případě Azure CLI následující příklad přihlašuje a připojí téma Event gridu k frontě Service Bus:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Témata služby Service Bus

Události v Event Grid můžete směrovat přímo do Service Busch témat pro zpracování událostí systému Azure pomocí Service Bus témata nebo pro scénáře zasílání zpráv pomocí příkazů řízení &.

V Azure Portal při vytváření odběru událostí vyberte **Service Bus téma** jako typ koncového bodu a pak klikněte na **Vybrat a koncový bod** a zvolte Service Bus téma.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Přidání obslužné rutiny tématu Service Bus pomocí rozhraní příkazového řádku

V případě Azure CLI následující příklad přihlašuje a připojí téma Event gridu k frontě Service Bus:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>Vlastnosti zprávy
Použijete-li **Service Bus téma nebo frontu** jako obslužnou rutinu události z Event Grid, jedná se o vlastnosti, které obdržíte v hlavičkách zprávy: 

| Název vlastnosti | Popis |
| ------------- | ----------- | 
| AEG-Subscription-Name | Název odběru události |
| AEG – počet doručení | <p>Počet pokusů o provedení události.</p> <p>Příklad: "1"</p> |
| AEG-typ události | <p>Typ události</p><p> Příklad: Microsoft. Storage. blobCreated</p> | 
| AEG – metadata – verze | <p>Verze události v metadatech</p> <p>Příklad: "1".</p><p> Pro **Event Grid schéma událostí**Tato vlastnost představuje verzi metadat a pro **schéma cloudové události**představuje **verzi specifikace**. </p>|
| AEG-data-verze | <p>Verze dat události</p><p>Příklad: "1".</p><p>Pro **Event Grid schéma událostí**Tato vlastnost představuje verzi dat a pro **schéma cloudových událostí**se nepoužije.</p> |

## <a name="message-headers"></a>Záhlaví zpráv
Při odesílání události do fronty Service Bus nebo tématu jako zprostředkované zprávy je ve službě zprostředkované zprávy `messageid` interní ID systému.

Interní ID systému pro zprávu bude udržováno v průběhu předoručení události, takže se můžete vyhnout duplicitním dodávkám zapnutím **duplicity v** entitě Service Bus. Doporučujeme povolit dobu trvání duplicity u Service Bus entity tak, aby byla buď hodnota TTL (Time to Live) pro událost nebo maximální doba opakování, podle toho, co je delší.

## <a name="rest-examples-for-put"></a>Příklady REST (pro PUT)

### <a name="service-bus-queue"></a>Fronta služby Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus doručení front se spravovanou identitou

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
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Téma služby Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus téma – doručování se spravovanou identitou

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> Doručování událostí do fronty nebo tématu služby Azure pro službu Azure v **jiném tenantovi** se nepodporuje. 

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
