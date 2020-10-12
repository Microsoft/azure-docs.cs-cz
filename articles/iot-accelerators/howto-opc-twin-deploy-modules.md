---
title: Postup nasazení OPC vlákna pro Azure od začátku | Microsoft Docs
description: Tento článek popisuje, jak nasazovat OPC zcela od začátku pomocí IoT Edge okna Azure Portal a také pomocí AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9ae3e9b4bb69bf0c85054b5d6144633923cac947
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282064"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Nasazení OPC vyzdvojeného modulu a závislostí od začátku

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

OPC modul se spouští na IoT Edge a poskytuje několik hraničních služeb pro služby OPC a Registry zařízení. 

Existuje několik možností, jak nasadit moduly do brány [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) , mezi nimi

- [Nasazení z okna IoT Edge Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Nasazení pomocí AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Další informace o podrobnostech a pokynech k nasazení najdete v [úložišti](https://github.com/Azure/azure-iiot-components)GitHub.

## <a name="deployment-manifest"></a>Manifest nasazení

Všechny moduly jsou nasazeny pomocí manifestu nasazení.  Příklad manifestu pro nasazení [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) i [OPC](https://github.com/Azure/azure-iiot-opc-twin-module) je uveden níže.

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

## <a name="deploying-from-azure-portal"></a>Nasazení z Azure Portal

Nejjednodušší způsob, jak nasadit moduly do zařízení Azure IoT Edge brány, je prostřednictvím Azure Portal.  

### <a name="prerequisites"></a>Požadavky

1. Nasaďte zdvojené [závislosti](howto-opc-twin-deploy-dependencies.md) OPC a získá výsledný `.env` soubor. Poznamenejte si nasazenou `hub name` `PCS_IOTHUBREACT_HUB_NAME` proměnnou ve výsledném `.env` souboru.

2. Zaregistrujte a spusťte bránu pro [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) nebo [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge a poznamenejte si ji `device id` .

### <a name="deploy-to-an-edge-device"></a>Nasazení do hraničního zařízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte do služby IoT Hub.

2. V nabídce na levé straně vyberte **IoT Edge** .

3. V seznamu zařízení klikněte na ID cílového zařízení.

4. Vyberte možnost **nastavit moduly**.

5. V části **moduly nasazení** na stránce vyberte **Přidat** a **IoT Edge modul.**

6. V dialogovém okně **IoT Edge vlastní modul** použijte `opctwin` jako název modulu jako název a pak zadejte *identifikátor URI image* kontejneru jako

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   V části *možnosti vytvoření kontejneru*použijte následující kód JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   V případě potřeby vyplňte volitelná pole. Další informace o možnostech vytvoření kontejneru, zásadách restartování a požadovaném stavu najdete v tématu [EdgeAgent požadované vlastnosti](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Další informace o tomto modulu najdete v tématu [definice nebo aktualizace požadovaných vlastností](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Vyberte **Save (Uložit** ) a opakujte krok **5**.  

8. V dialogovém okně IoT Edge vlastní modul použijte `opcpublisher` jako název pro modul a *identifikátor URI image* kontejneru jako 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   V části *možnosti vytvoření kontejneru*použijte následující kód JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Vyberte **Save (Uložit** ) a potom klikněte na **Další** a pokračujte na část trasy.

10. Na kartě trasy vložte následující 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    a vyberte **Další** .

11. Zkontrolujte informace o nasazení a manifest.  Měl by vypadat jako výše uvedený manifest nasazení.  Vyberte **Odeslat**.

12. Až nasadíte moduly do svého zařízení, můžete je zobrazit na stránce **Podrobnosti o zařízení** na portálu. Tato stránka zobrazuje název každého nasazeného modulu a také užitečné informace, jako je stav nasazení a ukončovací kód.

## <a name="deploying-using-azure-cli"></a>Nasazení pomocí Azure CLI

### <a name="prerequisites"></a>Požadavky

1. Nainstalujte nejnovější verzi [rozhraní příkazového řádku Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) z [tohoto místa](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Rychlé zprovoznění

1. Výše uvedený manifest nasazení uložte do `deployment.json` souboru.  

2. Pomocí následujícího příkazu použijte konfiguraci pro IoT Edge zařízení:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   V `device id` parametru se rozlišují malá a velká písmena. Parametr obsahu odkazuje na soubor manifestu nasazení, který jste uložili. 
    ![AZ IoT Edge Set-module Output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Až nasadíte moduly do svého zařízení, můžete je zobrazit pomocí následujícího příkazu:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Parametr ID zařízení rozlišuje velká a malá písmena. ![AZ IoT Hub Module-identity list Output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit OPC od začátku, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Nasazení OPC vlákna do existujícího projektu](howto-opc-twin-deploy-existing.md)
