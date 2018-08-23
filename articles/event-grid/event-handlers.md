---
title: Azure Event Grid obslužné rutiny událostí
description: Popisuje obslužné rutiny událostí podporovaných pro Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: tomfitz
ms.openlocfilehash: 08658c42687626779dea0de7dd724d9431a296da
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617137"
---
# <a name="event-handlers-in-azure-event-grid"></a>Obslužné rutiny událostí ve službě Azure Event Grid

Obslužná rutina události je místo, kde událost je odeslána. Obslužná rutina provede některé další akci zpracovat událost. Několik služeb Azure se automaticky konfigurují pro zpracování událostí. Můžete také všechny Webhooku pro zpracování událostí. WebHook nemusí být hostovaná v Azure pro zpracování událostí. Event Grid se podporuje jenom koncové body HTTPS Webhooku.

Tento článek obsahuje odkazy na obsah pro každou obslužnou rutinu události.

## <a name="azure-automation"></a>Azure Automation

Zpracování událostí pomocí automatizované runbooky pomocí Azure Automation.

|Titul  |Popis  |
|---------|---------|
|[Kurz: Automatizace Azure pomocí služby Event Grid a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost se aktivuje runbook služby Automation, značky virtuální počítač, který aktivuje zprávu, která je odeslána do kanálu Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Pomocí služby Azure Functions bez serveru reakci na události.

Pokud jako obslužnou rutinu používáte Azure Functions, použijte místo generických aktivačních událostí HTTP aktivační událost Event Gridu. Event Grid automaticky ověřuje aktivační události funkcí Event Grid. U obecných aktivačních událostí HTTP je potřeba implementovat [odpověď ověření](security-authentication.md#webhook-event-delivery).

|Titul  |Popis  |
|---------|---------|
| [Trigger služby Event Grid pro službu Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Základní informace o použití trigger služby Event Grid ve funkcích. |
| [Kurz: automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](resize-images-on-storage-blob-upload-event.md) | Uživatelé nahrávání obrázků do webové aplikace k účtu úložiště. Když se objekt blob úložiště, služby Event Grid odešle událost pro aplikace function app, které mění velikost nahraného obrázku. |
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří zachytávací soubor, služby Event Grid odešle událost pro aplikaci function app. Aplikace načte zachytávací soubor a migraci dat do datového skladu. |
| [Kurz: Azure Service Bus do příklady integrace služby Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus fungování aplikace a aplikaci logiky. |

## <a name="event-hubs"></a>Event Hubs

Pokud vaše řešení rychleji, než dokáže zpracovat události načte události, využívat službu Event Hubs. Vaše aplikace zpracovává události ze služby Event Hubs na něj vlastní plán. Je možné škálovat vaše zpracování příchozích událostí pro zpracování událostí.

Event Hubs může fungovat jako zdroj událostí nebo obslužnou rutinu události. V následujícím článku ukazuje, jak používat Event Hubs jako obslužná rutina.

|Titul  |Popis  |
|---------|---------|
| [Rychlý start: směrování vlastních událostí do služby Azure Event Hubs pomocí rozhraní příkazového řádku Azure a služby Event Grid](custom-event-to-eventhub.md) | Odešle vlastní události do centra událostí pro zpracování aplikací. |

Příklady služby Event Hubs jako zdroj, najdete v článku [zdroje služby Event Hubs](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridní připojení

Azure Relay Hybrid Connections použijte k odesílání událostí do aplikace, které jsou v podnikové síti a nemají veřejně přístupném koncovém bodu.

|Titul  |Popis  |
|---------|---------|
| [Kurz: odesílání událostí do služby hybridního připojení](custom-event-to-hybrid-connection.md) | Odešle vlastní události do existující hybridní připojení pro zpracování aplikace naslouchacího procesu. |

## <a name="logic-apps"></a>Logic Apps

Pomocí Logic Apps můžete automatizovat firemní procesy pro reagování na události.

|Titul  |Popis  |
|---------|---------|
| [Kurz: monitorování změn virtuálního počítače pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny k virtuálnímu počítači a odešle e-mailů o těchto změnách. |
| [Kurz: odesílání e-mailová oznámení o událostech služby Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky odešle e-mailové oznámení pokaždé, když zařízení do služby IoT hub. |
| [Kurz: Azure Service Bus do příklady integrace služby Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus fungování aplikace a aplikaci logiky. |

## <a name="queue-storage"></a>Queue Storage

Používání úložiště Queue přijímat události, které je třeba načíst. Pokud máte dlouhotrvající proces, který trvá příliš dlouho neodpovídala, může pomocí Queue storage. Odesíláním událostí do fronty úložiště můžete vyžádat aplikace a zpracovávat události z vlastní plán.

|Titul  |Popis  |
|---------|---------|
| [Rychlý start: směrování vlastních událostí do úložiště Azure Queue storage pomocí Azure CLI a Event gridu](custom-event-to-queue-storage.md) | Popisuje, jak posílat vlastní události do fronty úložiště. |

## <a name="webhooks"></a>Webhooky

Použití webhooků pro přizpůsobitelné koncové body, které reagují na události.

|Titul  |Popis  |
|---------|---------|
| Rychlý start: vytvoření a směrování vlastních událostí pomocí - [rozhraní příkazového řádku Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), a [portál](custom-event-quickstart-portal.md). | Ukazuje, jak k odesílání vlastních události Webhooku. |
| Rychlý start: Směrování událostí služby Blob storage do vlastního webového koncového bodu s - [rozhraní příkazového řádku Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), a [portál](blob-event-quickstart-portal.md). | Ukazuje, jak odesílat události služby blob storage do Webhooku. |
| [Rychlý start: odeslání události registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak odesílat události Container Registry pomocí Azure CLI. |
| [Přehled: příjem událostí pro koncový bod HTTP](receive-events.md) | Popisuje, jak ověřit koncový bod HTTP pro příjem událostí z odběr událostí a příjem a deserializovat události. |

## <a name="next-steps"></a>Další postup

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
