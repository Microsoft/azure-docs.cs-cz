---
ms.openlocfilehash: 597a617f892ba00af13b4f88cad1eddf00f0d11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750665"
---
### <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů

Jako součást požadavků jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte ukázkové soubory.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení hraničního zařízení. Obsahuje některé zástupné hodnoty. Soubor *. env* obsahuje hodnoty pro tyto proměnné.
1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:

    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsna*** seznam operací, které má program spustit.
    * ***Program. cs*** – ukázkový kód programu Tento kód:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](../../../direct-methods.md).
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.
1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:

      `"topologyName" : "InferencingWithHttpExtension"`

    * V části `GraphTopologyDelete` upravte název:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

1. Klikněte pravým tlačítkem myši na soubor *Src/Edge/deployment.yolov3.template.jsv* souboru a potom vyberte možnost **generovat manifest nasazení IoT Edge**.

    ![Generovat manifest nasazení IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    *deployment.yolov3.amd64.js* v souboru manifestu se vytvoří ve složce *Src/Edge/config* .
1. Pokud jste dokončili rychlé zprovoznění [Najít pohyb a generovat události](../../../detect-motion-emit-events-quickstart.md) , tento krok přeskočte. 

    V opačném případě v levém dolním rohu poblíž podokna **Azure IoT Hub** vyberte ikonu **Další akce** a pak vyberte **nastavit IoT Hub připojovací řetězec**. Můžete zkopírovat řetězec z *appsettings.jsv* souboru. Nebo pokud chcete mít jistotu, že jste nakonfigurovali správné centrum IoT v rámci Visual Studio Code, použijte [příkaz vybrat IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Nastavit připojovací řetězec IoT Hub](../../../media/quickstarts/set-iotconnection-string.png)

    > [!NOTE]
    > Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Klikněte pravým tlačítkem na *Src/Edge/config/deployment.yolov3.amd64.jsna* a vyberte **vytvořit nasazení pro jedno zařízení**. 

    ![Vytvoření nasazení pro jedno zařízení](../../../media/quickstarts/create-deployment-single-device.png)
1. Až budete vyzváni k výběru zařízení IoT Hub, vyberte **lva-Sample-Device**.
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:

    * Modul Live video Analytics s názvem `lvaEdge` .
    * `rtspsim`Modul, který simuluje Server RTSP a funguje jako zdroj živého informačního kanálu.

        > [!NOTE]
        > Výše uvedené kroky předpokládají, že používáte virtuální počítač vytvořený instalačním skriptem. Pokud místo toho používáte vlastní hraniční zařízení, přečtěte si hraniční zařízení a spusťte následující příkazy s **právy správce**, abyste mohli načíst a uložit ukázkový videosoubor, který se používá pro tento rychlý Start:  
        
        ```
        mkdir /home/lvaedgeuser/samples
        mkdir /home/lvaedgeuser/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
        chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
        ```
        * `yolov3`Modul, což je model detekce objektu YoloV3, který aplikuje počítač na image a vrací více tříd typů objektů.
 
      ![Moduly, které jsou nasazené na hraničním zařízení](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Příprava na monitorování událostí

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::
1. Klikněte pravým tlačítkem na zařízení Live video Analytics a vyberte **Spustit sledování předdefinovaného koncového bodu události**. Tento krok potřebujete, pokud chcete monitorovat události IoT Hub v okně **výstup** Visual Studio Code. 

   ![Spustit monitorování](../../../media/quickstarts/start-monitoring-iothub-events.png) 

> [!NOTE]
> Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
### <a name="run-the-sample-program"></a>Spuštění ukázkového programu

1. Chcete-li spustit relaci ladění, vyberte klávesu F5. V okně **terminálu** se zobrazí zprávy tištěné.
1. *operations.js* kódu začíná s voláními přímých metod `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po dokončení předchozích rychlých startů, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Chcete-li pokračovat, vyberte klávesu ENTER.

   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "2.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

    V okně **terminálu** se zobrazí další sada volání přímých metod:

     * Volání `GraphTopologySet` , které používá předchozí `topologyUrl`
     * Volání `GraphInstanceSet` , které používá následující tělo:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```

     * Volání `GraphInstanceActivate` , které spustí instanci grafu a tok videa
     * Druhé volání, které `GraphInstanceList` ukazuje, že instance grafu je ve stavu spuštěno
1. Výstup v okně **terminálu** pozastaví `Press Enter to continue` výzvu. Ještě nevybírejte ENTER. Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Přepněte do okna **výstup** v Visual Studio Code. Zobrazí se zprávy, které modul analýza videa v IoT Edge posílá do služby IoT Hub. Následující část tohoto rychlého startu popisuje tyto zprávy.
1. Mediální graf bude nadále spouštět a tisknout výsledky. Simulátor RTSP zachovává smyčku zdrojového videa. Chcete-li zastavit graf médií, vraťte se do okna **terminálu** a vyberte Enter. 

    Další série volání vyčistí prostředky:
      * Volání `GraphInstanceDeactivate` deaktivuje instanci grafu.
      * Volání `GraphInstanceDelete` Odstraní instanci.
      * Volání pro `GraphTopologyDelete` odstranění topologie.
      * Konečné volání k `GraphTopologyList` ukáže, že seznam je prázdný.
