---
title: Nasazení služby Azure Stream Analytics s hranou Azure IoT | Microsoft Docs
description: Nasazení služby Azure Stream Analytics jako modul pro hraniční zařízení
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5d4c2b3bc55b94b08287a06125e15ac61013834a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Nasazení služby Azure Stream Analytics jako modul IoT Edge – náhled

Zařízení IoT může vytvářet velké objemy dat. Ke snížení objemu odeslaná data nebo omezit čekací doba přenosu řešitelné přehledu data někdy analyzovat nebo zpracovat předtím, než dorazí do cloudu.

Azure IoT Edge využívá výhod předdefinovaných služby Azure IoT Edge moduly pro rychlé nasazení. [Azure Stream Analytics] [ azure-stream] je jeden takový modul. Můžete vytvořit úlohu služby Azure Stream Analytics z jeho portálu a potom přejděte na portál Azure IoT Hub a nasaďte ho jako modul IoT okraj. 

Azure Stream Analytics poskytuje syntaxe bohatě strukturovaných dotazů pro analýzu dat v cloudu i na IoT hraniční zařízení. Další informace o Azure Stream Analytics hranu IoT najdete v tématu [dokumentace Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Tento kurz vás provede vytvořením úlohu služby Azure Stream Analytics a jeho nasazení na IoT hraniční zařízení. Tímto způsobem můžete zpracovat datový proud telemetrie místní přímo na zařízení a generování výstrah, jež řídí okamžitý zásah na zařízení. 

Tento kurz představuje dva moduly: 
* Simulované teplotní snímač modulu (tempSensor), který generuje teploty data z 20 do 120 stupňů, zvýší o 1 každých 5 sekund. 
* Stream Analytics modul, který resetuje tempSensor dosáhne 70-průměru 30 sekund. V produkčním prostředí můžete použít tuto funkci vypnuté na počítači nebo přijmout preventivní opatření, když teplota dosáhne nebezpečná úrovně. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte úlohu služby Azure Stream Analytics zpracováním dat na okraj.
> * Připojte nové úlohy Azure Stream Analytics s další moduly IoT okraj.
> * Nasaďte úlohu služby Azure Stream Analytics IoT hraniční zařízení.

## <a name="prerequisites"></a>Požadavky

* Centrum IoT. 
* Zařízení, které jste vytvořili a nakonfigurovat v rychlé spuštění, nebo v článcích o nasazení v simulovaném zařízení v Azure IoT Edge [Windows] [ lnk-tutorial1-win] nebo v [Linux] [ lnk-tutorial1-lin]. Je nutné znát klíč připojení zařízení a ID zařízení. 
* Docker spuštění na vašem zařízení IoT okraj.
    * [V systému Windows nainstalujte Docker][lnk-docker-windows].
    * [Nainstalujte Docker v systému Linux][lnk-docker-linux].
* Python 2.7.x na vašem zařízení IoT okraj.
    * [Instalace jazyka Python 2.7 v systému Windows][lnk-python].
    * Většina Linuxových distribucích, včetně Ubuntu, už máte Python 2.7 nainstalována. Chcete-li zkontrolujte, zda je nainstalován pip, použijte následující příkaz: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Umožňuje vytvořit úlohu služby Azure Stream Analytics

V této části můžete vytvořit úlohu služby Azure Stream Analytics využít data ze služby IoT hub, dotaz odeslaný telemetrická data ze zařízení a pak je odeslat výsledky do kontejner úložiště objektů Blob v Azure. Další informace najdete v části "Přehled" [Stream Analytics dokumentaci][azure-stream]. 

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet služby Azure Storage je nutné k zajištění koncový bod má být použit jako výstup ve vaší úloze Azure Stream Analytics. Příklad v této části používá typ úložiště objektů Blob. Další informace najdete v části "Objekty BLOB" [dokumentaci pro Azure Storage][azure-storage].

1. V portálu Azure přejděte do **vytvořit prostředek**, zadejte **účet úložiště** v vyhledávacího pole a potom vyberte **účet úložiště – objekt blob, soubor, tabulka, fronta**.

2. V **vytvořit účet úložiště** podokně, zadejte název účtu úložiště, vyberte stejné umístění, kde jsou uložené služby IoT hub, vyberte stejné skupině prostředků jako služby IoT hub a pak vyberte **vytvořit**. Poznamenejte si název pro pozdější použití.

    ![vytvořit účet úložiště][1]

3. Přejděte na účet úložiště, který jste právě vytvořili a pak vyberte **procházet objekty BLOB**. 

4. Vytvořit nový kontejner pro modul Azure Stream Analytics k ukládání dat, nastavení přístupu na úrovni **kontejneru**a potom vyberte **OK**.

    ![Nastavení úložiště][10]

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. V portálu Azure přejděte do **vytvořit prostředek** > **Internet věcí**a potom vyberte **úlohy Stream Analytics**.

2. V **nové úlohy Stream Analytics** podokně, postupujte takto:

    a. V **název úlohy** zadejte název úlohy.
    
    b. V části **hostitelské prostředí**, vyberte **Edge**.
    
    c. Zbývající pole použijte výchozí hodnoty.

    > [!NOTE]
    > Úlohy Azure Stream Analytics IoT hranu nejsou aktuálně podporované v oblasti západní USA 2. 

3. Vyberte **Vytvořit**.

4. Ve vytvořené úloze pod **úlohy topologie**, otevřete **vstupy**.

   ![Azure Stream Analytics vstup](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Vyberte **přidat vstup datového proudu**, pak vyberte **Edge rozbočovače**.

5. V **nové vstup** podokně zadejte **teploty** jako vstupní alias. 

6. Vyberte **Uložit**.

7. V části **úlohy topologie**, otevřete **výstupy**.

   ![Výstup Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

8. Vyberte **přidat**, pak vyberte **Edge rozbočovače**.

8. V **nový výstupní** podokně zadejte **výstraha** jako alias pro výstup. 

9. Vyberte **Vytvořit**.

9. V části **úlohy topologie**, vyberte **dotazu**a výchozí text nahraďte následující dotaz:

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

10. Vyberte **Uložit**.

## <a name="deploy-the-job"></a>Úloha nasazení

Nyní jste připraveni k nasazení úlohy Azure Stream Analytics na vašem zařízení IoT okraj.

1. Na portálu Azure ve službě IoT hub, přejděte na **IoT okraj (preview)** a pak otevřete stránku podrobností pro vaše zařízení IoT okraj.

2. Vyberte **Set modules** (Nastavit moduly).  
    Pokud jste předtím nasadili modul tempSensor na tomto zařízení, může být autopopulate. Pokud není, přidejte modul následujícím způsobem:

   a. Vyberte **Přidat modul IoT Edge**.

   b. Pro název, typ **tempSensor**.
    
   c. Pro bitovou kopii identifikátor URI, zadejte **microsoft azureiotedge-simulated teploty-senzor: 1.0-preview**. 

   d. Nechte ostatní nastavení beze změny.
   
   e. Vyberte **Uložit**.

3. Chcete-li přidat úlohu Azure Stream Analytics Edge, vyberte **Import Azure Stream Analytics IoT Edge Module**.

4. Vyberte předplatné a úlohy Azure Stream Analytics okraj, který jste vytvořili. 

5. Vyberte předplatné a účet úložiště, který jste vytvořili a potom vyberte **Uložit**.

    ![Sada modulu][6]

6. Název modulu Azure Stream Analytics zkopírujte. 

    ![teplotní modulu][11]

7. Pokud chcete nakonfigurovat směrování, vyberte **Další**.

8. Zkopírujte následující kód do **trasy**. Nahraďte _{moduleName}_ s názvem modulu, který jste zkopírovali:

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

9. Vyberte **Next** (Další).

10. V kroku **kontroly šablony** vyberte **Submit** (Odeslat).

11. Vraťte se na stránce s podrobnostmi o zařízení a potom vyberte **aktualizovat**.  
    Měli byste vidět nového modulu Stream Analytics spuštěna, společně s modul agenta IoT okraj a okraj IoT hub.

    ![výstup modulu][7]

## <a name="view-data"></a>Zobrazení dat

Nyní můžete přejít na zařízení IoT Edge rezervovat interakce mezi tempSensor modul a modulu Azure Stream Analytics.

1. Zkontrolujte, zda jsou spuštěny všechny moduly v Docker:

   ```cmd/sh
   docker ps  
   ```

   ![výstup docker][8]

2. Zobrazte všechna data metrik a protokolování systému. Použijte název modulu Stream Analytics:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Nyní byste měli mít podívejte se na tento počítač teploty postupně roste, dokud nebude dosaženo 70 stupňů po dobu 30 sekund. Pak spustí modul Stream Analytics provést obnovení, a počítač teploty vrátí k 21. 

   ![docker protokolu][9]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali kontejner úložiště Azure a úlohu streamování Analytics k analýze dat ze zařízení IoT okraj. Pak načten vlastní modul Azure Stream Analytics pro přesun dat ze zařízení prostřednictvím datového proudu, do objektu blob pro stahování. Informace o tom, jak Azure IoT okraj můžete vytvořit další řešení pro vaši firmu, pokračování do dalších kurzech.

> [!div class="nextstepaction"] 
> [Nasadit model Azure Machine Learning jako modul][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
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
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
