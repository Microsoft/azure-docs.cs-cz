---
title: Nasazení Azure Machine Learning s Azure IoT Edge | Microsoft Docs
description: V tomto kurzu nasadíte Azure Machine Learning jako modul na hraniční zařízení.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: a1b34fe75f76d5f615ab33069f3012f22dc7ef2e
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413069"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Kurz: Nasazení Azure Machine Learning jako modulu IoT Edge (Preview)

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provede nasazením modulu Azure Machine Learning, který předpovídá, kdy zařízení selže, na základě simulovaných dat teploty počítače. Další informace o Azure ML na IoT Edge najdete na stránce [Dokumentace ke službě Azure Machine Learning](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md).

Modul Azure Machine Learning, který vytvoříte v tomto kurzu, přečte data o prostředí vygenerovaná zařízením a označí zprávy jako standardní nebo neobvyklé.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit modul Azure Machine Learning
> * Doručit kontejner modulu do služby Azure Container Registry
> * Nasadit modul Azure Machine Learning na zařízení IoT Edge
> * Zobrazení vygenerovaných dat

>[!NOTE]
>Moduly Azure Machine Learning na Azure IoT Edge jsou ve verzi Public Preview. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).
* Modul Azure Machine Learning nepodporuje procesory ARM.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure. 

