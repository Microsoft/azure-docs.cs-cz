---
title: Webhooky jako obslužné rutiny událostí pro události Azure Event Grid
description: Popisuje, jak můžete použít Webhooky jako obslužné rutiny událostí pro Azure Event Grid události. Azure Automation Runbooky a Logic Apps jsou podporovány jako obslužné rutiny událostí prostřednictvím webhooků.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875797"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooky, Runbooky Automation Logic Apps jako obslužné rutiny událostí pro Azure Event Grid události
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky konfiguruje pro zpracování událostí. Pro zpracování událostí můžete také použít jakýkoli Webhook. Webhook se nemusí hostovat v Azure a zpracovávat události. Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS.

> [!NOTE]
> - Azure Automation Runbooky a Logic Apps jsou podporovány jako obslužné rutiny událostí prostřednictvím webhooků. 
> - I když můžete použít **Webhook** jako **Typ koncového bodu** ke konfiguraci funkce Azure jako obslužné rutiny události, použijte **funkci Azure Function** jako typ koncového bodu. Další informace najdete v tématu [funkce Azure Functions jako obslužná rutina události](handler-functions.md).

## <a name="webhooks"></a>Webhooky
V následujících článcích najdete přehled a příklady použití webhooků jako obslužných rutin událostí. 

|Nadpis  |Popis  |
|---------|---------|
| Rychlý Start: vytváření a směrování vlastních událostí pomocí [Azure CLI](custom-event-quickstart.md), [PowerShellu](custom-event-quickstart-powershell.md)a [portálu](custom-event-quickstart-portal.md). | Ukazuje, jak odesílat vlastní události Webhooku. |
| Rychlý Start: směrování událostí služby Blob Storage do vlastního webového koncového bodu pomocí [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShellu](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)a [portálu](blob-event-quickstart-portal.md). | Ukazuje, jak odesílat události služby Blob Storage do Webhooku. |
| [Rychlý Start: odeslání událostí registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k posílání Container Registrych událostí. |
| [Přehled: příjem událostí do koncového bodu HTTP](receive-events.md) | Popisuje, jak ověřit koncový bod HTTP pro příjem událostí z odběru události a přijímat a deserializovat události. |


## <a name="azure-automation"></a>Azure Automation
Události můžete zpracovávat pomocí Azure Automation sad Runbook. Zpracování událostí pomocí automatizovaných runbooků je podporováno prostřednictvím webhooků. Vytvoříte Webhook pro Runbook a pak použijete obslužnou rutinu Webhooku. Příklad najdete v následujícím kurzu: 

|Nadpis  |Popis  |
|---------|---------|
|[Kurz: Azure Automation s využitím Event Grid a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost aktivuje Runbook služby Automation, který zaznamená virtuální počítač, a aktivuje zprávu odeslanou kanálu Microsoft Teams. |


## <a name="logic-apps"></a>Logic Apps
Použijte **Logic Apps** k implementaci podnikových procesů ke zpracování Event Gridch událostí. V tomto scénáři nevytvoříte Webhook explicitně. Webhook se vytvoří automaticky při konfiguraci aplikace logiky, která bude zpracovávat události z Event Grid. Příklady najdete v následujících kurzech: 

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: monitorování změn virtuálních počítačů pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny virtuálního počítače a odesílá e-maily o těchto změnách. |
| [Kurz: odesílání e-mailových oznámení o událostech Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky pošle e-mailové oznámení pokaždé, když se do služby IoT Hub přidá zařízení. |
| [Kurz: reakce na události Azure Service Bus přijatých prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid posílá zprávy z Service Bus tématu do aplikace Function App a Logic Apps. |

## <a name="rest-example-for-put"></a>Příklad REST (pro PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
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
