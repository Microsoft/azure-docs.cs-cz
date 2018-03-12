---
title: "Nasazení modulů na IoT hraniční zařízení pomocí rozšíření IoT 2.0 rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Nasazení modulů na IoT hraniční zařízení pomocí IoT rozšíření pro Azure CLI 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: 25f1becff11138e59595c211802acbf791094e53
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Nasazení modulů na IoT hraniční zařízení pomocí IoT rozšíření pro Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest) je open source pro různé platformy nástroj příkazového řádku pro správu prostředků Azure, jako je například IoT okraj. Azure CLI 2.0 je k dispozici v systému Windows, Linux a systému MacOS.

Azure CLI 2.0 umožňuje spravovat prostředky Azure IoT Hub, instance služby zřizování zařízení a propojené rozbočovače mimo pole. Nové rozšíření IoT vylepšuje 2.0 rozhraní příkazového řádku Azure s funkcemi, jako je Správa zařízení a úplných možností IoT okraj.

V tomto kurzu nejprve dokončit postup nastavení Azure CLI 2.0 a rozšíření IoT. Potom můžete naučit se nasazovat modulů na IoT hraniční zařízení pomocí dostupné příkazy rozhraní příkazového řádku.

## <a name="prerequisites"></a>Požadavky

* Účet Azure. Pokud nemáte ještě, můžete [vytvořit bezplatný účet](https://azure.microsoft.com/free/?v=17.39a) ještě dnes. 

* [Python 2.7 x nebo Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaší verzi 2.0 rozhraní příkazového řádku Azure 2.0.24 nebo vyšší. Použití `az –-version` k ověření. Tato verze podporuje az rozšíření příkazy a zavádí rozhraní Knack příkaz. Jeden způsob, jak nainstalovat v systému Windows je ke stažení a instalaci [MSI](https://aka.ms/InstallAzureCliWindows).

* [Rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Spusťte `az extension add --name azure-cli-iot-ext`. 
   2. Po instalaci pomocí `az extension list` ověření aktuálně nainstalovaná rozšíření nebo `az extension show --name azure-cli-iot-ext` zobrazíte podrobnosti o rozšíření IoT.
   3. Chcete-li odebrat rozšíření, použijte `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Vytvoření IoT hraniční zařízení
Tento článek poskytuje pokyny pro vytvoření nasazení IoT okraj. V příkladu se dozvíte, jak přihlásit k účtu Azure, vytvořte skupinu prostředků Azure (kontejner, který obsahuje související prostředky pro řešení s Azure), vytvoření služby IoT Hub, vytvořit tři identitu zařízení IoT Edge, nastavte značky a potom vytvořit nasazení služby IoT Edge Cílem těchto zařízení. 

Přihlaste se k účtu Azure. Když zadáte následující příkaz přihlášení se zobrazí výzva k použít webový prohlížeč k přihlášení pomocí jednorázové kódu: 

   ```cli
   az login
   ```

Vytvořit novou skupinu prostředků s názvem **IoTHubCLI** v oblasti Východ USA: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Vytvoření skupiny prostředků][2]

Vytvoření služby IoT hub názvem **CLIDemoHub** ve skupině pro nově vytvořený prostředek:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Každé předplatné je vymezena jeden bezplatné Centrum IoT. Pro vytvoření rozbočovač volné pomocí rozhraní příkazového řádku příkaz se nahradit hodnotu SKU `--sku F1`. Pokud již máte volné hub v rámci vašeho předplatného, budete se při pokusu o vytvoření druhá zobrazí chybové hlášení. 

Vytvořte IoT hraniční zařízení:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Vytvoření zařízení IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Nakonfigurujte hraniční IoT zařízení

Vytvořit šablonu JSON nasazení a uložte ho místně jako souboru txt. Když spustíte příkaz použít konfiguraci budete potřebovat cestu k souboru.

Nasazení šablony JSON by měla obsahovat vždy dva systému moduly, edgeAgent a edgeHub. Tento soubor můžete použít k nasazení dalších modulů zařízení IoT Edge kromě těchto dvou. Použijte následující příklad konfigurace IoT hraniční zařízení s jeden modul tempSensor:

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

Použít konfiguraci na IoT hraniční zařízení:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Zobrazení modulů ve vašem zařízení IoT okraj:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Seznam modulů][6]

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [pomocí zařízení IoT okraj jako brána](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

