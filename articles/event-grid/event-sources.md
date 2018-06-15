---
title: Zdroje událostí Azure událostí mřížky
description: Popisuje zdroje událostí podporovaných pro Azure událostí mřížky
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34302122"
---
# <a name="event-sources-in-azure-event-grid"></a>Zdroje událostí v mřížce událostí Azure

Zdroje událostí je, kde se stane události. Několik služeb Azure budou automaticky nakonfigurovány odesílat události. Můžete také vytvořit vlastní aplikace, které odesílají události. Vlastní aplikace nemusíte být hostovaná v Azure a použít událost mřížky pro distribuci událostí.

Tento článek obsahuje odkazy na obsah pro každý zdroj událostí.

## <a name="azure-subscriptions"></a>Předplatná Azure

Přihlásit k odběru předplatných Azure událostí reagovat na změny v prostředky napříč předplatné Azure.

|Titul |Popis  |
|---------|---------|
| [Integrovat událostí mřížky a týmy pro Microsoft Azure Automation.](ensure-tags-exists-on-new-virtual-machines.md) |Vytvoření virtuálního počítače, který odesílá události. Událost se aktivuje runbook služby automatizace, který značky virtuální počítač a aktivuje zprávu, která je odeslána do kanálu Teams společnosti Microsoft. |
| [Schématu události](event-schema-subscriptions.md) | Zobrazuje pole v událostech předplatného Azure. |

## <a name="custom-topics"></a>Vlastní témata

Přihlášení k odběru, na vlastní témata reakce na události aplikace.

|Titul  |Popis  |
|---------|---------|
| [Vytvoření a trasy vlastních událostí pomocí rozhraní příkazového řádku Azure](custom-event-quickstart.md) | Ukazuje, jak používat rozhraní příkazového řádku Azure k odesílání vlastních událostí. |
| [Vytvoření a trasy vlastních událostí pomocí prostředí Azure PowerShell](custom-event-quickstart-powershell.md) | Ukazuje, jak používat prostředí Azure PowerShell k odesílání vlastních událostí. |
| [Vytvoření a trasy vlastních událostí pomocí portálu Azure](custom-event-quickstart-portal.md) | Ukazuje, jak pomocí portálu k odeslání vlastní události. |
| [Odeslání na vlastní tématu](post-to-custom-topic.md) | Ukazuje, jak odeslat událost do vlastní tématu. |
| [Vlastní události trasy Azure Queue Storage](custom-event-to-queue-storage.md) | Popisuje, jak odeslat vlastní události Queue Storage. |
| [Schématu události](event-schema.md) | Pole zobrazuje ve vlastních událostí. |

## <a name="event-hubs"></a>Event Hubs

Přihlásit k odběru služby Event Hubs událostí reagovat k zachycení událostí souboru.

|Titul  |Popis  |
|---------|---------|
| [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří soubor zachycení, odešle událostí mřížky událost do aplikaci funkce. Aplikace načte soubor zachycení a migraci dat do datového skladu. |
| [Schématu události](event-schema-event-hubs.md) | Zobrazuje pole v událostech služby Event Hubs. |

## <a name="iot-hub"></a>IoT Hub

Přihlášení k odběru do služby IoT Hub události reagovat na zařízení vytvořena a odstranit události.

|Titul  |Popis  |
|---------|---------|
| [Odesílání e-mailová oznámení o událostech Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky odešle e-mailové oznámení pokaždé, když přidání zařízení do služby IoT hub. |
| [Reagování na události služby IoT Hub pomocí mřížky události pro aktivační událost akce](../iot-hub/iot-hub-event-grid.md) | Přehled integrace služby Iot Hubs s událostí mřížky. |
| [Schématu události](event-schema-iot-hub.md) | Zobrazí pole v IoT Hub události. |

## <a name="media-services"></a>Media Services

Přihlásit k odběru služby Media Services událostí reakce na události stavu úlohy.

|Titul  |Popis  |
|---------|---------|
| [Reaktivní události Media Services](../media-services/latest/reacting-to-media-services-events.md) | Přehled integrace Media Services s událostí mřížky. |
| [Události služby Azure Media Services trasy pro koncový bod vlastní webové pomocí rozhraní příkazového řádku](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak odesílat události ze služby Media Services. |
| [Schématu události](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zobrazuje pole v událostech Media Services. |

## <a name="resource-groups"></a>Skupiny prostředků

Přihlásit k odběru prostředků skupiny událostí reagovat na změny v prostředky napříč skupinu prostředků.

|Titul  |Popis  |
|---------|---------|
| [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky sleduje změny k virtuálnímu počítači a odešle e-mailů o těchto změnách. |
| [Schéma událostí](event-schema-resource-groups.md) | Zobrazuje pole v prostředku skupiny události. |

## <a name="service-bus"></a>Service Bus

Přihlásit k odběru sběrnice událostí reagovat na zprávy bez aktivní naslouchací proces.

|Titul  |Popis  |
|---------|---------|
| [Azure Service Bus příklady integrace Azure událostí mřížky](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Událost mřížky odešle zprávy ze tématu Service Bus fungování aplikace a aplikace logiky. |
| [Přehled integrace mřížky událostí Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Přehled integrace služby Service Bus s událostí mřížky. |
| [Schématu události](event-schema-service-bus.md) | Zobrazuje pole v událostech služby Service Bus. |

## <a name="storage"></a>Úložiště

Přihlásit k odběru úložiště objektů Blob událostí reakce na události vytvořené a odstraněných objektů blob.

|Titul  |Popis  |
|---------|---------|
| [Události úložiště objektů Blob trasy pro koncový bod vlastní webové pomocí rozhraní příkazového řádku Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat rozhraní příkazového řádku Azure k odesílání událostí úložiště objektů blob. |
| [Události úložiště objektů Blob trasy pro koncový bod vlastní webové pomocí prostředí PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat prostředí Azure PowerShell k odesílání událostí úložiště objektů blob. |
| [Reakce na události služby Blob Storage](../storage/blobs/storage-blob-event-overview.md) | Přehled integrace úložiště objektů Blob s událostí mřížky. |
| [Schématu události](event-schema-blob-storage.md) | Zobrazuje pole v událostech úložiště objektů Blob. |

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
