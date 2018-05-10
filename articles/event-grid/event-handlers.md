---
title: Azure mřížky událost obslužné rutiny událostí
description: Popisuje obslužné rutiny událostí podporovaných pro Azure událostí mřížky
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 01a49ec47b406b7b916ecad5ef9ccc3af295fac1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="event-handlers-in-azure-event-grid"></a>Obslužné rutiny událostí v mřížce událostí Azure

Obslužné rutiny události je místem, kde událost je odeslána. Obslužná rutina trvá některé další akce ke zpracování události. Několik služeb Azure se automaticky konfigurují pro zpracování událostí. Můžete taky všechny webhooku pro zpracování událostí. Webhook se nemusí být hostovaná v Azure a zpracování událostí.

Tento článek obsahuje odkazy na obsah pro každou obslužnou rutinu události.

## <a name="azure-automation"></a>Azure Automation

Zpracování událostí pomocí automatizované runbooků pomocí Azure Automation.

|Nadpis  |Popis  |
|---------|---------|
|[Integrovat událostí mřížky a týmy pro Microsoft Azure Automation.](ensure-tags-exists-on-new-virtual-machines.md) |Vytvoření virtuálního počítače, který odesílá události. Událost se aktivuje runbook služby automatizace, který značky virtuální počítač a aktivuje zprávu, která je odeslána do kanálu Teams společnosti Microsoft. |

## <a name="azure-functions"></a>Azure Functions

Pomocí Azure Functions pro bez serveru reakci na události.

|Nadpis  |Popis  |
|---------|---------|
| [Aktivační událost mřížky pro Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Přehled používání aktivační událost mřížky ve funkcích. |
| [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](resize-images-on-storage-blob-upload-event.md) | Uživatelé odesílat obrázky prostřednictvím webové aplikace k účtu úložiště. Při vytváření objektu blob storage, odešle událost mřížky událost funkce aplikace, která se změní velikost image nahrané. |
| [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří soubor zachycení, odešle událostí mřížky událost do aplikaci funkce. Aplikace načte soubor zachycení a migraci dat do datového skladu. |
| [Azure Service Bus příklady integrace Azure událostí mřížky](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Událost mřížky odešle zprávy ze tématu Service Bus fungování aplikace a aplikace logiky. |

## <a name="hybrid-connections"></a>Hybridní připojení

Hybridní připojení předávání přes Azure použijte k odesílání událostí do aplikace, které jsou v podnikové síti a nemáte veřejně přístupném koncovém bodu.

|Nadpis  |Popis  |
|---------|---------|
| [Odesílání událostí hybridní připojení.](custom-event-to-hybrid-connection.md) | Odešle vlastní události pro stávající hybridní připojení pro zpracování naslouchací proces aplikace. |

## <a name="logic-apps"></a>Logic Apps

Použití aplikace logiky k automatizaci obchodních procesů pro reagování na události.

|Nadpis  |Popis  |
|---------|---------|
| [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky sleduje změny k virtuálnímu počítači a odešle e-mailů o těchto změnách. |
| [Odesílání e-mailová oznámení o událostech Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky odešle e-mailové oznámení pokaždé, když přidání zařízení do služby IoT hub. |
| [Azure Service Bus příklady integrace Azure událostí mřížky](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Událost mřížky odešle zprávy ze tématu Service Bus fungování aplikace a aplikace logiky. |

## <a name="queue-storage"></a>Queue Storage

Používání úložiště Queue přijímat události, které musí být vyžádány.

|Nadpis  |Popis  |
|---------|---------|
| [Vlastní události trasy Azure Queue Storage pomocí rozhraní příkazového řádku Azure a událostí mřížky](custom-event-to-queue-storage.md) | Popisuje, jak odeslat vlastní události Queue Storage. |

## <a name="webhooks"></a>WebHooky

Pomocí webhooků pro přizpůsobitelné koncové body, které reakce na události.

|Nadpis  |Popis  |
|---------|---------|
| [Přijímat události pro koncový bod HTTP](receive-events.md) | Popisuje, jak k ověření koncového bodu protokolu HTTP přijímat události z události předplatného a přijímat a deserializovat události. |

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
