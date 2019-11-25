---
title: 'Tutorial: Stream Analytics at the edge - Azure IoT Edge'
description: In this tutorial, you deploy Azure Stream Analytics as a module to an IoT Edge device
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452527"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Deploy Azure Stream Analytics as an IoT Edge module

Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. Díky Azure IoT Edge můžete logiku služby [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) přesunout do samotného zařízení. Zpracováním telemetrických streamů na hraničních zařízeních můžete snížit objem nahrávaných dat a zkrátit dobu reakce na přehledy umožňující provádět různé akce.

Služby Azure IoT Edge a Azure Stream Analytics jsou integrované, takže můžete vytvořit úlohu Azure Stream Analytics na portálu Azure Portal a potom ji nasadit jako modul IoT Edge bez nutnosti dalšího programování.  

Azure Stream Analytics provides a richly structured query syntax for data analysis, both in the cloud and on IoT Edge devices. For more information, see [Azure Stream Analytics documentation](../stream-analytics/stream-analytics-edge.md).

Modul Stream Analytics v tomto kurzu počítá průměrnou teplotu modulu v 30sekundových intervalech. Jakmile průměrná teplota dosáhne hodnoty 70, modul pošle upozornění do zařízení, aby provedlo akci. V tomto případě akce spočívá v tom, že se resetuje čidlo simulované teploty. V produkčním prostředí můžete tuto funkci například využít k vypnutí přístroje nebo jako preventivní opatření pro případ, kdy teplota dosáhne nebezpečné úrovně. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit úlohu Azure Stream Analytics, která zpracuje data na hraničním zařízení
> * Připojit se k nové úloze Azure Stream Analytics pomocí jiných modulů IoT Edge
> * Nasadit úlohu Azure Stream Analytics na zařízení IoT Edge z portálu Azure Portal

<center>

