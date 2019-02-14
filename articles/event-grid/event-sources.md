---
title: Zdroje událostí služby Azure Event Grid
description: Tento článek popisuje zdroje událostí podporovaných pro Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: b6abb0048dcc722241a2cd87354224b0fd7fa2f9
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234182"
---
# <a name="event-sources-in-azure-event-grid"></a>Zdroje událostí ve službě Azure Event Grid

Zdroj událostí je, kde k události dojde. Několik služeb Azure se automaticky konfigurují pro odeslání události. Můžete také vytvořit vlastní aplikace, které odesíláte události. Vlastní aplikace nemusí být hostovaná v Azure pomocí služby Event Grid pro distribuci událostí.

Tento článek obsahuje odkazy na obsah pro každý zdroj událostí.

## <a name="azure-subscriptions"></a>Předplatná Azure

Přihlášení k odběru předplatného Azure událostí reakce na změny v prostředcích v předplatném Azure.

|Název |Popis  |
|---------|---------|
| [Kurz: Automatizace v Azure pomocí služby Event Grid a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost se aktivuje runbook služby Automation, značky virtuální počítač, který aktivuje zprávu, která je odeslána do kanálu Microsoft Teams. |
| [Postupy: přihlášení k odběru událostí prostřednictvím portálu](subscribe-through-portal.md) | Použití portálu k přihlášení k odběru událostí předplatného Azure. |
| [Azure CLI: přihlášení k odběru událostí předplatného Azure](./scripts/event-grid-cli-azure-subscription.md) |Ukázkový skript, který vytvoří odběr Event gridu s předplatným Azure a odesílá události Webhooku. |
| [Prostředí PowerShell: přihlášení k odběru událostí předplatného Azure](./scripts/event-grid-powershell-azure-subscription.md)| Ukázkový skript, který vytvoří odběr Event gridu s předplatným Azure a odesílá události Webhooku. |
| [Schéma událostí](event-schema-subscriptions.md) | Zobrazí pole v událostech předplatného Azure. |

## <a name="container-registry"></a>Container Registry

Přihlášení k odběru Container Registry událostí reakce na změny v obrázcích.

|Název |Popis  |
|---------|---------|
| [Rychlý start: odeslání události registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak odesílat události Container Registry pomocí Azure CLI. |
| [Schéma událostí](event-schema-container-registry.md) | Zobrazí pole v událostech Container Registry. |

## <a name="custom-topics"></a>Vlastní témata

Přihlásit k vlastní témata pro reakci na události aplikace.

|Název  |Popis  |
|---------|---------|
| [Rychlý start: vytvoření a směrování vlastních událostí pomocí Azure CLI](custom-event-quickstart.md) | Ukazuje, jak používat rozhraní příkazového řádku Azure k odesílání vlastních událostí. |
| [Rychlý start: vytvoření a směrování vlastních událostí pomocí Azure Powershellu](custom-event-quickstart-powershell.md) | Ukazuje, jak pomocí prostředí Azure PowerShell k odesílání vlastních události. |
| [Rychlý start: vytvoření a směrování vlastních událostí pomocí webu Azure portal](custom-event-quickstart-portal.md) | Ukazuje, jak pomocí portálu pro odesílání vlastních událostí. |
| [Rychlý start: směrování vlastních událostí do úložiště Azure Queue storage](custom-event-to-queue-storage.md) | Popisuje, jak posílat vlastní události do fronty úložiště. |
| [Postupy: odeslání do vlastního tématu](post-to-custom-topic.md) | Ukazuje, jak publikovat události do vlastního tématu. |
| [Azure CLI: vytvoření vlastního tématu Event gridu](./scripts/event-grid-cli-create-custom-topic.md)|Ukázkový skript vytvoří vlastní téma. Tento skript načte koncového bodu a klíče.|
| [Azure CLI: přihlášení k odběru událostí vlastního tématu](./scripts/event-grid-cli-subscribe-custom-topic.md)|Ukázkový skript vytvoří odběr vlastního tématu. Odesílá události do Webhooku.|
| [PowerShell: vytvoření vlastního tématu Event gridu](./scripts/event-grid-powershell-create-custom-topic.md)|Ukázkový skript vytvoří vlastní téma. Tento skript načte koncového bodu a klíče.|
| [Prostředí PowerShell: přihlášení k odběru událostí vlastního tématu](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Ukázkový skript vytvoří odběr vlastního tématu. Odesílá události do Webhooku.|
| [Šablony Resource Manageru: vlastní témata a koncový bod Webhooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Šablony Resource Manageru, který vytvoří vlastní téma a odběr tohoto vlastního tématu. Odesílá události do Webhooku. |
|
| [Šablony Resource Manageru: vlastní témata a Event Hubs koncového bodu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Šablony Resource Manageru, která vytvoří odběr vlastního tématu. Odesílá události do Azure Event Hubs. |
| [Schéma událostí](event-schema.md) | Zobrazí pole ve vlastních událostí. |

## <a name="event-hubs"></a>Event Hubs

Přihlášení k odběru služby Event Hubs událostí reagovat pro zachycení událostí souborů. Event Hubs může fungovat jako zdroj událostí nebo obslužnou rutinu události. Následující články popisují, jak používat Event Hubs jako zdroj.

|Název  |Popis  |
|---------|---------|
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří zachytávací soubor, služby Event Grid odešle událost pro aplikaci function app. Aplikace načte zachytávací soubor a migraci dat do datového skladu. |
| [Schéma událostí](event-schema-event-hubs.md) | Zobrazí pole v událostech služby Event Hubs. |

Příklady služby Event Hubs jako obslužná rutina, najdete v článku [obslužné rutiny služby Event Hubs](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Přihlášení k odběru služby IoT Hub událostí na zařízení se vytvořilo, odstraněna, připojené a odpojené události.

|Název  |Popis  |
|---------|---------|
| [Odeslání e-mailová oznámení o událostech služby Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky odešle e-mailové oznámení pokaždé, když zařízení do služby IoT Hub. |
| [Reakce na události služby IoT Hub s využitím služby Event Grid pro aktivaci akcí](../iot-hub/iot-hub-event-grid.md) | Přehled integrace služby IoT Hub s využitím služby Event Grid. |
| [Schéma událostí](event-schema-iot-hub.md) | Zobrazí pole ve službě IoT Hub události. |
| [Pořadí připojená zařízení a zařízení odpojí události](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Ukazuje, jak pořadí událostí stavu připojení zařízení. |

## <a name="media-services"></a>Media Services

Přihlásit k odběru služby Media Services události pro reakci na události stavu úloh.

|Název  |Popis  |
|---------|---------|
| [Přehled: reakce na události služby Media Services](../media-services/latest/reacting-to-media-services-events.md) | Přehled integrace služby Media Services pomocí služby Event Grid. |
| [Kurz: Směrování událostí služby Azure Media Services do vlastního webového koncového bodu pomocí rozhraní příkazového řádku](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak odesílat události ze služby Media Services. |
| [Schéma událostí](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zobrazí pole v událostech služby Media Services. |

## <a name="resource-groups"></a>Skupiny prostředků

Přihlášení odběru událostí skupiny prostředků reakce na změny v prostředky ve skupině prostředků.

|Název  |Popis  |
|---------|---------|
| [Kurz: monitorování změn virtuálního počítače pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny k virtuálnímu počítači a odešle e-mailů o těchto změnách. |
| [Azure CLI: přihlášení k odběru událostí skupiny prostředků](./scripts/event-grid-cli-resource-group.md)| Ukázkový skript, který se přihlásí odběru událostí skupiny prostředků. Odesílá události do Webhooku. |
| [Azure CLI: přihlášení k odběru událostí skupiny prostředků a filtr pro určitý prostředek](./scripts/event-grid-cli-resource-group-filter.md) | Ukázkový skript, který odebírá události pro skupinu prostředků a vyfiltruje události pro jeden prostředek. |
| [Prostředí PowerShell: přihlášení k odběru událostí skupiny prostředků](./scripts/event-grid-powershell-resource-group.md) | Ukázkový skript, který se přihlásí odběru událostí skupiny prostředků. Odesílá události do Webhooku. |
| [Prostředí PowerShell: přihlášení k odběru událostí skupiny prostředků a filtr pro určitý prostředek](./scripts/event-grid-powershell-resource-group-filter.md) | Ukázkový skript, který odebírá události pro skupinu prostředků a vyfiltruje události pro jeden prostředek. |
| [Šablony Resource Manageru: prostředků předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Se přihlásí k odběru událostí Azure předplatné nebo skupinu prostředků. Odesílá události do Webhooku. |
| [Schéma událostí](event-schema-resource-groups.md) | Zobrazí pole v prostředku skupiny událostí. |

## <a name="service-bus"></a>Service Bus

Přihlášení k odběru služby Service Bus událostí reagovat na zprávy bez aktivní naslouchací proces.

|Název  |Popis  |
|---------|---------|
| [Kurz: Azure Service Bus do příklady integrace služby Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus fungování aplikace a aplikaci logiky. |
| [Přehled: Azure Service Bus k integraci služby Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Přehled integrace služby Service Bus s využitím služby Event Grid. |
| [Schéma událostí](event-schema-service-bus.md) | Zobrazí pole v událostech služby Service Bus. |

## <a name="storage"></a>Storage

Přihlášení k odběru úložiště objektů Blob událostí reagovat na události vytvořené a odstraněných objektů blob.

|Název  |Popis  |
|---------|---------|
| [Rychlý start: Směrování událostí služby Blob storage do vlastního webového koncového bodu pomocí Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat rozhraní příkazového řádku Azure k odesílání událostí služby blob storage do Webhooku. |
| [Rychlý start: Směrování událostí služby Blob storage do vlastního webového koncového bodu pomocí Powershellu](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure PowerShell k odesílání událostí služby blob storage do Webhooku. |
| [Rychlý start: vytvoření a směrování událostí služby Blob storage pomocí webu Azure portal](blob-event-quickstart-portal.md) | Ukazuje, jak používat portál k odesílání událostí služby blob storage do Webhooku. |
| [Azure CLI: přihlášení k odběru událostí účtu úložiště objektů Blob](./scripts/event-grid-cli-blob.md) | Ukázkový skript, který se přihlásí odběru událostí účtu úložiště objektů Blob. Odešle událost Webhooku. |
| [Prostředí PowerShell: přihlášení k odběru událostí účtu úložiště objektů Blob](./scripts/event-grid-powershell-blob.md) | Ukázkový skript, který se přihlásí odběru událostí účtu úložiště objektů Blob. Odešle událost Webhooku. |
| [Šablony Resource Manageru: Vytvoření úložiště objektů Blob a předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. Odesílá události do Webhooku. |
| [Přehled: reakce na události služby Blob storage](../storage/blobs/storage-blob-event-overview.md) | Přehled integrace úložiště objektů Blob pomocí služby Event Grid. |
| [Schéma událostí](event-schema-blob-storage.md) | Zobrazí pole v události služby Blob Storage. |

## <a name="maps"></a>Maps
Přihlásit k odběru Azure Maps události pro reakci na události monitorové geografické zóny. Aplikace může například doručení e-mailové oznámení pokaždé, když zařízení přejde do nebo ji opustí monitorové geografické zóny.

|Název  |Popis  |
|---------|---------|
| [Reagujte na události Azure Maps pomocí služby Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled integrace Azure Maps pomocí služby Event Grid. |
| [Kurz: Nastavení monitorové geografické zóny](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Tento kurz vás provede kroky základní informace o nastavení monitorové geografické zóny s využitím map Azure. Pomocí Azure Event Grid streamování výsledků monitorové geografické zóny a nastavení oznámení na základě výsledků monitorové geografické zóny. |
| [Schéma událostí](event-schema-azure-maps.md) | Zobrazí pole v událostech Azure Maps. |


## <a name="next-steps"></a>Další postup

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
