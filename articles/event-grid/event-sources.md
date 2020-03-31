---
title: Zdroje událostí azure event grid
description: Předplatná Azure, registr kontejnerů, vlastní témata, centra událostí, ioT hub, trezor klíčů, mediální služby, skupiny prostředků, service bus, úložiště, mapy, konfigurace aplikací, signál R, strojové učení.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264996"
---
# <a name="event-sources-in-azure-event-grid"></a>Zdroje událostí v Azure Event Grid

Zdroj události je místo, kde dojde k události. Několik služeb Azure se automaticky nakonfiguruje k odesílání událostí. Můžete také vytvořit vlastní aplikace, které odesílají události. Vlastní aplikace nemusí být hostované v Azure, aby bylo možné používat Event Grid pro distribuci událostí.

Tento článek obsahuje odkazy na obsah pro každý zdroj událostí.

## <a name="azure-subscriptions"></a>Předplatná Azure

Přihlaste se k odběru událostí předplatného Azure a reagujte na změny prostředků v rámci předplatného Azure.

|Nadpis |Popis  |
|---------|---------|
| [Kurz: Azure Automation s Grid událostí a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Vytvořte virtuální počítač, který odešle událost. Událost aktivuje runbook automatizace, který označí virtuální počítač a aktivuje zprávu, která je odeslána do kanálu Microsoft Teams. |
| [Jak: přihlásit se k odběru událostí prostřednictvím portálu](subscribe-through-portal.md) | Pomocí portálu se můžete přihlásit k odběru událostí pro předplatné Azure. |
| [Vyjednané zúžení azure: přihlášení k odběru událostí pro předplatné Azure](./scripts/event-grid-cli-azure-subscription.md) |Ukázkový skript, který vytvoří předplatné služby Event Grid pro předplatné Azure a odešle události do WebHooku. |
| [PowerShell: přihlášení k odběru událostí pro předplatné Azure](./scripts/event-grid-powershell-azure-subscription.md)| Ukázkový skript, který vytvoří předplatné služby Event Grid pro předplatné Azure a odešle události do WebHooku. |
| [Schéma události](event-schema-subscriptions.md) | Zobrazuje pole v událostech předplatného Azure. |

## <a name="container-registry"></a>Container Registry

Přihlaste se k odběru událostí registru kontejnerů a reagujte na změny v obrázcích.

|Nadpis |Popis  |
|---------|---------|
| [Úvodní příručka: Odeslání událostí registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k odesílání událostí registru kontejnerů. |
| [Schéma události](event-schema-container-registry.md) | Zobrazí pole v událostech registru kontejnerů. |

## <a name="custom-topics"></a>Vlastní témata

Přihlaste se k odběru vlastních témat, abyste reagovali na události aplikace.

|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: vytváření a směrování vlastních událostí pomocí azure CLI](custom-event-quickstart.md) | Ukazuje, jak používat Azure CLI k odesílání vlastních událostí. |
| [Úvodní příručka: vytváření a směrování vlastních událostí pomocí Azure PowerShellu](custom-event-quickstart-powershell.md) | Ukazuje, jak používat Azure PowerShell k odesílání vlastních událostí. |
| [Úvodní příručka: vytváření a směrování vlastních událostí pomocí portálu Azure](custom-event-quickstart-portal.md) | Ukazuje, jak pomocí portálu odesílat vlastní události. |
| [Úvodní příručka: směrování vlastních událostí do úložiště Azure Queue](custom-event-to-queue-storage.md) | Popisuje, jak odeslat vlastní události do úložiště fronty. |
| [Jak: příspěvek na vlastní téma](post-to-custom-topic.md) | Ukazuje, jak zveřejnit událost do vlastního tématu. |
| [Nastavení příkazu k řešení Azure: vytvoření vlastního tématu gridu událostí](./scripts/event-grid-cli-create-custom-topic.md)|Ukázkový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [Nastavení příkazu k příkazu Azure: přihlášení k odběru událostí pro vlastní téma](./scripts/event-grid-cli-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události webhooku.|
| [PowerShell: vytvoření vlastního tématu event gridu](./scripts/event-grid-powershell-create-custom-topic.md)|Ukázkový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [PowerShell: Přihlášení k odběru událostí pro vlastní téma](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události webhooku.|
| [Šablona Správce prostředků: vlastní téma a koncový bod WebHooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Šablona Správce prostředků, která vytvoří vlastní téma a předplatné pro toto vlastní téma. Odesílá události webhooku. |
|
| [Šablona Správce prostředků: vlastní téma a koncový bod Centra událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Šablona Správce prostředků, která vytvoří předplatné pro vlastní téma. Odesílá události do Centra událostí Azure. |
| [Schéma události](event-schema.md) | Zobrazuje pole ve vlastních událostech. |

## <a name="event-hubs"></a>Event Hubs

Přihlaste se k odběru událostí Event Hubs a reagujte na události sběru souborů. Centra událostí mohou fungovat jako zdroj událostí nebo obslužná rutina události. Následující články ukazují, jak používat Centra událostí jako zdroj.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Centra událostí vytvoří soubor pro digitalizaci, event grid odešle událost do aplikace funkce. Aplikace načte soubor capture a migruje data do datového skladu. |
| [Schéma události](event-schema-event-hubs.md) | Zobrazuje pole v událostech Event Hubs. |

Příklady event hubů jako obslužné rutiny naleznete v tématu [Obslužná rutina centra událostí](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Přihlaste se k odběru událostí služby IoT Hub a reagujte na události vytvořené, odstraněné, připojené, odpojené a telemetrické události zařízení.

|Nadpis  |Popis  |
|---------|---------|
| [Posílání e-mailových oznámení o událostech Azure IoT Hub pomocí Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikace logiky odešle e-mail s oznámením pokaždé, když se zařízení přidá do vašeho IoT Hubu. |
| [Reakce na události služby IoT Hub pomocí služby Event Grid ke spuštění akcí](../iot-hub/iot-hub-event-grid.md) | Přehled integrace IoT Hubu s Event Grid. |
| [Schéma události](event-schema-iot-hub.md) | Zobrazuje pole v událostech ioT hubu. |
| [Objednejte připojení zařízení a odpojené události zařízení](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Ukazuje, jak objednat události stavu připojení zařízení. |

## <a name="key-vault-preview"></a>Trezor klíčů (náhled)

Integrace trezoru klíčů s mřížkou událostí je aktuálně ve verzi preview. 

Přihlaste se k odběru událostí trezoru klíčů, které mají být upozorňovány, když platnost tajného klíče vyprší, platnost tajného klíče nebo má tajný klíč k dispozici novou verzi. 

|Nadpis  |Popis  |
|---------|---------|
| [Monitorování událostí trezoru klíčů pomocí Azure Event Grid](../key-vault/event-grid-overview.md) | Přehled integrace trezoru klíčů s mřížkou událostí. |
| [Kurz: Vytváření a sledování událostí trezoru klíčů pomocí mřížky událostí](../key-vault/event-grid-tutorial.md) | Přečtěte si, jak nastavit oznámení mřížky událostí pro trezor klíčů. |
| [Schéma události](event-schema-key-vault.md) | Zobrazuje pole v událostech trezoru klíčů. |

## <a name="media-services"></a>Media Services

Přihlaste se k odběru událostí služby Media Services a odhlaste se na události stavu úlohy.

|Nadpis  |Popis  |
|---------|---------|
| [Přehled: reakce na události služby Media Services](../media-services/latest/reacting-to-media-services-events.md) | Přehled integrace mediálních služeb s event gridem. |
| [Kurz: směrování událostí Mediálníslužby Azure do vlastního webového koncového bodu pomocí příkazového příkazového příkazu](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak odesílat události ze služby Media Services. |
| [Schéma události](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zobrazuje pole v událostech služby Media Services. |

## <a name="resource-groups"></a>Skupiny prostředků

Přihlaste se k odběru událostí skupiny prostředků a reagujte na změny prostředků v rámci skupiny prostředků.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: sledování změn virtuálních strojů pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny ve virtuálním počítači a odesílá e-maily o těchto změnách. |
| [Nastavení příkazu k příkazu Azure: přihlášení k odběru událostí pro skupinu prostředků](./scripts/event-grid-cli-resource-group.md)| Ukázkový skript, který se přihlásí k odběru událostí pro skupinu prostředků. Odesílá události webhooku. |
| [Azure CLI: Přihlásit se k odběru událostí pro skupinu prostředků a filtrovat prostředek](./scripts/event-grid-cli-resource-group-filter.md) | Ukázkový skript, který se přihlásí k odběru událostí pro skupinu prostředků a filtruje události pro jeden prostředek. |
| [PowerShell: přihlášení k odběru událostí pro skupinu prostředků](./scripts/event-grid-powershell-resource-group.md) | Ukázkový skript, který se přihlásí k odběru událostí pro skupinu prostředků. Odesílá události webhooku. |
| [PowerShell: Přihlášení k odběru událostí pro skupinu prostředků a filtrování prostředků](./scripts/event-grid-powershell-resource-group-filter.md) | Ukázkový skript, který se přihlásí k odběru událostí pro skupinu prostředků a filtruje události pro jeden prostředek. |
| [Šablona Správce prostředků: odběr prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Přihlásí se k odběru událostí pro předplatné Azure nebo skupinu prostředků. Odesílá události webhooku. |
| [Schéma událostí](event-schema-resource-groups.md) | Zobrazuje pole v událostech skupiny prostředků. |

## <a name="service-bus"></a>Service Bus

Přihlásit se k události Service Bus reagovat na zprávy bez aktivní houskovací proces.

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: Příklady integrace Azure Service Bus do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus do funkční aplikace a aplikace logiky. |
| [Přehled: Integrace Azure Service Bus do gridu událostí](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Přehled integrace sběrnice Service Bus s událostí Grid. |
| [Schéma události](event-schema-service-bus.md) | Zobrazí pole v událostech služby Service Bus. |

## <a name="storage"></a>Úložiště

Přihlaste se k odběru událostí blob Storage a reagujte na události vytvořené a odstraněné objekty objektů blob.

>[!NOTE]
> Pouze účty úložiště druhu **StorageV2 (pro obecné účely v2)** a Integrace událostí podpory **BlobStorage.** **Úložiště (pro obecné účely v1)** *nepodporuje* integraci s Event Grid.

|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: směrování událostí úložiště objektů Blob do vlastního webového koncového bodu pomocí azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k odesílání událostí úložiště objektů blob na WebHook. |
| [Úvodní příručka: směrování událostí úložiště objektů Blob do vlastního webového koncového bodu pomocí Prostředí PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure PowerShell k odesílání událostí úložiště objektů blob na WebHook. |
| [Úvodní příručka: vytváření a směrování událostí úložiště objektů Blob pomocí portálu Azure](blob-event-quickstart-portal.md) | Ukazuje, jak pomocí portálu odesílat události úložiště objektů blob na WebHook. |
| [Nastavení příkazu k řešení Azure: přihlášení k odběru událostí pro účet úložiště objektů Blob](./scripts/event-grid-cli-blob.md) | Ukázkový skript, který se přihlásí k odběru události pro účet úložiště objektů Blob. Odešle událost webhooku. |
| [PowerShell: Přihlášení k odběru událostí pro účet úložiště objektů Blob](./scripts/event-grid-powershell-blob.md) | Ukázkový skript, který se přihlásí k odběru události pro účet úložiště objektů Blob. Odešle událost webhooku. |
| [Šablona Správce prostředků: Vytvoření úložiště objektů Blob a předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. Odesílá události webhooku. |
| [Přehled: Reakce na události úložiště objektů Blob](../storage/blobs/storage-blob-event-overview.md) | Přehled integrace úložiště objektů Blob s událostí. |
| [Schéma události](event-schema-blob-storage.md) | Zobrazuje pole v událostech úložiště objektů blob. |

## <a name="maps"></a>Maps
Přihlaste se k odběru událostí Azure Maps a reagujte na události geografické zóny. Aplikace může například doručit e-mailové oznámení pokaždé, když zařízení vstoupí nebo ukončí geofence.

|Nadpis  |Popis  |
|---------|---------|
| [Reakce na události Azure Maps pomocí Gridu událostí](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled integrace Map Azure s Event Grid. |
| [Kurz: Nastavení geografické zóny](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Tento kurz vás provede základními kroky nastavení geografické zóny pomocí Azure Maps. Azure Event Grid slouží k streamování výsledků geografické zóny a nastavení oznámení na základě výsledků geografické zóny. |
| [Schéma události](event-schema-azure-maps.md) | Zobrazuje pole v událostech Azure Maps. |

## <a name="app-configuration"></a>App Configuration
Přihlaste se k odběru událostí konfigurace aplikace Azure a reagujte na události změny hodnoty klíče.

|Nadpis | Popis |
|---------|---------|
| [Reakce na události konfigurace aplikace Azure pomocí gridu událostí](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled integrace konfigurace aplikací Azure s událostí gridu. |
| [Úvodní příručka: směrování událostí konfigurace aplikací Azure do vlastního webového koncového bodu pomocí azure cli](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak pomocí azure cli k odeslání událostí konfigurace aplikací Azure na WebHook. |
| [Schéma události](event-schema-app-configuration.md) | Zobrazuje pole v událostech konfigurace aplikací Azure. |

## <a name="azure-signalr"></a>Azure SignalR
Přihlaste se k odběru událostí služby Azure SignalR service a reagujte na události připojení klienta.

|Nadpis | Popis |
|---------|---------|
| [Reakce na události služby Azure SignalR pomocí mřížky událostí](../azure-signalr/signalr-concept-event-grid-integration.md) | Přehled integrace služby Azure SignalR s gridem událostí. |
| [Jak odeslat události služby Azure SignalR do mřížky událostí](../azure-signalr/signalr-howto-event-grid-integration.md) | Ukazuje, jak odeslat události služby Azure SignalR do aplikace prostřednictvím služby Event Grid. |
| [Schéma události](event-schema-azure-signalr.md) | Zobrazuje pole v událostech služby Azure SignalR. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Přihlaste se k odběru událostí pracovního prostoru Azure Machine Learning, abyste reagovali na registraci modelu.

| Nadpis | Popis |
| ----- | ----- |
| [Využití událostí Azure Machine Learning](../machine-learning/concept-event-grid-integration.md) | Přehled integrace Azure Machine Learning s Event Grid. |
| [Schéma událostí Azure Event Grid pro Azure Machine Learning](event-schema-machine-learning.md) | Zobrazuje pole v událostech Azure Machine Learning. |

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
