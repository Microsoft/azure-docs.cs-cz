---
title: Jak nasadit modul OPC Twin pro Azure od začátku | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nasadit OPC Twin od začátku pomocí okna IoT Edge portálu Azure a také pomocí příkazového příkazu AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241062"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Nasazení modulu OPC Twin a závislostí od začátku

Modul OPC Twin běží na IoT Edge a poskytuje několik hraničních služeb pro dvojče Zařízení OPC a služby registru. 

Existuje několik možností nasazení modulů do brány [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, mezi nimi

- [Nasazení z čepele IoT Edge na webu Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Nasazení pomocí funkce AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Další informace o podrobnostech nasazení a pokyny naleznete v [úložišti](https://github.com/Azure/azure-iiot-components)GitHub .

## <a name="deployment-manifest"></a>Manifest nasazení

Všechny moduly jsou nasazeny pomocí manifestu nasazení.  Příklad manifestu pro nasazení [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) a [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) je uveden níže.

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Nasazení z webu Azure Portal

Nejjednodušší způsob nasazení modulů do zařízení brány Azure IoT Edge je prostřednictvím portálu Azure.  

### <a name="prerequisites"></a>Požadavky

1. Nasaďte závislosti [DvojčeTece](howto-opc-twin-deploy-dependencies.md) OPC `.env` a získaďte výsledný soubor. Všimněte si `hub name` nasazené `PCS_IOTHUBREACT_HUB_NAME` proměnné ve `.env` výsledném souboru.

2. Zaregistrujte se [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) a spusťte bránu Linux `device id`nebo [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge a poznamenejte si její .

### <a name="deploy-to-an-edge-device"></a>Nasazení do hraničního zařízení

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) a přejděte do svého centra IoT hub.

2. V levé nabídce vyberte **IoT Edge.**

3. Klikněte na ID cílového zařízení ze seznamu zařízení.

4. Vyberte **možnost Nastavit moduly**.

5. V části **Moduly nasazení** na stránce vyberte **Přidat** a **IoT Edge Module.**

6. V dialogovém okně **Vlastní modul IoT Edge** použijte `opctwin` jako název modulu a zadejte identifikátor URI *image* kontejneru jako

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Jako *možnosti vytvoření kontejneru*použijte následující json:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   V případě potřeby vyplňte volitelná pole. Další informace o možnostech vytváření kontejnerů naleznete v zásadách restartování a požadovaném stavu v tématu [EdgeAgent požadované vlastnosti](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Další informace o dvojčeti modulu naleznete v [tématu Definování nebo aktualizace požadovaných vlastností](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Vyberte **Uložit** a zopakujte krok **5**.  

8. V dialogovém okně Vlastní modul `opcpublisher` IoT Edge použijte jako název modulu a *identifikátor URI obrázku* kontejneru jako 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Jako *možnosti vytvoření kontejneru*použijte následující json:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Chcete-li pokračovat do oddílu trasy, vyberte **možnost Uložit** a potom **další.**

10. Na kartě Trasy vložte následující 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    a vyberte **Další**

11. Zkontrolujte informace o nasazení a manifest.  Mělo by to vypadat jako výše uvedený manifest nasazení.  Vyberte **Odeslat**.

12. Po nasazení modulů do zařízení je můžete všechny zobrazit na stránce **Podrobnosti o zařízení** na portálu. Na této stránce se zobrazí název každého nasazeného modulu a také užitečné informace, jako je stav nasazení a ukončovací kód.

## <a name="deploying-using-azure-cli"></a>Nasazení pomocí azure cli

### <a name="prerequisites"></a>Požadavky

1. [Nainstalujte](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nejnovější verzi [rozhraní příkazového řádku Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) odtud .

### <a name="quickstart"></a>Rychlý start

1. Uložte výše uvedený `deployment.json` manifest nasazení do souboru.  

2. Pomocí následujícího příkazu použijte konfiguraci na zařízení IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Parametr `device id` rozlišuje malá a velká písmena. Parametr obsahu odkazuje na soubor manifestu nasazení, který jste uložili. 
    ![výstup az IoT Edge set-modules výstup](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Po nasazení modulů do zařízení je můžete všechny zobrazit pomocí následujícího příkazu:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Parametr ID zařízení rozlišuje malá a velká písmena. ![výstup seznamu az iot hub modul-identity](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili, jak nasadit OPC Twin od nuly, zde je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Nasazení OPC Twin do existujícího projektu](howto-opc-twin-deploy-existing.md)
