---
title: Nasazení Azure Machine Learning s Azure IoT hranou | Microsoft Docs
description: Azure Machine Learning nasadit jako modul pro hraniční zařízení
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1b8a6079a68c50f80b593a1f56bc5256047ef31c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Nasazení Azure Machine Learning jako modul IoT Edge – náhled

Moduly IoT Edge můžete nasadit kód, který implementuje obchodní logiku přímo do zařízení IoT okraj. Tento kurz vás provede procesem nasazení modul Azure Machine Learning, který bude předpovídat při selhání podle dat snímačů v simulované zařízení IoT okraj, který jste vytvořili v zařízení [nasazení Azure IoT Edge v simulovaném zařízení v systému Windows] [ lnk-tutorial1-win] nebo [Linux] [ lnk-tutorial1-lin] kurzy. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Vytvořit modul Azure Machine Learning
> * Nabízená kontejner modulu registru kontejner Azure
> * Modul služby Azure Machine Learning nasadit do zařízení IoT Edge
> * Vygeneruje zobrazení dat

Modul Azure Machine Learning, který vytvoříte v tomto kurzu přečte prostředí data generována zařízení a popisků zprávy jako neobvyklé, nebo ne. 

## <a name="prerequisites"></a>Požadavky

* Azure IoT hraniční zařízení, který jste vytvořili v prvním kurzu nebo rychlý start.
* Připojovací řetězec služby IoT Hub pro službu IoT hub, která zařízení IoT Edge připojí k.
* Účet Azure Machine Learning. Chcete-li vytvořit účet, postupujte podle pokynů v [vytvořit Azure Machine Learning účtů a nainstalujte Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts). Není nutné k instalaci aplikace workbench pro účely tohoto kurzu. 
* Modul správy Azure ML na váš počítač. Nastavení prostředí a vytvoření účtu, postupujte podle pokynů v [nastavení modelu správce](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Vytvoření kontejneru Azure ML
V této části se stáhnout soubory trained model a převést je na kontejner Azure ML.  

Na počítači spuštění modulu správy pro Azure ML, stáhněte a uložte [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) a [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) ze sady IoT Azure ML na Githubu. Tyto soubory definovat vyškolení strojového učení model, který nasadíte do zařízení Iot okraj. 

Použijte pro cvičný model vytvořit kontejner, který se dá nasadit na IoT hraniční zařízení. Pomocí následujícího příkazu:

   * Zaregistrujte modelu.
   * Vytvořte manifest.
   * Vytvořit kontejner Docker image s názvem *machinelearningmodule*.
   * Nasazení bitové kopie do clusteru Azure Container Service (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Zobrazení kontejneru úložiště

Zkontrolujte, zda image kontejneru byl úspěšně vytvořen a uložen v úložišti Azure kontejneru, který je přidružen prostředí machine learning.

1. Na [portál Azure](https://portal.azure.com), přejděte na **všechny služby** a vyberte **kontejneru registrech**.
2. Vyberte v registru. Název by měl začínat **mlcr** , která patří do skupiny prostředků, umístění a předplatné, které jste použili k nastavení správy pro modul.
3. Vyberte **přístupové klíče**
4. Kopírování **přihlášení na server**, **uživatelské jméno**, a **heslo**.  Je nutné tyto z hraniční zařízení získat přístup k registru.
5. Vyberte **úložiště**
6. Vyberte **machinelearningmodule**
7. Nyní máte úplnou bitovou kopii cesta kontejneru. Poznamenejte si tato cesta bitové kopie pro další části. By měl vypadat takto: **.azureacr.io/machinelearningmodule:1 < registry_name >**

## <a name="add-registry-credentials-to-your-edge-device"></a>Přidejte pověření registru do hraniční zařízení

Přidáte přihlašovací údaje pro vaše registru do hraniční runtime v počítači, na kterém je spuštěn hraniční zařízení. Tento příkaz dává runtime přístup ke kontejneru pro vyžádání obsahu.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>Spuštění řešení

1. Na [portál Azure](https://portal.azure.com), přejděte do služby IoT hub.
1. Přejděte na **IoT Edge (preview)** a vyberte zařízení IoT Edge.
1. Vyberte **Set modules** (Nastavit moduly).
1. Pokud modul tempSensor jste předtím nasadili do zařízení IoT Edge, může autopopulate. Pokud již není v seznamu modulů, přidejte ji.
    1. Vyberte **přidání okraj IoT modulu**.
    2. V **název** zadejte `tempSensor`.
    3. V **Image URI** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Vyberte **Uložit**.
1. Přidejte strojového učení modul, který jste vytvořili.
    1. Vyberte **přidání okraj IoT modulu**.
    1. V **název** pole, zadejte `machinelearningmodule`
    1. V **Image** pole, zadejte adresu bitové kopie, třeba `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Vyberte **Uložit**.
1. Zpět v kroku **přidání modulů** vyberte **Další**.
1. V **zadejte trasy** kroku, zkopírujte do textového pole JSON níže. První trasa je určena k přenosu zprávy teplotní snímač machine learning module prostřednictvím koncového bodu "amlInput", což je koncový bod, který použít všechny moduly Azure Machine Learning. Druhá trasa je určena k přenosu zpráv z modulu learning počítač do služby IoT Hub. V této trase '' amlOutput'' je koncový bod, který všechny moduly Azure Machine Learning pomocí výstupní data a '' proti proudu$ "označuje IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Vyberte **Next** (Další). 
1. V kroku **kontroly šablony** vyberte **Submit** (Odeslat). 
1. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit).  Byste měli vidět, že se vám nové **machinelearningmodule** společně s **tempSensor** modulu a moduly runtime IoT okraj.

## <a name="view-generated-data"></a>Vygeneruje zobrazení dat

Můžete zobrazit zprávy typu zařízení cloud, které zařízení IoT Edge odešle pomocí [IoT Hub explorer](https://github.com/azure/iothub-explorer) nebo rozšíření Azure IoT nástrojů pro Visual Studio Code. 

1. V sadě Visual Studio Code vyberte **zařízení IoT Hub**. 
2. Vyberte **...**  vyberte **nastavit připojovací řetězec centra IoT** z nabídky. 

   ![Zařízení IoT Hub další nabídky](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Do textového pole, které se otevře v horní části stránky zadejte iothubowner připojovací řetězec služby IoT hub. Zařízení IoT okraj by měl zobrazit v seznamu zařízení IoT Hub.
4. Vyberte **...**  znovu vyberte **začít monitorovat D2C zpráva**.
5. Podívejte se na zprávy přicházející z tempSensor každých pět sekund. Tělo zprávy obsahuje vlastnost s názvem **anomálií** které machinelearningmodule poskytuje hodnotu true nebo false. **AzureMLResponse** vlastnost obsahuje hodnotu "OK", pokud model proběhla úspěšně. 

   ![Azure ML odpovědi do těla zprávy](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili modul IoT Edge používá technologii Azure Machine Learning. Můžete pokračovat na všechny ostatní kurzy informace o další způsoby, které mohou pomoci Azure IoT Edge, že zapněte data do podnikových statistik na hranici.

> [!div class="nextstepaction"]
> [Nasazení funkce Azure jako modul](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
