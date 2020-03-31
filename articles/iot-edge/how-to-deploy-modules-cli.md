---
title: Nasazení modulů z příkazového řádku Rozhraní příkazového řádku Azure – Azure IoT Edge
description: Pomocí rozhraní příkazového příkazu Azure s rozšířením Azure IoT Extension můžete z vašeho iot hubu do zařízení IoT Edge nakonfigurovat ého nakonfigurováno pomocí manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 619ba7cb2d99e0137fd1834096dd5b66ffcd6ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240384"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Nasazení modulů Azure IoT Edge pomocí rozhraní příkazového příkazového příkazu Azure

Jakmile vytvoříte moduly IoT Edge s vaší obchodní logikou, chcete je nasadit do svých zařízení, aby fungovaly na hraničních zařízeních. Pokud máte více modulů, které spolupracují na shromažďování a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, která je spojují.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je nástroj příkazového řádku s otevřeným zdrojovým kódem pro různé platformy pro správu prostředků Azure, jako je IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služeb zřizování zařízení a propojené rozbočovače po vybalení. Nové rozšíření IoT obohacuje rozhraní příkazového odpovky Azure o funkce, jako je správa zařízení a úplná funkce IoT Edge.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON, pak pomocí tohoto souboru k nabízení nasazení zařízení IoT Edge. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete [v tématu Nasazení a monitorování modulů IoT Edge ve velkém měřítku.](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Požadavky

* Centrum [IoT v](../iot-hub/iot-hub-create-using-cli.md) předplacenéazure.
* [Zařízení IoT Edge](how-to-register-device.md#register-with-the-azure-cli) s nainstalovaným runtimem IoT Edge.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně vaše verze Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v [tématu Informace o tom, jak lze moduly IoT Edge používat, konfigurovat a znovu používat](module-composition.md).

Chcete-li nasadit moduly pomocí rozhraní příkazového příkazu Konto Azure, uložte manifest nasazení místně jako soubor JSON. Cestu k souboru použijete v další části při spuštění příkazu k použití konfigurace zařízení.

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

## <a name="deploy-to-your-device"></a>Nasazení zařízení

Moduly nasadíte do zařízení použitím manifestu nasazení, který jste nakonfigurovali s informacemi o modulu.

Změňte adresáře do složky, do které je uložen manifest nasazení. Pokud jste použili některou ze šablon VS Code `deployment.json` IoT Edge, použijte soubor ve `deployment.template.json` složce **konfigurace** adresáře řešení a ne souboru.

Pomocí následujícího příkazu použijte konfiguraci na zařízení IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametr ID zařízení rozlišuje malá a velká písmena. Parametr obsahu odkazuje na soubor manifestu nasazení, který jste uložili.

   ![výstup az iot edge set-modules výstup](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Zobrazení modulů v zařízení

Po nasazení modulů do zařízení je můžete všechny zobrazit pomocí následujícího příkazu:

Zobrazení modulů v zařízení IoT Edge:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametr ID zařízení rozlišuje malá a velká písmena.

   ![výstup seznamu az iot hub modul-identity](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
