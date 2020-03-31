---
title: Nasazení modulů ve velkém měřítku pomocí kódu Visual Studio – Azure IoT Edge
description: Pomocí rozšíření IoT pro kód Visual Studio můžete vytvořit automatická nasazení pro skupiny zařízení IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774130"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Nasazení modulů IoT Edge ve velkém měřítku pomocí kódu Visual Studia

Pomocí kódu Visual Studio Code můžete vytvořit **automatické nasazení IoT Edge** ke správě probíhajících nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) služby IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů do více zařízení. Můžete také sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete [v tématu Principy automatického nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V tomto článku nastavíte visual studio kód a rozšíření IoT. Pak se dozvíte, jak nasadit moduly do sady zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

* Centrum [IoT v](../iot-hub/iot-hub-create-through-portal.md) předplacenéazure.
* [Zařízení IoT Edge](how-to-register-device.md#register-with-visual-studio-code) s nainstalovaným runtimem IoT Edge.
* [Visual Studio kód](https://code.visualstudio.com/).
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) pro kód Visual Studia.

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlášení pro přístup k centru IoT hub

Rozšíření Azure IoT pro Visual Studio Code můžete použít k operacím s centrem. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat centrum IoT hub, na kterém pracujete.

1. V kódu Visual Studia otevřete zobrazení **Průzkumníka.**

1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub.**

1. Klikněte na **...** v záhlaví oddílu **Azure IoT Hub.** Pokud nevidíte tři tečky, najeďte nad záhlaví.

1. Zvolte **Vybrat centrum IoT .**

1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů k tomu.

1. Vyberte své předplatné Azure.

1. Vyberte svůj ioT hub.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit. Také popisuje, jak toky dat mezi moduly a požadované vlastnosti dvojčatmodulu. Další informace najdete v [tématu Naučte se nasazovat moduly a navazovat trasy v IoT Edge](module-composition.md).

Chcete-li nasadit moduly pomocí kódu sady Visual Studio, uložte manifest nasazení místně jako . Soubor JSON. Při spuštění příkazu budete muset zadat jeho umístění, abyste u zařízení použili konfiguraci.

Zde je základní manifest nasazení s jedním modulem jako příklad:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Pokud potřebujete určit, která zařízení IoT Edge můžete aktuálně nakonfigurovat, spusťte příkaz **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Identifikace zařízení s cílovými podmínkami

Chcete-li identifikovat zařízení IoT Edge, která mají přijímat nasazení, musíte zadat cílovou podmínku. Cílová podmínka je splněna, pokud jsou zadaná kritéria spárována s id zařízení, hodnotou značky nebo ohlášenou hodnotou vlastnosti.

Značky nakonfigurují v dvojčeti zařízení. Zde je příklad dvojčete zařízení, které má značky:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Toto zařízení obdrží nasazení, pokud cílová podmínka pro nasazení obsahuje výraz, který `tag.location.building = '20'`odpovídá jedné z hodnot značky, například .

Pokud chcete cílit na konkrétní zařízení bez ohledu na jeho `deviceId` značky nebo jiné hodnoty, stačí zadat cílovou podmínku.

Zde jsou některé další příklady:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' NEBO deviceId = 'linuxprod2' NEBO deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' A tags.location = 'westus2'
* tags.environment = 'prod' NEBO tags.location = 'westus2'
* tags.operator = 'John' And tags.environment = 'prod' A NOT deviceId = 'linuxprod1'

Podrobnosti naleznete [v cílové podmínce.](module-deployment-monitoring.md#target-condition) Další informace o dvojčata zařízení a značky, [najdete v tématu Principy a použití dvojčatzařízení v centru IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Úprava dvojčete zařízení

Můžete upravit dvojče zařízení v kódu Sady Visual Studio a nakonfigurovat značky. V nabídce **Zobrazení** vyberte **Paleta příkazů** a spusťte příkaz **IoT Edge: Edit Device Twin** . Vyberte zařízení IoT Edge a zobrazí se dvojče zařízení.

V tomto příkladu nebyly definovány žádné značky. Nahraďte aktuální `"tags": {}` prázdný oddíl vlastní definicí značek.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Po uložení místního souboru spusťte příkaz **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Vytvoření nasazení ve velkém měřítku

Po nakonfigurování manifestu nasazení a nakonfigurovaných značek v dvojčeti zařízení jste připraveni k nasazení.

1. Z nabídky **Zobrazení** vyberte **Paleta příkazů** a vyberte příkaz **Azure IoT Edge: Create Deployment at Scale** .

1. Přejděte na soubor JSON manifestu nasazení, který chcete použít, a klepněte na **tlačítko Vybrat manifest nasazení okraje**.

1. Zadejte hodnoty podle výzvy, počínaje **id nasazení**.

   ![Určení id nasazení](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Zadejte hodnoty pro tyto parametry:

  | Parametr | Popis |
  | --- | --- |
  | ID nasazení | Název nasazení, které bude vytvořeno v centru IoT. Pojmenujte nasazení jedinečný název, který je až 128 malá písmena. Vyhněte se mezerám `& ^ [ ] { } \ | " < > /`a následujícím neplatným znakům: . |
  | Cílová podmínka | Zadejte cílovou podmínku, která určí, která zařízení budou s tímto nasazením zaměřena.Podmínka je založena na značky dvojčete zařízení nebo dvojče zařízení hlášené vlastnosti a by měla odpovídat formátu výrazu.Například `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Priorita |  Kladné celé číslo. Pokud jsou dvě nebo více nasazení zaměřena na stejné zařízení, použije se nasazení s nejvyšší číselnou hodnotou priority. |

  Po zadání priority by měl terminál zobrazit výstup podobný následujícímu zobrazení:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorování a úpravy nasazení

Pomocí [azure cli](how-to-deploy-monitor-cli.md#monitor-a-deployment) nebo [portálu Azure](how-to-deploy-monitor.md#monitor-a-deployment) můžete monitorovat, upravovat a odstraňovat nasazení. Obě poskytují metriky na vaše nasazení.

## <a name="next-steps"></a>Další kroky

Další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
