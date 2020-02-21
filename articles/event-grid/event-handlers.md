---
title: Obslužné rutiny událostí Azure Event Grid
description: Popisuje podporované obslužné rutiny událostí pro Azure Event Grid. Azure Automation, funkce, Event Hubs, Hybrid Connections, Logic Apps, Service Bus, Queue Storage, Webhooky.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525800"
---
# <a name="event-handlers-in-azure-event-grid"></a>Obslužné rutiny událostí v Azure Event Grid

Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky konfiguruje pro zpracování událostí. Pro zpracování událostí můžete také použít jakýkoli Webhook. Webhook se nemusí hostovat v Azure a zpracovávat události. Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS.

Tento článek obsahuje odkazy na obsah pro jednotlivé obslužné rutiny události.

## <a name="azure-automation"></a>Automatizace Azure

Použijte Azure Automation ke zpracování událostí s automatizovanými Runbooky.

|Název  |Popis  |
|---------|---------|
|[Kurz: Azure Automation s využitím Event Grid a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost aktivuje Runbook služby Automation, který zaznamená virtuální počítač, a aktivuje zprávu odeslanou kanálu Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Používejte Azure Functions pro reakci bez serveru na události.

Pokud jako obslužnou rutinu používáte Azure Functions, použijte místo generických aktivačních událostí HTTP aktivační událost Event Gridu. Event Grid automaticky ověřuje aktivační události funkcí Event Grid. U obecných aktivačních událostí HTTP je potřeba implementovat [odpověď ověření](security-authentication.md#webhook-event-delivery).

|Název  |Popis  |
|---------|---------|
| [Rychlý Start: zpracování událostí pomocí funkce](custom-event-to-function.md) | Odesílá vlastní událost do funkce pro zpracování. |
| [Aktivační událost Event Grid pro Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Přehled použití triggeru Event Grid v Functions. |
| [Kurz: automatizace změny velikosti nahraných imagí pomocí Event Grid](resize-images-on-storage-blob-upload-event.md) | Uživatelé nahrávají obrázky prostřednictvím webové aplikace do účtu úložiště. Když se vytvoří objekt BLOB úložiště, Event Grid pošle událost do aplikace Function App, která změní velikost nahraného obrázku. |
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří zachytávací soubor, Event Grid pošle událost do aplikace Function App. Aplikace načte zachytávací soubor a migruje data do datového skladu. |
| [Kurz: Azure Service Bus pro Azure Event Grid příklady integrace](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid posílá zprávy z Service Bus tématu do aplikace Function App a Logic Apps. |

## <a name="event-hubs"></a>Event Hubs

Použijte Event Hubs, pokud řešení získává události rychleji, než může zpracovávat události. Vaše aplikace zpracovává události od Event Hubs podle vlastního plánu. Můžete škálovat zpracování událostí a zpracovávat příchozí události.

Event Hubs může fungovat buď jako zdroj události, nebo jako obslužná rutina události. Následující článek ukazuje, jak použít Event Hubs jako obslužnou rutinu.

|Název  |Popis  |
|---------|---------|
| [Rychlý Start: směrování vlastních událostí do Azure Event Hubs pomocí Azure CLI a Event Grid](custom-event-to-eventhub.md) | Odesílá vlastní událost do centra událostí pro zpracování aplikací. |
| [Správce prostředků Šablona: vlastní téma a koncový bod Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Správce prostředků šablonu, která vytvoří odběr pro vlastní téma. Odesílá události do Azure Event Hubs. |

Příklady Event Hubs jako zdroje najdete v tématu [Event Hubs source](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridní připojení

Pomocí Azure Relay Hybrid Connections můžete odesílat události aplikacím, které se nacházejí v podnikové síti, a nemají veřejně přístupný koncový bod.

|Název  |Popis  |
|---------|---------|
| [Kurz: odeslání událostí do hybridního připojení](custom-event-to-hybrid-connection.md) | Odesílá vlastní událost do stávajícího hybridního připojení pro zpracování pomocí aplikace naslouchacího procesu. |

## <a name="logic-apps"></a>Logic Apps

Použijte Logic Apps k automatizaci obchodních procesů pro reagování na události.

|Název  |Popis  |
|---------|---------|
| [Kurz: monitorování změn virtuálních počítačů pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny virtuálního počítače a odesílá e-maily o těchto změnách. |
| [Kurz: odesílání e-mailových oznámení o událostech Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky pošle e-mailové oznámení pokaždé, když se do služby IoT Hub přidá zařízení. |
| [Kurz: Azure Service Bus pro Azure Event Grid příklady integrace](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid posílá zprávy z Service Bus tématu do aplikace Function App a Logic Apps. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Fronty služby Service Bus

Události v Event Grid můžete směrovat přímo na Service Bus fronty pro použití v ukládání do vyrovnávací paměti nebo v rámci scénářů řízení & příkazů v podnikových aplikacích.

V Azure Portal při vytváření odběru událostí vyberte Service Bus Queue jako typ koncového bodu a pak klikněte na vybrat koncový bod, aby bylo možné zvolit frontu Service Bus.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Přidání obslužné rutiny fronty Service Bus pomocí rozhraní příkazového řádku

V případě Azure CLI následující příklad přihlašuje a připojí téma Event gridu k frontě Service Bus:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Service Bus témata

Události v Event Grid můžete směrovat přímo do Service Bus témat, abyste mohli zpracovávat události systému Azure pomocí Service Busch témat, nebo pro scénáře řízení zasílání zpráv z příkazového &.

V Azure Portal při vytváření odběru událostí vyberte Service Bus téma jako typ koncového bodu a pak klikněte na vybrat a koncový bod, aby se daly vybrat Service Bus téma.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Přidání obslužné rutiny tématu Service Bus pomocí rozhraní příkazového řádku

V případě Azure CLI následující příklad přihlašuje a připojí téma Event gridu k frontě Service Bus:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Queue Storage

Pomocí Queue Storage můžete přijímat události, které je potřeba vyřadit. Úložiště Queue můžete použít, když máte dlouho běžící proces, který trvá příliš dlouho, než odpoví. Při odesílání událostí do služby Queue Storage může aplikace získat a zpracovat události podle vlastního plánu.

|Název  |Popis  |
|---------|---------|
| [Rychlý Start: směrování vlastních událostí do Azure Queue Storage pomocí rozhraní příkazového řádku Azure a Event Grid](custom-event-to-queue-storage.md) | Popisuje, jak odesílat vlastní události do úložiště fronty. |

## <a name="webhooks"></a>Webhooky

Webhooky použijte k přizpůsobitelným koncovým bodům, které reagují na události.

|Název  |Popis  |
|---------|---------|
| Rychlý Start: vytváření a směrování vlastních událostí pomocí [Azure CLI](custom-event-quickstart.md), [PowerShellu](custom-event-quickstart-powershell.md)a [portálu](custom-event-quickstart-portal.md). | Ukazuje, jak odesílat vlastní události Webhooku. |
| Rychlý Start: směrování událostí služby Blob Storage do vlastního webového koncového bodu pomocí [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShellu](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)a [portálu](blob-event-quickstart-portal.md). | Ukazuje, jak odesílat události služby Blob Storage do Webhooku. |
| [Rychlý Start: odeslání událostí registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k posílání Container Registrych událostí. |
| [Přehled: příjem událostí do koncového bodu HTTP](receive-events.md) | Popisuje, jak ověřit koncový bod HTTP pro příjem událostí z odběru události a přijímat a deserializovat události. |

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku o [Event Gridu](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