![Diagram - Tutorial architecture, stage and deploy ASA job](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Zařízení Azure IoT Edge:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

In this section, you create an Azure Stream Analytics job that will do the following steps:
* Receive data from your IoT Edge device.
* Query the telemetry data for values outside a set range.
* Take action on the IoT Edge device based on the query results. 

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Když vytvoříte úlohu Azure Stream Analytics, která se má spouštět na zařízení IoT Edge, je potřeba ji uložit tak, aby ji bylo možné volat ze zařízení. Můžete použít existující účet úložiště Azure nebo teď vytvořit nový. 

1. In the Azure portal, go to **Create a resource** > **Storage** > **Storage account**. 

1. Zadejte následující hodnoty pro vytvoření účtu úložiště:

   | Pole | Hodnota |
   | ----- | ----- |
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Name (Název) | Zadejte jedinečný název účtu úložiště. | 
   | Umístění | Zvolte umístění, které je blízko vás. |


1. Keep the default values for the other fields and select **Review + Create**.

1. Review your settings then select **Create**.

### <a name="create-a-new-job"></a>Vytvoření nové úlohy

1. In the Azure portal, go to **Create a resource** > **Internet of Things** > **Stream Analytics job**.

1. Zadejte následující hodnoty pro vytvoření úlohy:

   | Pole | Hodnota |
   | ----- | ----- |
   | Název úlohy | Zadejte název úlohy. Například **IoTEdgeJob** | 
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. | 
   | Hostitelské prostředí | Vyberte **Edge**. |
 
1. Vyberte **Create** (Vytvořit).

### <a name="configure-your-job"></a>Konfigurace úlohy

Po vytvoření úlohy Stream Analytics na webu Azure Portal pro ni můžete nakonfigurovat vstup, výstup a dotaz, který má spustit pro data, která jí procházejí. 

V této části se s využitím těchto tří prvků (vstup, výstup a dotaz) vytvoří úloha, která přijímá data o teplotě ze zařízení IoT Edge. Úloha analyzuje tato data v 30sekundových intervalech. Pokud průměrná teplota v tomto intervalu přesáhne 70 stupňů, do zařízení IoT Edge se odešle upozornění. V další části při nasazení úlohy přesně určíte, odkud data přicházejí a kam směřují.  

1. Přejděte k vaší úloze Stream Analytics na webu Azure Portal. 

1. V části **Topologie úlohy** vyberte **Vstupy** a pak **Přidat vstup streamu**.

   ![Azure Stream Analytics - add input](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový vstup** zadejte **teplota** jako alias pro vstup. 

1. U ostatních polí ponechte výchozí hodnoty a vyberte **Uložit**.

1. V části **Topologie úlohy** otevřete **Výstupy** a pak vyberte **Přidat**.

   ![Azure Stream Analytics - add output](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový výstup** zadejte **upozornění** jako alias pro výstup. 

1. U ostatních polí ponechte výchozí hodnoty a vyberte **Uložit**.

1. V části **Topologie úlohy** vyberte **Dotaz**.

1. Výchozí text nahraďte následujícím dotazem. Tento kód SQL odešle do výstupu úlohy příkaz k resetování, pokud průměrná teplota počítače v 30sekundovém intervalu dosáhne 70 stupňů. Příkaz k resetování je ve snímači předem naprogramovaný jako akce, kterou je možné provést. 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. Vyberte **Save** (Uložit).

### <a name="configure-iot-edge-settings"></a>Konfigurace nastavení IoT Edge

Pokud chcete úlohu Stream Analytics připravit k nasazení na zařízení IoT Edge, musíte úlohu přidružit ke kontejneru v účtu úložiště. Až přejdete k nasazení úlohy, definice úlohy se exportuje do kontejneru úložiště. 

1. Under **Configure**, select **Storage account settings** then select **Add storage account**. 

   ![Azure Stream Analytics - add storage account](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Select the **Storage account** that you created at the beginning of this tutorial from the drop-down menu.

1. V poli **Kontejner** vyberte **Vytvořit nový** a zadejte název kontejneru úložiště. 

1. Vyberte **Save** (Uložit). 

## <a name="deploy-the-job"></a>Nasazení úlohy

Nyní jste připraveni nasadit úlohu Azure Stream Analytics na zařízení IoT Edge. 

V této části pomocí průvodce **Nastavení modulů** na webu Azure Portal vytvoříte *manifest nasazení*. Manifest nasazení je soubor JSON popisující všechny moduly, které se nasadí do zařízení, registry kontejnerů, které uchovávají image modulů, způsob správy modulů a způsob vzájemné komunikace mezi moduly. Vaše zařízení IoT Edge načte svůj manifest nasazení ze služby IoT Hub a pak pomocí informací v něm obsažených nasadí a nakonfiguruje všechny přiřazené moduly. 

Pro účely tohoto kurzu nasadíte dva moduly. The first is **SimulatedTemperatureSensor**, which is a module that simulates a temperature and humidity sensor. Druhým modulem je vaše úloha Stream Analytics. Modul snímače poskytuje datový proud, který bude analyzovat dotaz vaší úlohy. 

1. Na webu Azure Portal přejděte do svého centra IoT.

1. Go to **IoT Edge**, and then open the details page for your IoT Edge device.

1. Vyberte **Nastavit moduly**.  

1. If you previously deployed the SimulatedTemperatureSensor module on this device, it might autopopulate. Pokud ne, přidejte ho následujícím postupem:

   1. Klikněte na **Add** (Přidat) a vyberte **IoT Edge Module** (Module IoT Edge).
   1. For the name, type **SimulatedTemperatureSensor**.
   1. Jako identifikátor URI obrázku zadejte **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.

1. Přidejte úlohu Azure Stream Analytics Edge následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul Azure Stream Analytics**.
   1. Vyberte předplatné a úlohu Azure Stream Analytics Edge, kterou jste vytvořili. 
   1. Vyberte **Save** (Uložit).

   Once you save your changes, the details of your Stream Analytics job are published to the storage container that you created. 

1. When the Stream Analytics module is added to the list of modules, select **Configure** to see how it's structured. 

   Identifikátor URI image odkazuje na standardní image Azure Stream Analytics. This one image is used for every Stream Analytics module that gets deployed to an IoT Edge device. 

   Dvojče modulu má nakonfigurovanou požadovanou vlastnost **ASAJobInfo**. Hodnota této vlastnosti odkazuje na definici úlohy ve vašem kontejneru úložiště. This property is how the Stream Analytics image is configured with your specific job details. 

   By default, the Stream Analytics module takes the same name as the job it's based on. You can change the module name on this page if you like, but it's not necessary. 

1. Close the module configuration page.

1. Poznamenejte si název vašeho modulu Stream Analytics, protože ho budete potřebovat v dalším kroku, a pak pokračujte výběrem **Další**.

1. Nahraďte výchozí hodnotu v části **Trasy** následujícím kódem. Místo všech tří výskytů zástupného textu _{moduleName}_ zadejte název vašeho modulu Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Trasy, které zde deklarujete, definují tok dat zařízením IoT Edge. The telemetry data from SimulatedTemperatureSensor are sent to IoT Hub and to the **temperature** input that was configured in the Stream Analytics job. The **alert** output messages are sent to IoT Hub and to the SimulatedTemperatureSensor module to trigger the reset command. 

1. Vyberte **Další**.

1. In the **Review Deployment** step, you can see how the information you provided in the wizard is converted into a JSON deployment manifest. When you're done reviewing the manifest, select **Submit**.

1. Vraťte se na obrazovku podrobností o zařízení a vyberte **Aktualizovat**.  

    You should see the new Stream Analytics module running, along with the IoT Edge agent and IoT Edge hub modules. It may take a few minutes for the information to reach your IoT Edge device, and then for the new modules to start. If you don't see the modules running right away, continue refreshing the page.

    ![SimulatedTemperatureSensor and ASA module reported by device](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Zobrazení dat

Now you can go to your IoT Edge device to check out the interaction between the Azure Stream Analytics module and the SimulatedTemperatureSensor module.

1. Zkontrolujte, že v Dockeru běží všechny moduly:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Zobrazte si všechny systémové protokoly a data metrik. Použijte název modulu Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. View the reset command affect the SimulatedTemperatureSensor by viewing the sensor logs:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   You can watch the machine's temperature gradually rise until it reaches 70 degrees for 30 seconds. Potom modul Stream Analytics aktivuje resetování a teplota přístroje klesne na 21. 

   ![Reset command output into module logs](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali úlohu Azure Streaming Analytics, aby analyzovala data ze zařízení IoT Edge. Potom jste nahráli tento modul Azure Stream Analytics na zařízení IoT Edge, aby místně zpracovával a reagoval na zvýšení teploty a posílal stream agregovaných dat do cloudu. Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"] 
> [Nasazení modelu Azure Machine Learning jako modulu](tutorial-deploy-machine-learning.md)
