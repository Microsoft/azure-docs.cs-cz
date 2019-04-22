---
title: Jak nasadit OPC dvojče modulu pro Azure od nuly | Dokumentace Microsoftu
description: Jak nasadit OPC Dvojčete úplně od začátku.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491352"
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
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
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
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
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

## <a name="run-and-debug-locally"></a>Spustit a ladit v místním prostředí

Pro řešení potíží a ladění je vhodné spouštět hraniční moduly místně pomocí [simulátor vývoj IoT Edge](https://github.com/Azure/iotedgehubdev).  Poskytuje místní vývojové prostředí se simulátor pro vytváření, vývoji, testování, spouštění a ladění modulů Azure IoT Edge a řešení s použitím stejného bitů nebo kódu, které se používají v produkčním prostředí.

### <a name="prerequisites"></a>Požadavky

1. Nasazení Dvojčeti OPC [závislosti](howto-opc-twin-deploy-dependencies.md).

2. Nainstalujte [Docker CE (18.02.0+)](https://www.docker.com/community-edition) na [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) nebo [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Nainstalujte [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (nutné jen **Linux**. Compose už součástí instalace Windows nebo macOS Docker CE)

4. Nainstalujte [Pythonu (2.7 / 3.5+) a Pip](https://www.python.org/)

5. Nainstalujte iotedgehubdev spuštěním následující příkaz, v terminálu

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Nainstalovat `iotedgehubdev` k **kořenové** v systému Linux nebo macOS (*nepoužívejte '--uživatele "možnost v příkazu 'pip install'*).
> Ujistěte se, že není žádný modul runtime Azure IoT Edge, který je spuštěn ve stejném počítači s iotedgehubdev, protože vyžadují stejné porty.

### <a name="quickstart"></a>Rychlý start

1. Postupujte podle pokynů a [vytvořit hraniční zařízení na portálu Azure portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Zkopírujte připojovací řetězec zařízení edge.

2. Nastavení simulátoru pomocí připojovacího řetězce edge.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Zkopírujte výše manifestu do `deployment.json` soubor ve stejné složce.  Spustit nasazení v simulátoru pomocí

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Přestat používat simulátor

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasadit OPC Dvojčete úplně od začátku, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Nasazení do existujícího projektu Dvojčete OPC](howto-opc-twin-deploy-existing.md)