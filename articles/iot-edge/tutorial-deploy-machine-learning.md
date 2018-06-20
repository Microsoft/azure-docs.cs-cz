---
title: Nasazení Azure Machine Learning s Azure IoT Edge | Microsoft Docs
description: Nasazení modulu Azure Machine Learning na zařízení Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 248bc97c214c013d10f1839201ce2f572cb854ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631169"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Nasazení Azure Machine Learning jako modulu IoT Edge – Preview

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. V tomto kurzu se seznámíte s nasazením modulu Azure Machine Learning, který předpovídá selhání zařízení na základě dat senzorů na simulovaném zařízení IoT Edge, které jste vytvořili v kurzech [Nasazení služby Azure IoT Edge na simulované zařízení ve Windows][lnk-tutorial1-win] nebo [Linuxu][lnk-tutorial1-lin].

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit modul Azure Machine Learning
> * Doručit kontejner modulu do služby Azure Container Registry
> * Nasadit modul Azure Machine Learning na zařízení IoT Edge
> * Zobrazení vygenerovaných dat

Modul Azure Machine Learning, který vytvoříte v tomto kurzu, přečte data o prostředí vygenerovaná zařízením a označí zprávy jako standardní nebo neobvyklé.

## <a name="prerequisites"></a>Požadavky

* Zařízení Azure IoT Edge, které jste vytvořili v rychlém startu nebo v prvním kurzu.
* Připojovací řetězec IoT Hub pro připojení zařízení IoT Edge k IoT Hubu.
* Účet služby Azure Machine Learning. Pokud chcete vytvořit účet, postupujte podle pokynů v části o [vytvoření účtů služby Azure Machine Learning a instalaci aplikace Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Kvůli tomuto kurzu nemusíte aplikaci Workbench instalovat. 
* Správu modelů pro Azure ML na počítači. K nastavení prostředí a vytvoření účtu použijte pokyny v [nastavení Správy modelů](../machine-learning/desktop-workbench/deployment-setup-configuration.md).

Modul Azure Machine Learning nepodporuje procesory ARM.

## <a name="create-the-azure-ml-container"></a>Vytvoření kontejneru Azure ML
V této části si stáhnete soubory trénovaného modelu a převedete je na kontejner Azure ML.

Na počítači, na kterém běží Správa modelů pro Azure ML, si stáhněte ze sady nástrojů Azure ML IoT Toolkit na GitHubu soubory [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) a [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) a uložte je. Tyto soubory definují trénovaný model strojového učení, který nasadíte na svém zařízení IoT Edge.

Trénovaný model použijte k vytvoření kontejneru, který můžete nasadit na zařízeních IoT Edge. Následující příkaz použijte k:

   * Registraci modelu.
   * Vytvořte manifest.
   * Vytvoření image kontejneru Docker s názvem *machinelearningmodule*.
   * Nasazení image v clusteru Azure Kubernetes Service (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Zobrazení úložiště kontejnerů

Ověřte, že image kontejneru byla úspěšně vytvořena a uložena v kontejnerovém úložišti Azure, které je spojené s vaším prostředím strojového učení.

1. Na webu [Azure Portal](https://portal.azure.com) přejděte na **Všechny služby** a vyberte **Registry kontejnerů**.
2. Vyberte registr. Název by měl začínat na **mlcr** a měl by patřit do skupiny prostředků, umístění a předplatného, které jste použili k nastavení Správy modelů.
3. Vyberte **Přístupové klíče**.
4. Zkopírujte hodnoty **Přihlašovací server**, **Uživatelské jméno** a **Heslo**.  Budete je potřebovat pro přístup k registru ze zařízení Edge.
5. Vyberte **Úložiště**.
6. Vyberte **machinelearningmodule**.
7. Teď máte celou cestu k imagi kontejneru. Poznamenejte si tuto cestu pro další část. Měla by vypadat takto: **<název_registru>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Přidání přihlašovacích údajů registru do hraničního zařízení

Přidejte přihlašovací údaje k vašemu registru do modulu runtime Edge na počítači, na kterém spouštíte službu Edge. Tímto příkazem dáte modulu runtime přístup k načtení kontejneru.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

## <a name="run-the-solution"></a>Spuštění řešení

1. Na webu [Azure Portal](https://portal.azure.com) přejděte do svého IoT Hubu.
1. Přejděte na **IoT Edge (preview)** a vyberte zařízení IoT Edge.
1. Vyberte **Set modules** (Nastavit moduly).
1. Pokud máte na zařízení IoT Edge nasazený modul tempSensor, může se automaticky doplnit. Pokud v seznamu modulů není, přidejte ho.
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Vyberte **Uložit**.
1. Přidejte modul strojového učení, který jste vytvořili.
    1. Vyberte **Přidat modul IoT Edge**.
    1. Do pole **Název** zadejte `machinelearningmodule`.
    1. Do pole **Image** zadejte adresu své image, například `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Vyberte **Uložit**.
1. Zpět v kroku **přidání modulů** vyberte **Další**.
1. V kroku **Určení tras** zkopírujte do textového pole následující JSON. První trasa přenáší zprávy z teplotního senzoru do modulu strojového učení prostřednictvím koncového bodu „amlInput“. Tento koncový bod používají všechny moduly Azure Machine Learning. Druhá trasa přenáší zprávy z modulu strojového učení do IoT Hubu. V této trase je „amlOutput“ koncový bod, který používají všechny moduly služby Azure Machine Learning k výstupu dat. Položka „$upstream“ znamená IoT Hub.

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
1. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit).  Vedle modulu **tempSensor** a modulů runtime IoT Edge byste měli vidět i nový spuštěný modul **machinelearningmodule**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

K zobrazení zpráv, které zařízení IoT Edge posílá do cloudu, můžete použít [průzkumníka IoT Hubu](https://github.com/azure/iothub-explorer) nebo rozšíření Azure IoT Toolkit pro Visual Studio Code.

1. V editoru Visual Studio Code vyberte **IoT Hub Devices** (zařízení IoT Hubu).
2. Vyberte **...** a potom v nabídce vyberte **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu).

   ![Nabídka dalších možností zařízení IoT Hubu](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Do textového pole, které se otevře nahoře na stránce, zadejte připojovací řetězec iothubowner svého IoT Hubu. Vaše zařízení IoT Edge by se mělo zobrazit v seznamu zařízení IoT Hubu.
4. Znovu vyberte **...** a pak vyberte **Start monitoring D2C message** (Začít monitorovat zprávy D2C).
5. Sledujte zprávy, které přicházejí každých pět sekund ze senzoru tempSenzor. Text zprávy obsahuje vlastnost **anomaly**, která v modulu machinelearningmodule nabývá hodnoty true nebo false. Pokud bylo spuštění modelu úspěšné, obsahuje vlastnost **AzureMLResponse** hodnotu „OK“.

   ![Text zprávy s odpovědí Azure ML](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili modul IoT Edge, který používá technologii Azure Machine Learning. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení funkce Azure Functions jako modulu](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
