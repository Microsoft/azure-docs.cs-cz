---
title: Nasazení modulů do zařízení IoT Edge pomocí rozšíření IoT pro Azure CLI 2.0 | Microsoft Docs
description: Nasazení modulů do zařízení IoT Edge pomocí rozšíření IoT pro Azure CLI 2.0
author: chrissie926
manager: ''
ms.author: menchi
ms.date: 03/02/2018
ms.topic: tutorial
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
md.custom: mvc
ms.openlocfilehash: deee54fe5d11d6d1cf5485357f853b1cb078f96d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631577"
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Nasazení modulů do zařízení IoT Edge pomocí rozšíření IoT pro Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy určený ke správě prostředků Azure, jako je služba IoT Edge. Azure CLI 2.0 je k dispozici v systémech Windows, Linux a MacOS.

Azure CLI 2.0 bez dalších úprav umožňuje spravovat prostředky služby Azure IoT Hub, instance služby Device Provisioning a propojená centra. Nové rozšíření IoT vylepšuje rozhraní Azure CLI 2.0 o funkce, jako je správa zařízení a plně funkční IoT Edge.

V tomto článku nastavíte Azure CLI 2.0 a rozšíření IoT. Pak se naučíte nasazovat moduly do zařízení IoT Edge příkazy dostupnými v CLI.

## <a name="prerequisites"></a>Požadavky

* Účet Azure. Pokud ještě nemáte účet Azure, můžete si ho ještě dnes [zdarma vytvořit](https://azure.microsoft.com/free/?v=17.39a). 

* [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. Ve Windows můžete instalaci jednoduše provést stažením a instalací [MSI](https://aka.ms/InstallAzureCliWindows).

* [Rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Spusťte `az extension add --name azure-cli-iot-ext`. 
   2. Po instalaci použijte příkaz `az extension list` k ověření aktuálně nainstalovaných rozšíření nebo příkaz `az extension show --name azure-cli-iot-ext` k zobrazení podrobností o rozšíření IoT.
   3. K odebrání rozšíření použijte příkaz `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Vytvoření zařízení IoT Edge
V tomto článku najdete pokyny k vytvoření nasazeného IoT Edge. V tomto příkladu si ukážeme, jak se přihlásit k účtu Azure, vytvořit skupinu prostředků Azure (kontejner, ve kterém jsou prostředky související s řešením Azure), jak vytvořit IoT Hub, tři identity zařízení IoT Edge, jak nastavit značky a pak vytvořit nasazený IoT Edge určený pro tato zařízení. 

Přihlaste se ke svému účtu Azure. Jakmile zadáte následující přihlašovací příkaz, zobrazí se výzva, abyste se přihlásili ve webovém prohlížeči zadáním jednorázového kódu: 

   ```cli
   az login
   ```

Vytvořte novou skupinu prostředků s názvem **IoTHubCLI** ve východní oblasti USA: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Vytvoření skupiny prostředků][2]

V nově vytvořené skupině prostředků vytvořte IoT Hub pojmenovaný **CLIDemoHub**:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >V každém předplatném smí být jenom jeden bezplatný IoT Hub. Pokud chcete vytvořit bezplatné centrum příkazem CLI, nahraďte hodnotu SKU hodnotou `--sku F1`. Pokud v předplatném máte bezplatné centrum, zobrazí se při pokusu o vytvoření druhého centra chybová zpráva. 

Vytvoře zařízení IoT Edge:

   ```cli
   az iot hub device-identity create --device-id edge001 --hub-name CLIDemoHub --edge-enabled
   ```

   ![Vytvoření zařízení IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Vytvořte nasazovanou šablonu JSON a uložte v místním počítači jako soubor TXT. Cestu k souboru budete potřebovat ke spuštění příkazu apply-configuration.

Nasazené šablony JSON by měly vždy obsahovat dva systémové moduly: edgeAgent a edgeHub. Kromě těchto dvou modulů můžete soubor použít k nasazení dalších modulů do zařízení IoT Edge. Ke konfiguraci zařízení IoT Edge s jedním modulem tempSensor použijte následující ukázku:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": ""
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {},
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

Konfiguraci použijte pro zařízení IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Zobrazení modulů v zařízení IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Seznam modulů][6]

## <a name="next-steps"></a>Další kroky

* Naučte se [používat zařízení IoT Edge jako bránu](how-to-create-transparent-gateway.md).

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

