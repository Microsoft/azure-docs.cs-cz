---
title: Obslužné rutiny událostí azure event gridu
description: Popisuje podporované obslužné rutiny událostí pro Azure Event Grid. Azure Automation, Funkce, Centra událostí, Hybridní připojení, Logic Apps, Service Bus, Queue Storage, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265048"
---
# <a name="event-handlers-in-azure-event-grid"></a>Obslužné rutiny událostí v Azure Event Grid

Obslužná rutina události je místo, kam je událost odeslána. Obslužná rutina provede některé další akce ke zpracování události. Několik služeb Azure se automaticky nakonfiguruje pro zpracování událostí. Můžete také použít libovolný WebHook pro zpracování událostí. WebHook nemusí být hostované v Azure pro zpracování událostí. Event Grid podporuje pouze koncové body HTTPS WebHook.

Tento článek obsahuje odkazy na obsah pro každou obslužnou rutinu události.

## <a name="azure-automation"></a>Azure Automation

Azure Automation slouží ke zpracování událostí pomocí automatických sad runbooků.

|Nadpis  |Popis  |
|---------|---------|
|[Kurz: Azure Automation s Grid událostí a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost aktivuje runbook automatizace, který označí virtuální počítač a aktivuje zprávu, která je odeslána do kanálu Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Funkce Azure slouží k odpovědi na události bez serveru.

Pokud jako obslužnou rutinu používáte Azure Functions, použijte místo generických aktivačních událostí HTTP aktivační událost Event Gridu. Event Grid automaticky ověřuje aktivační události funkcí Event Grid. U obecných aktivačních událostí HTTP je potřeba implementovat [odpověď ověření](security-authentication.md#webhook-event-delivery).

|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: Zpracování událostí pomocí funkce](custom-event-to-function.md) | Odešle vlastní událost do funkce pro zpracování. |
| [Aktivační událost mřížky událostí pro funkce Azure](../azure-functions/functions-bindings-event-grid.md) | Přehled použití aktivační události mřížky událostí v části Funkce. |
| [Kurz: automatizace změna velikosti nahraných obrázků pomocí event gridu](resize-images-on-storage-blob-upload-event.md) | Uživatelé nahrávají obrázky prostřednictvím webové aplikace do účtu úložiště. Když je vytvořen objekt blob úložiště, Event Grid odešle událost do aplikace funkce, která změní velikost nahrané ho obrázku. |
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Centra událostí vytvoří soubor pro digitalizaci, event grid odešle událost do aplikace funkce. Aplikace načte soubor capture a migruje data do datového skladu. |
| [Kurz: Příklady integrace Azure Service Bus do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus do funkční aplikace a aplikace logiky. |

## <a name="event-hubs"></a>Event Hubs

Centra událostí použijte, když vaše řešení získá události rychleji, než může zpracovat události. Vaše aplikace zpracovává události z centra událostí podle vlastního plánu. Zpracování událostí můžete škálovat tak, aby zpracovávalo příchozí události.

Centra událostí mohou fungovat jako zdroj událostí nebo obslužná rutina události. Následující článek ukazuje, jak používat centra událostí jako obslužnou rutinu.

|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: směrování vlastních událostí do Centra událostí Azure pomocí Azure CLI a Event Grid](custom-event-to-eventhub.md) | Odešle vlastní událost do centra událostí ke zpracování aplikací. |
| [Šablona Správce prostředků: vlastní téma a koncový bod Centra událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Šablona Správce prostředků, která vytvoří předplatné pro vlastní téma. Odesílá události do Centra událostí Azure. |

Příklady event hubů jako zdroje najdete v tématu [Zdroj centra událostí](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridní připojení

Hybridní připojení Azure Relay slouží k odesílání událostí do aplikací, které jsou v rámci podnikové sítě a nemají veřejně přístupný koncový bod.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: odesílání událostí do hybridního připojení](custom-event-to-hybrid-connection.md) | Odešle vlastní událost do existujícího hybridního připojení pro zpracování aplikací naslouchací proces. |

## <a name="logic-apps"></a>Logic Apps

Pomocí aplikací Logika můžete automatizovat obchodní procesy pro reakci na události.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: sledování změn virtuálních strojů pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny ve virtuálním počítači a odesílá e-maily o těchto změnách. |
| [Kurz: Odesílání e-mailových oznámení o událostech služby Azure IoT Hub pomocí aplikací Logika](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky odešle e-mail s oznámením pokaždé, když se zařízení přidá do vašeho centra IoT hub. |
| [Kurz: Příklady integrace Azure Service Bus do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus do funkční aplikace a aplikace logiky. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Fronty služby Service Bus

Události v event gridu můžete směrovat přímo do front service bus pro použití při ukládání do vyrovnávací paměti nebo příkazu & scénáře řízení v podnikových aplikacích.

Na webu Azure Portal při vytváření předplatného událostí vyberte jako typ koncového bodu "Fronta služby Service Bus" a pak klikněte na "vybrat koncový bod", abyste zvolte frontu služby Service Bus.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Přidání obslužné rutiny fronty služby Service Bus pomocí zapisovatcí chodnínízpráv služby Service Bus

Pro Azure CLI následující příklad přihlásí a připojí téma mřížky událostí do fronty Service Bus:

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

### <a name="service-bus-topics"></a>Témata služby Service Bus

Události v programu Event Grid můžete směrovat přímo do témat service bus, abyste mohli zpracovávat systémové události Azure s tématy služby Service Bus nebo pro scénáře řízení zpráv & příkaz.

Na webu Azure Portal při vytváření předplatného událostí vyberte jako typ koncového bodu možnost "Téma služby Service Bus" a pak klikněte na "vybrat a koncový bod", abyste zvolili téma služby Service Bus.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Použití cli k přidání obslužné rutiny tématu služby Service Bus

Pro Azure CLI následující příklad přihlásí a připojí téma mřížky událostí do fronty Service Bus:

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

Použití úložiště fronty pro příjem událostí, které je třeba vytáhnout. Úložiště fronty můžete použít, pokud máte dlouho běžící proces, který trvá příliš dlouho reagovat. Odesláním událostí do úložiště fronty může aplikace vyžádat a zpracovat události podle vlastního plánu.

|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: směrování vlastních událostí do úložiště fronty Azure pomocí Azure CLI a Event Grid](custom-event-to-queue-storage.md) | Popisuje, jak odeslat vlastní události do úložiště fronty. |

## <a name="webhooks"></a>Webhooky

Použijte webhooky pro přizpůsobitelné koncové body, které reagují na události.

|Nadpis  |Popis  |
|---------|---------|
| Úvodní příručka: vytvářet a směrovat vlastní události s - [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md)a [portál](custom-event-quickstart-portal.md). | Ukazuje, jak odeslat vlastní události na WebHook. |
| Úvodní příručka: směrování událostí úložiště objektů blob do vlastního webového koncového bodu s - [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)a [portál](blob-event-quickstart-portal.md). | Ukazuje, jak odesílat události úložiště objektů blob webhooku. |
| [Úvodní příručka: Odeslání událostí registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k odesílání událostí registru kontejnerů. |
| [Přehled: příjem událostí do koncového bodu HTTP](receive-events.md) | Popisuje, jak ověřit koncový bod HTTP přijímat události z odběru událostí a přijímat a rekonstruovat události. |

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
