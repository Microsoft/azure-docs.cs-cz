---
title: Nasazení modulů z příkazového řádku Azure CLI – Azure IoT Edge
description: Pomocí rozhraní příkazového řádku Azure s rozšířením Azure IoT nahrajte modul IoT Edge z IoT Hub do zařízení IoT Edge, jak je nakonfigurované v manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e93360d4045f9c97d45abe2af489804a4c3c85f0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673498"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Nasadit moduly Azure IoT Edge pomocí Azure CLI

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby device provisioning a propojená centra úprav. Nové rozšíření IoT vylepšuje Azure CLI s funkcemi, jako je Správa zařízení a úplné možnosti služby IoT Edge.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON a pak jej použít pro vložení nasazení do zařízení IoT Edge. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete v tématu [nasazení a sledování IoT Edgech modulů ve velkém měřítku](how-to-deploy-monitor-cli.md) .

## <a name="prerequisites"></a>Předpoklady

* [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* [IoT Edge zařízení](how-to-register-device.md#register-with-the-azure-cli) s nainstalovaným modulem runtime IoT Edge.
* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.

Můžete nasadit moduly pomocí Azure CLI, uložte jako soubor .json místně manifest nasazení. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení.

Tady je manifest základní nasazení s jeden modul jako příklad:

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

Moduly nasadíte do zařízení s použitím manifestu nasazení, který jste nakonfigurovali pomocí informací o modulech.

Změňte adresář na složku, ve kterém je uložený manifestu nasazení. Pokud jste použili jednu z šablon VS Code IoT Edge, použijte `deployment.json` soubor ve složce **config** adresáře řešení, a ne soubor `deployment.template.json`.

Chcete-li použít konfiguraci pro zařízení IoT Edge, použijte následující příkaz:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametr ID zařízení rozlišuje velká a malá písmena. Obsahu parametr odkazuje na nasazení manifestu soubor, který jste uložili.

   ![AZ iot edge sada modulů výstupu](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich pomocí následujícího příkazu:

Zobrazení modulů v zařízení IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametr ID zařízení rozlišuje velká a malá písmena.

   ![AZ iot hub modul identity výstupu](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat a monitorovat IoT Edge moduly ve velkém měřítku](how-to-deploy-monitor.md) .
