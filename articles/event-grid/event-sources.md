---
title: Azure Event Grid zdroje událostí
description: Tento článek popisuje podporované zdroje událostí pro Azure Event Grid. Zdroj události je místo, kde dojde k události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 3a87475146e2b23c4a824e568ea3da33e1f4abf0
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512942"
---
# <a name="event-sources-in-azure-event-grid"></a>Zdroje událostí v Azure Event Grid

Zdroj události je místo, kde dojde k události. Několik služeb Azure se automaticky nakonfiguruje pro odesílání událostí. Můžete také vytvořit vlastní aplikace, které odesílají události. Vlastní aplikace nemusíte hostovat v Azure, aby bylo možné použít Event Grid pro distribuci událostí.

Tento článek obsahuje odkazy na obsah pro každý zdroj události.

## <a name="azure-subscriptions"></a>Předplatná Azure

Přihlaste se k odběru událostí předplatného Azure, abyste mohli reagovat na změny prostředků v rámci předplatného Azure

|Nadpis |Popis  |
|---------|---------|
| [Kurz: Azure Automation s využitím Event Grid a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost aktivuje Runbook služby Automation, který zaznamená virtuální počítač, a aktivuje zprávu odeslanou kanálu Microsoft Teams. |
| [Postupy: přihlášení k odběru událostí prostřednictvím portálu](subscribe-through-portal.md) | Použijte portál k přihlášení k odběru událostí pro předplatné Azure. |
| [Azure CLI: přihlášení k odběru událostí pro předplatné Azure](./scripts/event-grid-cli-azure-subscription.md) |Ukázkový skript, který vytvoří předplatné služby Event Grid k předplatnému Azure a odesílá události do Webhooku. |
| [PowerShell: přihlášení k odběru událostí pro předplatné Azure](./scripts/event-grid-powershell-azure-subscription.md)| Ukázkový skript, který vytvoří předplatné služby Event Grid k předplatnému Azure a odesílá události do Webhooku. |
| [Schéma událostí](event-schema-subscriptions.md) | Zobrazuje pole v událostech předplatného Azure. |

## <a name="container-registry"></a>Container Registry

Přihlaste se k odběru Container Registrych událostí, aby odpovídaly změnám v obrázcích.

|Nadpis |Popis  |
|---------|---------|
| [Rychlý Start: odeslání událostí registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k posílání Container Registrych událostí. |
| [Schéma událostí](event-schema-container-registry.md) | Zobrazuje pole v Container Registrych událostech. |

## <a name="custom-topics"></a>Vlastní témata

Přihlaste se k odběru vlastních témat, která reagují na události aplikace.

|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: vytvoření a směrování vlastních událostí pomocí Azure CLI](custom-event-quickstart.md) | Ukazuje, jak používat Azure CLI k posílání vlastních událostí. |
| [Rychlý Start: vytvoření a směrování vlastních událostí pomocí Azure PowerShell](custom-event-quickstart-powershell.md) | Ukazuje, jak používat Azure PowerShell k odesílání vlastních událostí. |
| [Rychlý Start: vytvoření a směrování vlastních událostí pomocí Azure Portal](custom-event-quickstart-portal.md) | Ukazuje, jak používat portál k posílání vlastních událostí. |
| [Rychlý Start: směrování vlastních událostí do Azure Queue Storage](custom-event-to-queue-storage.md) | Popisuje, jak odesílat vlastní události do úložiště fronty. |
| [Postupy: odeslání do vlastního tématu](post-to-custom-topic.md) | Ukazuje, jak publikovat událost do vlastního tématu. |
| [Azure CLI: vytvoření Event Grid vlastního tématu](./scripts/event-grid-cli-create-custom-topic.md)|Vzorový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [Azure CLI: přihlášení k odběru událostí pro vlastní téma](./scripts/event-grid-cli-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události do Webhooku.|
| [PowerShell: vytvoření Event Grid vlastního tématu](./scripts/event-grid-powershell-create-custom-topic.md)|Vzorový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [PowerShell: přihlášení k odběru událostí vlastního tématu](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události do Webhooku.|
| [Správce prostředků Šablona: vlastní téma a koncový bod Webhooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Správce prostředků šablonu, která vytvoří vlastní téma a předplatné pro toto vlastní téma. Odesílá události do Webhooku. |
|
| [Správce prostředků Šablona: vlastní téma a koncový bod Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Správce prostředků šablonu, která vytvoří odběr pro vlastní téma. Odesílá události do Azure Event Hubs. |
| [Schéma událostí](event-schema.md) | Zobrazuje pole ve vlastních událostech. |

## <a name="event-hubs"></a>Event Hubs

Přihlaste se k odběru Event Hubsch událostí, aby odpovídaly událostem zachycení souboru. Event Hubs může fungovat buď jako zdroj události, nebo jako obslužná rutina události. Následující články ukazují, jak použít Event Hubs jako zdroj.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří zachytávací soubor, Event Grid pošle událost do aplikace Function App. Aplikace načte zachytávací soubor a migruje data do datového skladu. |
| [Schéma událostí](event-schema-event-hubs.md) | Zobrazuje pole v Event Hubsch událostech. |

Příklady Event Hubs jako obslužné rutiny naleznete v tématu [obslužná rutina Event Hubs](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Centrum IoT

Přihlaste se k odběru událostí IoT Hub pro reakci na události vytvoření, odstranění, připojení, odpojení a telemetrie zařízení.

|Nadpis  |Popis  |
|---------|---------|
| [Posílání e-mailových oznámení o událostech Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky pošle e-mailové oznámení pokaždé, když se do vašeho IoT Hub přidá zařízení. |
| [Reakce na události IoT Hub pomocí Event Grid k aktivaci akcí](../iot-hub/iot-hub-event-grid.md) | Přehled integrace IoT Hub s Event Grid |
| [Schéma událostí](event-schema-iot-hub.md) | Zobrazuje pole v IoT Hubch událostech. |
| [Objednat události připojené k zařízení a odpojené zařízení](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Ukazuje, jak objednat události stavu připojení zařízení. |

## <a name="key-vault-preview"></a>Key Vault (Preview)

Key Vault integrace s Event Grid je momentálně ve verzi Preview. 

Přihlaste se k odběru Key Vaultch událostí, které budou upozorňovány na vypršení platnosti tajného kódu, vypršení platnosti tajného klíče nebo pro tajný kód je k dispozici nová verze. 

|Nadpis  |Popis  |
|---------|---------|
| [Monitorování událostí Key Vault s využitím Azure Event Grid](../key-vault/event-grid-overview.md) | Přehled integrace Key Vault s Event Grid |
| [Kurz: vytváření a sledování událostí Key Vault s využitím Event Grid](../key-vault/event-grid-tutorial.md) | Přečtěte si, jak nastavit oznámení Event Grid pro Key Vault. |
| [Schéma událostí](event-schema-key-vault.md) | Zobrazuje pole v Key Vaultch událostech. |

## <a name="media-services"></a>Media Services

Přihlaste se k odběru událostí Media Services, aby odpovídaly událostem stavu úlohy.

|Nadpis  |Popis  |
|---------|---------|
| [Přehled: reakce na události Media Services](../media-services/latest/reacting-to-media-services-events.md) | Přehled integrace Media Services s Event Grid |
| [Kurz: směrování událostí Azure Media Services do vlastního webového koncového bodu pomocí rozhraní příkazového řádku](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak odesílat události z Media Services. |
| [Schéma událostí](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zobrazuje pole v Media Servicesch událostech. |

## <a name="resource-groups"></a>Skupiny prostředků

Přihlaste se k odběru událostí skupiny prostředků, aby odpovídaly změnám v prostředcích v rámci skupiny prostředků.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: monitorování změn virtuálních počítačů pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny virtuálního počítače a odesílá e-maily o těchto změnách. |
| [Azure CLI: přihlášení k odběru událostí pro skupinu prostředků](./scripts/event-grid-cli-resource-group.md)| Ukázkový skript, který se přihlašuje k odběru událostí pro skupinu prostředků. Odesílá události do Webhooku. |
| [Azure CLI: přihlášení k odběru událostí pro skupinu prostředků a filtrování prostředku](./scripts/event-grid-cli-resource-group-filter.md) | Ukázkový skript, který přihlašuje události pro skupinu prostředků a filtruje události pro jeden prostředek. |
| [PowerShell: přihlášení k odběru událostí pro skupinu prostředků](./scripts/event-grid-powershell-resource-group.md) | Ukázkový skript, který se přihlašuje k odběru událostí pro skupinu prostředků. Odesílá události do Webhooku. |
| [PowerShell: přihlášení k odběru událostí pro skupinu prostředků a filtrování prostředku](./scripts/event-grid-powershell-resource-group-filter.md) | Ukázkový skript, který přihlašuje události pro skupinu prostředků a filtruje události pro jeden prostředek. |
| [Správce prostředků Šablona: odběr prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Přihlašuje se k odběru událostí předplatného Azure nebo skupiny prostředků. Odesílá události do Webhooku. |
| [Schéma událostí](event-schema-resource-groups.md) | Zobrazí pole v událostech skupiny prostředků. |

## <a name="service-bus"></a>Service Bus

Přihlaste se k odběru Service Busch událostí, aby reagovaly na zprávy bez aktivního naslouchacího procesu.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: Azure Service Bus pro Azure Event Grid příklady integrace](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid posílá zprávy z Service Bus tématu do aplikace Function App a Logic Apps. |
| [Přehled: Azure Service Bus pro Event Grid integraci](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Přehled integrace Service Bus s Event Grid |
| [Schéma událostí](event-schema-service-bus.md) | Zobrazuje pole v Service Busch událostech. |

## <a name="storage"></a>Storage

Přihlaste se k odběru událostí Blob Storage pro reakci na události vytvořené a odstraněné objekty blob.

|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: směrování událostí služby Blob Storage do vlastního webového koncového bodu pomocí Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k posílání událostí služby Blob Storage do Webhooku. |
| [Rychlý Start: směrování událostí služby Blob Storage do vlastního webového koncového bodu pomocí prostředí PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak použít Azure PowerShell k odeslání událostí služby Blob Storage do Webhooku. |
| [Rychlý Start: vytváření a směrování událostí služby Blob Storage pomocí Azure Portal](blob-event-quickstart-portal.md) | Ukazuje, jak používat portál k posílání událostí služby Blob Storage do Webhooku. |
| [Azure CLI: přihlášení k odběru událostí pro účet úložiště objektů BLOB](./scripts/event-grid-cli-blob.md) | Ukázkový skript, který se přihlásí k odběru události pro účet úložiště objektů BLOB. Událost pošle Webhooku. |
| [PowerShell: přihlášení k odběru událostí pro účet úložiště objektů BLOB](./scripts/event-grid-powershell-blob.md) | Ukázkový skript, který se přihlásí k odběru události pro účet úložiště objektů BLOB. Událost pošle Webhooku. |
| [Správce prostředků Šablona: vytvoření úložiště objektů BLOB a předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. Odesílá události do Webhooku. |
| [Přehled: reakce na události služby Blob Storage](../storage/blobs/storage-blob-event-overview.md) | Přehled integrace služby Blob Storage s Event Grid. |
| [Schéma událostí](event-schema-blob-storage.md) | Zobrazuje pole v Blob Storagech událostech. |

## <a name="maps"></a>Mapy
Přihlaste se k odběru událostí Azure Maps pro reakci na události geografické ploty. Aplikace může například doručovat e-mailové oznámení pokaždé, když zařízení vstoupí do geografické zóny nebo je ukončí.

|Nadpis  |Popis  |
|---------|---------|
| [Reakce na události Azure Maps pomocí Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled Integrace Azure Maps s Event Grid |
| [Kurz: nastavení geografického plotu](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | V tomto kurzu se seznámíte se základními kroky pro nastavení geografického plotu pomocí Azure Maps. Použijete Azure Event Grid ke streamování výsledků geografické plotu a nastavení oznámení na základě výsledků geografické ploty. |
| [Schéma událostí](event-schema-azure-maps.md) | Zobrazuje pole v Azure Mapsch událostech. |

## <a name="app-configuration"></a>App Configuration
Přihlaste se k odběru událostí konfigurace aplikace Azure a odpovězte na události změny klíč-hodnota.

|Nadpis | Popis |
|---------|---------|
| [Reakce na události konfigurace aplikace Azure pomocí Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled integrace konfigurace aplikace Azure s Event Grid. |
| [Rychlý Start: směrování událostí konfigurace aplikací Azure do vlastního webového koncového bodu pomocí Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k posílání událostí konfigurace aplikace Azure do Webhooku. |
| [Schéma událostí](event-schema-app-configuration.md) | Zobrazuje pole v událostech konfigurace aplikace Azure. |

## <a name="azure-signalr"></a>Azure SignalR
Přihlaste se k odběru událostí služby signalizace Azure, aby reagovaly na události připojení klienta.

|Nadpis | Popis |
|---------|---------|
| [Reakce na události služby signalizace Azure pomocí Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Přehled integrace služby Azure Signaler pomocí Event Grid. |
| [Odeslání událostí služby signalizace Azure do Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Ukazuje, jak odesílat události služby signalizace Azure do aplikace prostřednictvím Event Grid. |
| [Schéma událostí](event-schema-azure-signalr.md) | Zobrazuje pole v událostech služby signalizace Azure. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Přihlaste se k odběru Azure Machine Learning událostí pracovního prostoru, aby odpovídaly registraci modelu.

| Nadpis | Popis |
| ----- | ----- |
| [Využívání Azure Machine Learningch událostí](../machine-learning/concept-event-grid-integration.md) | Přehled Integrace Azure Machine Learning s Event Grid |
| [Azure Event Grid schéma událostí pro Azure Machine Learning](event-schema-machine-learning.md) | Zobrazí pole v událostech Azure Machine Learning. |

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
