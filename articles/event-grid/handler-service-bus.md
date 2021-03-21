---
title: Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události
description: Popisuje, jak můžete použít Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 12b72420e3475b46a4cd61ce5032b478af740dde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97399855"
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

V případě Azure CLI následující příklad přihlašuje a připojí téma Event gridu k Service Bus tématu:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Při odesílání události do fronty Service Bus nebo tématu jako zprostředkované zprávy je ve službě zprostředkované zprávy `messageid` interní ID systému.

Interní ID systému pro zprávu bude udržováno v průběhu předoručení události, takže se můžete vyhnout duplicitním dodávkám zapnutím **duplicity v** entitě Service Bus. Doporučujeme povolit dobu trvání duplicity u Service Bus entity tak, aby byla buď hodnota TTL (Time to Live) pro událost nebo maximální doba opakování, podle toho, co je delší.

## <a name="rest-examples-for-put"></a>Příklady REST (pro PUT)

### <a name="service-bus-queue"></a>Fronta Service Bus

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

### <a name="service-bus-topic"></a>Service Bus téma

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

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