Prostředky pro vývoj:
* Účet služby Azure Machine Learning. Postupujte podle pokynů v části o [vytvoření účtů služeb Azure Machine Learning a instalaci aplikace Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Kvůli tomuto kurzu nemusíte aplikaci Workbench instalovat. 
* Správa modelů pro Azure ML. K nastavení prostředí a vytvoření účtu použijte pokyny v [nastavení Správy modelů](../machine-learning/desktop-workbench/deployment-setup-configuration.md). Při nastavení nasazení doporučujeme místo clusteru zvolit místní kroky (kde je to možné).

### <a name="disable-process-identification"></a>Zakázání identifikace procesů

>[!NOTE]
>
> Azure Machine Learning ve verzi Preview nepodporuje zabezpečovací funkci identifikace procesů, která je ve výchozím nastavení IoT Edge povolená. 
> Níže jsou uvedené kroky k jejímu zakázání. Pro použití v produkčním prostředí to ale není vhodné. Tyto kroky jsou nezbytné pouze v Linuxu, jinak je dokončíte během pokynů k instalaci modulu runtime Windows Edge.

K zakázání identifikace procesů na vašem zařízení IoT Edge budete muset v části **connect** konfigurace procesu démon IoT Edge zadat IP adresu a port pro **workload_uri** a **management_uri**.

Nejdřív zjistěte IP adresu. Do příkazového řádku zadejte `ifconfig` a zkopírujte IP adresu rozhraní **docker0**.

Upravte konfigurační soubor procesu démon IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Aktualizujte oddíl **connect** konfigurace svojí IP adresou. Příklad:
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Stejné adresy zadejte do části konfigurace **listen**. Příklad:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Vytvořte proměnnou prostředí IOTEDGE_HOST s adresou management_uri (pro trvalé nastavení ji přidejte do `/etc/environment`). Příklad:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


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

Zkontrolujte, že se image kontejneru úspěšně vytvořila a uložila v registru kontejneru Azure, který je spojený s vaším prostředím strojového učení.

1. Na webu [Azure Portal](https://portal.azure.com) přejděte na **Všechny služby** a vyberte **Registry kontejnerů**.
2. Vyberte registr. Název by měl začínat na **mlcr** a měl by patřit do skupiny prostředků, umístění a předplatného, které jste použili k nastavení Správy modelů.
3. Vyberte **Přístupové klíče**.
4. Zkopírujte hodnoty **Přihlašovací server**, **Uživatelské jméno** a **Heslo**.  Budete je potřebovat pro přístup k registru ze zařízení Edge.
5. Vyberte **Úložiště**.
6. Vyberte **machinelearningmodule**.
7. Teď máte celou cestu k imagi kontejneru. Poznamenejte si tuto cestu pro další část. Měla by vypadat takto: **<název_registru>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Nasazení zařízení

1. Na webu [Azure Portal](https://portal.azure.com) přejděte do svého IoT Hubu.

1. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge.

1. Vyberte **Nastavit moduly**.

1. V části **Registry Settings** (Nastavení registru) přidejte přihlašovací údaje, které jste zkopírovali z registru kontejneru Azure. 

   ![Přidání přihlašovacích údajů registru](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Pokud máte na zařízení IoT Edge nasazený modul tempSensor, může se automaticky doplnit. Pokud v seznamu modulů není, přidejte ho.

    1. Klikněte na **Přidat** a vyberte **Modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Vyberte **Uložit**.

1. Přidejte modul strojového učení, který jste vytvořili.

    1. Klikněte na **Přidat** a vyberte **Modul IoT Edge**.
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

1. Vyberte **Další**.

1. V kroku **Kontrola nasazení** vyberte **Odeslat**.

1. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit).  Vedle modulu **tempSensor** a modulů runtime IoT Edge byste měli vidět i nový spuštěný modul **machinelearningmodule**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Můžete zobrazit zprávy generované každým modulem IoT Edge a také zprávy, které se doručují do vašeho IoT Hubu.

### <a name="view-data-on-your-iot-edge-device"></a>Zobrazení dat na zařízení IoT Edge

Na zařízení IoT Edge můžete zobrazit zprávy odesílané z každého jednotlivého modulu. 

Pokud tyto příkazy provádíte na zařízení s Linuxem, možná budete muset použít `sudo` pro zvýšená oprávnění.

1. Takto zobrazíte všechny moduly na zařízení IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Takto zobrazíte zprávy odesílané z konkrétního zařízení. Použijte název modulu z výstupu předchozího příkazu.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Zobrazení dat odesílaných do IoT Hubu

K zobrazení zpráv zařízení-cloud, které přijímá váš IoT Hub, můžete použít [průzkumníka IoT Hubu](https://github.com/azure/iothub-explorer) nebo [rozšíření Azure IoT Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Následující kroky ukazují, jak nastavit Visual Studio Code k monitorování zpráv zařízení-cloud, které přicházejí do vašeho IoT Hubu. 

1. V editoru Visual Studio Code vyberte **IoT Hub Devices** (zařízení IoT Hubu).

2. Vyberte **...** a potom v nabídce vyberte **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu).

   ![Nabídka dalších možností zařízení IoT Hubu](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Do textového pole, které se otevře nahoře na stránce, zadejte připojovací řetězec iothubowner svého IoT Hubu. Vaše zařízení IoT Edge by se mělo zobrazit v seznamu zařízení IoT Hubu.

4. Znovu vyberte **...** a pak vyberte **Start monitoring D2C message** (Začít monitorovat zprávy D2C).

5. Sledujte zprávy, které přicházejí každých pět sekund ze senzoru tempSenzor. Text zprávy obsahuje vlastnost **anomaly**, která v modulu machinelearningmodule nabývá hodnoty true nebo false. Pokud bylo spuštění modelu úspěšné, obsahuje vlastnost **AzureMLResponse** hodnotu „OK“.

   ![Text zprávy s odpovědí Azure ML](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Pokud budete pokračovat k dalšímu doporučenému článku, můžete už vytvořené prostředky a konfigurace zachovat a znovu je použít.

Pokud nebudete pokračovat, můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění prostředků Azure a skupiny prostředků je nevratná akce. V případě odstranění se skupina prostředků i všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Pokud chcete odstranit jenom IoT Hub, spusťte následující příkaz, ve kterém se použije název vaší služby Hub a název skupiny prostředků:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Odstranění celé skupiny prostředků podle názvu:

1. Přihlaste se k webu [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. Do textového pole **Filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaši službu IoT Hub. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili modul IoT Edge, který používá technologii Azure Machine Learning. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Filtrování dat snímačů pomocí kódu C#](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
