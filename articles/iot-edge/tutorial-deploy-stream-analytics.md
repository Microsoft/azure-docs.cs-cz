---
title: 'Kurz: Nasazení úloh ASA na zařízení Azure IoT Edge | Microsoft Docs'
description: V tomto kurzu nasadíte Azure Stream Analytics jako modul na zařízení IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: f2ef53ee53eb2e95d84fc11f3190f62d0e3c2455
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413871"
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

>[!NOTE]
>Moduly Azure Stream Analytics pro IoT Edge jsou ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).
* Modul Azure Machine Learning nepodporuje procesory ARM.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

V této části vytvoříte úlohu Azure Stream Analytics, která vezme data ze služby IoT Hub, vytvoří dotaz na odeslaná telemetrická data ze zařízení a potom výsledky předá do kontejneru úložiště objektů blob Azure. Další informace najdete v přehledu v [dokumentaci ke službě Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Pro úlohy Azure Stream Analytics je potřeba mít účet Azure Storage, který bude fungovat jako koncový bod pro výstup úlohy. V příkladu v této části používáme typ úložiště objektů blob. Další informace najdete v oddílu věnovaném objektům blob v [dokumentaci ke službě Azure Storage][azure-storage].

1. Na portálu Azure Portal přejděte na **Vytvořit prostředek**, do vyhledávacího pole zadejte **Storage account** a potom vyberte **Storage account - blob, file, table, queue**.

2. V podokně **Vytvořit účet úložiště** zadejte název účtu úložiště, vyberte stejné umístění, ve kterém je uložena služba IoT Hub, vyberte stejnou skupinu prostředků, kterou má i služba IoT Hub, a potom vyberte **Vytvořit**. Tento název si poznamenejte pro pozdější použití.

    ![vytvořit účet úložiště][1]


### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na webu Azure Portal přejděte na **Vytvořit prostředek** > **Internet věcí** a potom vyberte **Úloha Stream Analytics**.

2. V podokně **Nová úloha Stream Analytics** proveďte tyto kroky:

   1. Do pole **Název úlohy** zadejte název úlohy.
   
   2. Použijte stejnou **Skupinu prostředků** a **Umístění** jako u služby IoT Hub. 

      > [!NOTE]
      > V současné době se úlohy Azure Stream Analytics na zařízeních IoT Edge nepodporují v oblasti USA – západ 2. 

   3. V části **Hostitelské prostředí** vyberte **Edge**.
    
3. Vyberte **Vytvořit**.

4. Ve vytvořené úloze v části **Topologie úlohy** otevřete **Vstupy**.

   ![Vstup Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Vyberte **Přidat vstup streamu** a potom **Edge Hub**.

6. V podokně **Nový vstup** zadejte **teplota** jako alias pro vstup. 

7. Vyberte **Uložit**.

8. V části **Topologie úlohy** otevřete **Výstupy**.

   ![Výstup Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. Vyberte **Přidat** a potom **Edge Hub**.

10. V podokně **Nový výstup** zadejte **upozornění** jako alias pro výstup. 

11. Vyberte **Uložit**.

12. V části **Topologie úlohy** vyberte **Dotaz** a potom výchozí text nahraďte následujícím dotazem, který vytvoří upozornění v případě, že teplota přístroje v 30sekundovém intervalu dosáhne 70 stupňů:

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

13. Vyberte **Uložit**.

14. V části **Konfigurovat** vyberte **Nastavení IoT Edge**.

15. Z rozevíracího seznamu vyberte svůj **účet úložiště**.

16. V poli **Kontejner** vyberte **Vytvořit nový** a zadejte název kontejneru úložiště. 

17. Vyberte **Uložit**. 


## <a name="deploy-the-job"></a>Nasazení úlohy

Nyní jste připraveni nasadit úlohu Azure Stream Analytics na zařízení IoT Edge.

1. Na webu Azure Portal přejděte ve službě IoT Hub na **IoT Edge** a otevřete stránku podrobností zařízení IoT Edge.

2. Vyberte **Nastavit moduly**.  

   Pokud jste na zařízení už dříve nasadili modul tempSensor, může se automaticky doplnit. Pokud ne, přidejte ho následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul IoT Edge**.
   2. Jako název zadejte **tempSensor**.
   3. Jako identifikátor URI obrázku zadejte **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   4. Další nastavení ponechte beze změny.
   5. Vyberte **Uložit**.

3. Přidejte úlohu Azure Stream Analytics Edge následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul Azure Stream Analytics**.
   2. Vyberte předplatné a úlohu Azure Stream Analytics Edge, kterou jste vytvořili. 
   3. Vyberte **Uložit**.

4. Vyberte **Další**.

5. Nahraďte výchozí hodnotu v části **Trasy** následujícím kódem. Místo _{moduleName}_ zadejte název modulu Azure Stream Analytics. Modul by měl mít stejný název jako úloha, ze které jste ho vytvořili. 

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

6. Vyberte **Další**.

7. V kroku **Kontrola nasazení** vyberte **Odeslat**.

8. Vraťte se na obrazovku podrobností o zařízení a vyberte **Aktualizovat**.  

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
2. Zobrazte si všechny systémové protokoly a data metrik. Použijte název modulu Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Měli byste mít možnost sledovat, jak teplota přístroje pomalu stoupá, dokud nedosáhne 70 stupňů po dobu 30 sekund. Potom modul Stream Analytics aktivuje resetování a teplota přístroje klesne na 21. 

   ![Protokol Dockeru][9]

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

V tomto kurzu jste nakonfigurovali úlohu Azure Streaming Analytics, aby analyzovala data ze zařízení IoT Edge. Potom jste nahráli tento modul Azure Stream Analytics na zařízení IoT Edge, aby místně zpracovával a reagoval na zvýšení teploty a posílal stream agregovaných dat do cloudu. Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"] 
> [Nasazení modelu Azure Machine Learning jako modulu][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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

