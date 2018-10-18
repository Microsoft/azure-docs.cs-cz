---
title: 'Kurz: Nasazení úloh ASA na zařízení Azure IoT Edge | Microsoft Docs'
description: V tomto kurzu nasadíte Azure Stream Analytics jako modul na zařízení IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: de856aed32b596209f7af2861b76ed6648d0caca
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067818"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Kurz: Nasazení služby Azure Stream Analytics jako modulu IoT Edge (Preview)

Celá řada řešení IoT využívá analytické služby k získání přehledu o datech přicházejících do cloudu ze zařízení IoT. Díky Azure IoT Edge můžete logiku služby [Azure Stream Analytics][azure-stream] přesunout do samotného zařízení. Zpracováním telemetrických streamů na hraničních zařízeních můžete snížit objem nahrávaných dat a zkrátit dobu reakce na přehledy umožňující provádět různé akce.

Služby Azure IoT Edge a Azure Stream Analytics jsou integrované, takže můžete vytvořit úlohu Azure Stream Analytics na portálu Azure Portal a potom ji nasadit jako modul IoT Edge bez nutnosti dalšího programování.  

Azure Stream Analytics poskytuje rozsáhle strukturovanou syntaxi dotazů pro analýzu dat jak v cloudu, tak i na zařízeních IoT Edge. Další informace o službě Azure Stream Analytics na hraničních zařízeních IoT Edge najdete v [dokumentaci ke službě Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Modul Stream Analytics v tomto kurzu počítá průměrnou teplotu modulu v 30sekundových intervalech. Jakmile průměrná teplota dosáhne hodnoty 70, modul pošle upozornění do zařízení, aby provedlo akci. V tomto případě akce spočívá v tom, že se resetuje čidlo simulované teploty. V produkčním prostředí můžete tuto funkci například využít k vypnutí přístroje nebo jako preventivní opatření pro případ, kdy teplota dosáhne nebezpečné úrovně. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit úlohu Azure Stream Analytics, která zpracuje data na hraničním zařízení
> * Připojit se k nové úloze Azure Stream Analytics pomocí jiných modulů IoT Edge
> * Nasadit úlohu Azure Stream Analytics na zařízení IoT Edge z portálu Azure Portal

<center>
![Diagram architektury v tomto kurzu](./media/tutorial-deploy-stream-analytics/ASATutorialDiagram.png)
</center>

>[!NOTE]
>Moduly Azure Stream Analytics pro IoT Edge jsou ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free v Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

V této části vytvoříte úlohu Azure Stream Analytics, která vezme data ze služby IoT Hub, vytvoří dotaz na odeslaná telemetrická data ze zařízení a potom výsledky předá do kontejneru úložiště objektů blob Azure. 

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Když vytvoříte úlohu Azure Stream Analytics, která se má spouštět na zařízení IoT Edge, je potřeba ji uložit tak, aby ji bylo možné volat ze zařízení. Můžete použít existující účet úložiště Azure nebo teď vytvořit nový. 

1. Na webu Azure Portal přejděte do části **Vytvořit prostředek** > **Úložiště** > **Účet úložiště – objekt blob, soubor, tabulka, fronta**. 

1. Zadejte následující hodnoty pro vytvoření účtu úložiště:

   | Pole | Hodnota |
   | ----- | ----- |
   | Název | Zadejte jedinečný název účtu úložiště. | 
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |

1. U ostatních polí ponechte výchozí hodnoty a vyberte **Vytvořit**. 

### <a name="create-a-new-job"></a>Vytvoření nové úlohy

1. Na webu Azure Portal přejděte do části **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.

1. Zadejte následující hodnoty pro vytvoření úlohy:

   | Pole | Hodnota |
   | ----- | ----- |
   | Název úlohy | Zadejte název úlohy. Například **IoTEdgeJob** | 
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. | 
   | Hostitelské prostředí | Vyberte **Edge**. |
 
1. Vyberte **Vytvořit**.

### <a name="configure-your-job"></a>Konfigurace úlohy

Po vytvoření úlohy Stream Analytics na webu Azure Portal pro ni můžete nakonfigurovat vstup, výstup a dotaz, který má spustit pro data, která jí procházejí. 

V této části se s využitím těchto tří prvků (vstup, výstup a dotaz) vytvoří úloha, která přijímá data o teplotě ze zařízení IoT Edge. Úloha analyzuje tato data v 30sekundových intervalech. Pokud průměrná teplota v tomto intervalu přesáhne 70 stupňů, do zařízení IoT Edge se odešle upozornění. V další části při nasazení úlohy přesně určíte, odkud data přicházejí a kam směřují.  

1. Přejděte k vaší úloze Stream Analytics na webu Azure Portal. 

1. V části **Topologie úlohy** vyberte **Vstupy** a pak **Přidat vstup streamu**.

   ![Vstup Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový vstup** zadejte **teplota** jako alias pro vstup. 

1. U ostatních polí ponechte výchozí hodnoty a vyberte **Uložit**.

1. V části **Topologie úlohy** otevřete **Výstupy** a pak vyberte **Přidat**.

   ![Výstup Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

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

1. Vyberte **Uložit**.

### <a name="configure-iot-edge-settings"></a>Konfigurace nastavení IoT Edge

Pokud chcete úlohu Stream Analytics připravit k nasazení na zařízení IoT Edge, musíte úlohu přidružit ke kontejneru v účtu úložiště. Až přejdete k nasazení úlohy, definice úlohy se exportuje do kontejneru úložiště. 

1. V části **Konfigurovat**vyberte **Nastavení účtu úložiště**.

1. Vyberte **Přidat účet úložiště**. 

1. Z rozevíracího seznamu vyberte svůj **účet úložiště**.

1. V poli **Kontejner** vyberte **Vytvořit nový** a zadejte název kontejneru úložiště. 

1. Vyberte **Uložit**. 

## <a name="deploy-the-job"></a>Nasazení úlohy

Nyní jste připraveni nasadit úlohu Azure Stream Analytics na zařízení IoT Edge. 

V této části pomocí průvodce **Nastavení modulů** na webu Azure Portal vytvoříte *manifest nasazení*. Manifest nasazení je soubor JSON popisující všechny moduly, které se nasadí do zařízení, registry kontejnerů, které uchovávají image modulů, způsob správy modulů a způsob vzájemné komunikace mezi moduly. Vaše zařízení IoT Edge načte svůj manifest nasazení ze služby IoT Hub a pak pomocí informací v něm obsažených nasadí a nakonfiguruje všechny přiřazené moduly. 

Pro účely tohoto kurzu nasadíte dva moduly. Prvním je modul **tempSensor**, který simuluje snímač teploty a vlhkosti. Druhým modulem je vaše úloha Stream Analytics. Modul snímače poskytuje datový proud, který bude analyzovat dotaz vaší úlohy. 

1. Na webu Azure Portal přejděte ve službě IoT Hub na **IoT Edge** a otevřete stránku podrobností zařízení IoT Edge.

1. Vyberte **Nastavit moduly**.  

1. Pokud jste na zařízení už dříve nasadili modul tempSensor, může se automaticky doplnit. Pokud ne, přidejte ho následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul IoT Edge**.
   1. Jako název zadejte **tempSensor**.
   1. Jako identifikátor URI obrázku zadejte **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.

1. Přidejte úlohu Azure Stream Analytics Edge následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul Azure Stream Analytics**.
   1. Vyberte předplatné a úlohu Azure Stream Analytics Edge, kterou jste vytvořili. 
   1. Vyberte **Uložit**.

1. Po publikování úlohy Stream Analytics do kontejneru úložiště, který jste vytvořili, kliknutím na název modulu zobrazte strukturu modulu Stream Analytics. 

   Identifikátor URI image odkazuje na standardní image Azure Stream Analytics. Jedná se o stejnou image, která se používá pro každou úlohu, která se nasadí do zařízení IoT Edge. 

   Dvojče modulu má nakonfigurovanou požadovanou vlastnost **ASAJobInfo**. Hodnota této vlastnosti odkazuje na definici úlohy ve vašem kontejneru úložiště. Tato vlastnost určuje konfiguraci image Stream Analytics s konkrétními informacemi o vaší úloze. 

1. Zavřete stránku modulu.

1. Poznamenejte si název vašeho modulu Stream Analytics, protože ho budete potřebovat v dalším kroku, a pak pokračujte výběrem **Další**.

1. Nahraďte výchozí hodnotu v části **Trasy** následujícím kódem. Místo všech tří výskytů zástupného textu _{moduleName}_ zadejte název vašeho modulu Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Trasy, které zde deklarujete, definují tok dat zařízením IoT Edge. Telemetrická data z modulu tempSensor se odesílají do služby IoT Hub a do vstupu **temperature** nakonfigurovaného v úloze Stream Analytics. Výstupní zprávy **upozornění** se odesílají do služby IoT Hub a do modulu tempSensor, kde aktivují příkaz k resetování. 

1. Vyberte **Další**.

1. V kroku **Kontrola nasazení** vyberte **Odeslat**.

1. Vraťte se na obrazovku podrobností o zařízení a vyberte **Aktualizovat**.  

    Měl by se zobrazit spuštěný nový modul Stream Analytics společně s modulem agenta IoT Edge a centrem služby IoT Edge.

    ![Výstup modulu][7]

## <a name="view-data"></a>Zobrazení dat

Nyní můžete přejít do zařízení IoT Edge a zkontrolovat interakci mezi modulem Azure Stream Analytics a modulem tempSensor.

1. Zkontrolujte, že v Dockeru běží všechny moduly:

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
1. Zobrazte si všechny systémové protokoly a data metrik. Použijte název modulu Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Měli byste mít možnost sledovat, jak teplota přístroje pomalu stoupá, dokud nedosáhne 70 stupňů po dobu 30 sekund. Potom modul Stream Analytics aktivuje resetování a teplota přístroje klesne na 21. 

   ![Protokol Dockeru][9]

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali úlohu Azure Streaming Analytics, aby analyzovala data ze zařízení IoT Edge. Potom jste nahráli tento modul Azure Stream Analytics na zařízení IoT Edge, aby místně zpracovával a reagoval na zvýšení teploty a posílal stream agregovaných dat do cloudu. Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"] 
> [Nasazení modelu Azure Machine Learning jako modulu][lnk-ml-tutorial]

<!-- Images. -->
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

