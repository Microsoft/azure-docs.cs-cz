---
title: Nasazení modulů ve velkém měřítku pomocí Visual Studio Code-Azure IoT Edge
description: K vytvoření automatických nasazení pro skupiny IoT Edge zařízení použijte rozšíření IoT pro Visual Studio Code.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7f6e90edc0503326dc9dbb06abfcf59fa2d51e1e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043812"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Nasazení IoT Edgech modulů ve velkém měřítku pomocí Visual Studio Code

**Automatické nasazení IoT Edge** můžete vytvořit pomocí Visual Studio Code ke správě probíhajících nasazení v mnoha zařízeních najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](../iot-hub/iot-hub-automatic-device-management.md) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení. Můžete také sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V tomto článku jste nastavili Visual Studio Code a rozšíření IoT. Pak se dozvíte, jak nasadit moduly do sady IoT Edgech zařízení.

## <a name="prerequisites"></a>Požadované součásti

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* Nejméně jedno IoT Edge zařízení.

  Pokud nemáte nastavené zařízení IoT Edge, můžete ho vytvořit na virtuálním počítači Azure. Použijte postup v jednom z článků rychlý Start k [Vytvoření virtuálního zařízení](quickstart-linux.md) se systémem Linux nebo [Vytvoření virtuálního zařízení s Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) pro Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se, abyste měli přístup ke službě IoT Hub.

Pomocí rozšíření Azure IoT pro Visual Studio Code můžete provádět operace s rozbočovačem. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat službu IoT Hub, na které pracujete.

1. V Visual Studio Code otevřete zobrazení **Průzkumníka** .

1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud se tři tečky nevidí, najeďte myší na záhlaví.

1. Zvolte **vybrat IoT Hub**.

1. Pokud nejste přihlášení k účtu Azure, postupujte podle pokynů.

1. Vyberte své předplatné Azure.

1. Vyberte centrum IoT.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit. Popisuje také způsob, jakým datové toky mezi moduly a požadované vlastnosti jsou v modulu vlákna. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

Chcete-li nasadit moduly pomocí Visual Studio Code, uložte manifest nasazení místně jako. Soubor JSON. Když spustíte příkaz pro použití konfigurace na zařízení, budete muset zadat jeho umístění.

Tady je základní manifest nasazení s jedním modulem jako příklad:

>[!NOTE]
>Tento ukázkový manifest nasazení používá schéma verze 1,1 pro agenta IoT Edge a centrum. Verze schématu 1,1 byla vydána společně s IoT Edge verze 1.0.10 a umožňuje funkce, jako je pořadí spouštění modulu a stanovení priorit směrování.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
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
              "version": "1.1",
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

Pokud potřebujete určit, která IoT Edge zařízení můžete konfigurovat, spusťte příkaz **IoT Edge: získat informace o zařízení** .

## <a name="identify-devices-with-target-conditions"></a>Identifikace zařízení s cílovými podmínkami

Chcete-li identifikovat IoT Edge zařízení, která mají přijmout nasazení, je nutné zadat cílovou podmínku. Podmínka cíle je splněna, pokud zadaná kritéria odpovídají typu deviceId, hodnota značky nebo hodnota hlášené vlastnosti.

V zařízení nakonfigurujete značky na vlákna. Tady je příklad typu vlákna zařízení, které má značky:

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

Toto zařízení dostane nasazení, pokud cílová podmínka pro nasazení obsahuje výraz, který odpovídá jedné z hodnot značky, jako je například `tag.location.building = '20'` .

Pokud chcete cílit na konkrétní zařízení bez ohledu na jeho značky nebo jiné hodnoty, stačí zadat `deviceId` pro cílovou podmínku.

Tady jsou některé další příklady:

* deviceId = ' linuxprod1 '
* deviceId = ' linuxprod1 ' nebo deviceId = ' linuxprod2 ' nebo deviceId = ' linuxprod3 '
* Tags. Environment = ' prod '
* Tags. Environment = ' prod ' a Tags. Location = ' westus2 '
* Tags. Environment = ' prod ' nebo Tags. Location = ' westus2 '
* Tags. operator = Jan a Tags. Environment = ' prod ' a NOT deviceId = ' linuxprod1 '

Podrobnosti najdete v tématu [cílová podmínka](module-deployment-monitoring.md#target-condition) . Další informace o tom, jaké jsou vlákna a značky zařízení, najdete [v tématu pochopení a používání nevláken zařízení v IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Upravit zdvojené zařízení

Chcete-li konfigurovat značky, můžete upravit stav vlákna zařízení v Visual Studio Code. V nabídce **zobrazení** vyberte možnost **paleta příkazů** a spusťte příkaz **IoT Edge: upravit** dodaný zařízení. Vyberte zařízení IoT Edge a zobrazí se zařízení, které se zobrazí.

V tomto příkladu nebyly definovány žádné značky. Nahraďte aktuální prázdný oddíl `"tags": {}` vlastní definicí značek.

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

Po uložení místního souboru spusťte příkaz **IoT Edge: Update** dodaný se zařízením.

## <a name="create-deployment-at-scale"></a>Škálování pro vytváření nasazení

Po dokončení konfigurace manifestu nasazení a nakonfigurované značky v zařízení s dvojitou tímu jste připraveni k nasazení.

1. V nabídce **zobrazení** vyberte **paleta příkazů** a vyberte **Azure IoT Edge: vytvořit nasazení** pomocí příkazu škálovat.

1. Přejděte do souboru JSON manifestu nasazení, který chcete použít, a klikněte na **Vybrat manifest nasazení Edge**.

1. Zadejte hodnoty jako výzvy, počínaje **ID nasazení**.

   ![Zadat ID nasazení](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Zadejte hodnoty pro tyto parametry:

  | Parametr | Popis |
  | --- | --- |
  | ID nasazení | Název nasazení, které se vytvoří ve službě IoT Hub. Dejte vašemu nasazení jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /` . |
  | Cílová podmínka | Zadejte cílovou podmínku pro určení, která zařízení budou cílem tohoto nasazení.Podmínka je založena na nevyhovujících značkách zařízení nebo na dohlášených vlastnostech zařízení, které se musí shodovat s formátem výrazu.Například `tags.environment='test' and properties.reported.devicemodel='4000x'` . |
  | Priorita |  Kladné celé číslo. Pokud jsou na jednom zařízení cílené dva nebo více nasazení, uplatní se nasazení s nejvyšší číselnou hodnotou priority. |

  Po zadání priority by měl terminál zobrazit výstup podobný následujícímu znázornění:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorování a úpravy nasazení

K monitorování, úpravám a odstraňování nasazení použijte [Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) nebo rozhraní příkazového [řádku Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) . Oba poskytují metriky pro vaše nasazení.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).