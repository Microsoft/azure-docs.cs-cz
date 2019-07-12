---
title: Jak nasadit OPC dvojče modulu pro Azure od nuly | Dokumentace Microsoftu
description: Jak nasadit OPC Dvojčete úplně od začátku.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 798f087c260b6b0a1efc366b864fe2bb7bce732e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603690"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Nasazení modulu Dvojčete OPC a závislosti úplně od začátku

OPC Dvojčete modulu běží na hraničních zařízeních IoT a poskytuje několik služeb edge do dvojčete zařízení OPC a registr služeb. 

Existuje několik možností, jak můžete nasadit moduly pro vaše [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) brány mezi nimi

- [Nasazení z okna portálu Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Nasazení pomocí rozhraní příkazového řádku AZ](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Další informace o podrobnosti nasazení a pokyny najdete v článku Githubu [úložiště](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Manifest nasazení

Všechny moduly jsou nasazeny pomocí manifestu nasazení.  Manifest příklad nasazení obou [vydavatel OPC](https://github.com/Azure/iot-edge-opc-publisher) a [OPC Dvojčete](https://github.com/Azure/azure-iiot-opc-twin-module) je uveden níže.

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

## <a name="deploying-from-azure-portal"></a>Nasazení z webu Azure portal

Nejjednodušší způsob, jak nasadit moduly pro zařízení brány Azure IoT Edge je na webu Azure portal.  

### <a name="prerequisites"></a>Požadavky

1. Nasazení Dvojčeti OPC [závislosti](howto-opc-twin-deploy-dependencies.md) a získat výsledný `.env` souboru. Všimněte si, nasazené `hub name` z `PCS_IOTHUBREACT_HUB_NAME` proměnné ve výsledné `.env` souboru.

2. Zaregistrujte se a začněte [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) nebo [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) brána IoT Edge a poznamenejte si jeho `device id`.

### <a name="deploy-to-an-edge-device"></a>Nasazení do hraničního zařízení

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a přejděte do služby IoT hub.

2. Vyberte **IoT Edge** z nabídky na levé straně.

3. Kliknutím na ID cílové zařízení ze seznamu zařízení.

4. Vyberte **Nastavit moduly**.

5. V **moduly nasazení** části stránky vyberte **přidat** a **modul IoT Edge.**

6. V **modul IoT Edge vlastní** použijte dialogové okno `opctwin` jako název modulu, pak zadejte kontejner *identifikátor URI image* jako

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Jako *možnosti vytvoření* použijte následující kód JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   V případě potřeby zadejte volitelná pole. Pro další informace o kontejneru vytvořte možnosti, zásady restartování a zjistěte požadovaný stav [EdgeAgent požadované vlastnosti](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Další informace o dvojčeti modulu najdete v části [definovat nebo aktualizace požadované vlastnosti](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Vyberte **Uložit** a opakujte krok **5**.  

8. V dialogovém okně vlastní modul IoT Edge, použijte `opcpublisher` jako název modulu a kontejneru *identifikátor URI image* jako 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Jako *možnosti vytvoření* použijte následující kód JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Vyberte **Uložit** a potom **Další** pokračujte k části trasy.

10. Na kartě trasy vložte následující 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    a vyberte **další**

11. Zkontrolujte informace o nasazení a manifest.  Měl by vypadat jako výše manifestu nasazení.  Vyberte **odeslat**.

12. Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich **podrobnosti o zařízení** stránky portálu. Tato stránka zobrazuje název každé nasazené modulu, stejně jako užitečné informace, jako je nasazení stavu a ukončovací kód.

## <a name="deploying-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

### <a name="prerequisites"></a>Požadavky

1. Nainstalujte nejnovější verzi [rozhraní příkazového řádku Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) z [tady](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Rychlý start

1. Uložit do výše uvedené manifestu nasazení `deployment.json` souboru.  

2. Chcete-li použít konfiguraci pro zařízení IoT Edge, použijte následující příkaz:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id` Parametr je velká a malá písmena. Obsahu parametr odkazuje na nasazení manifestu soubor, který jste uložili. 
    ![výstupní sada modulů IoT Edge az](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich pomocí následujícího příkazu:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Parametr ID zařízení rozlišuje velká a malá písmena. ![AZ iot hub modul identity výstupu](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasadit OPC Dvojčete úplně od začátku, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Nasazení do existujícího projektu Dvojčete OPC](howto-opc-twin-deploy-existing.md)
