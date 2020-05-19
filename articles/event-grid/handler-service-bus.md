---
title: Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události
description: Popisuje, jak můžete použít Service Bus fronty a témata jako obslužné rutiny událostí pro Azure Event Grid události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 201d3203d845ce84207d103750709fe2ff93f022
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598574"
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

## <a name="service-bus-topics"></a>Service Bus témata

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
Použijete-li **Service Bus téma nebo frontu** jako obslužnou rutinu události z Event Grid, nastavte následující záhlaví zpráv: 

| Název vlastnosti | Popis |
| ------------- | ----------- | 
| AEG-Subscription-Name | Název odběru události |
| AEG – počet doručení | <p>Počet pokusů o provedení události.</p> <p>Příklad: "1"</p> |
| AEG-typ události | <p>Typ události</p><p> Příklad: Microsoft. Storage. blobCreated</p> | 
| AEG – metadata – verze | <p>Verze události v metadatech</p> <p>Příklad: "1".</p><p> Pro **Event Grid schéma událostí**Tato vlastnost představuje verzi metadat a pro **schéma cloudové události**představuje **verzi specifikace**. </p>|
| AEG-data-verze | <p>Verze dat události</p><p>Příklad: "1".</p><p>Pro **Event Grid schéma událostí**Tato vlastnost představuje verzi dat a pro **schéma cloudových událostí**se nepoužije.</p> |

## <a name="message-headers"></a>Záhlaví zpráv
Při odesílání události do fronty Service Bus nebo tématu jako zprostředkované zprávy `messageid` je ID zprostředkované zprávy **ID události**.

ID události bude zachováno v průběhu Redistribuce události, takže se můžete vyhnout duplicitním dodávkám zapnutím **duplicity v** entitě Service Bus. Doporučujeme povolit dobu trvání duplicity u Service Bus entity tak, aby byla buď hodnota TTL (Time to Live) pro událost nebo maximální doba opakování, podle toho, co je delší.

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
