---
title: Analýza živého videa pomocí vlastního modelu – Azure
description: V tomto rychlém startu použijete počítačovou vizi k analýze živého kanálu videa z (simulované) kamery IP.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 75e18917b0d44dc33999d17360cd66a538c83d2b
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065195"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Rychlý Start: Analýza živého videa pomocí vlastního modelu

V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého kanálu videa z (simulované) kamery IP. Uvidíte, jak použít model počítačové vize k detekci objektů. Do odvozené služby se pošle podmnožina snímků v živém obrazovém kanálu. Výsledky se odesílají do centra IoT Edge. 

Tento rychlý Start používá virtuální počítač Azure jako zařízení IoT Edge a používá simulovaný živý Stream videa. Vychází z ukázkového kódu napsaného v jazyce C# a sestavuje se v rychlém startu pro [detekci pohybů a generování událostí](detect-motion-emit-events-quickstart.md) . 

## <a name="prerequisites"></a>Předpoklady

* Účet Azure, který zahrnuje aktivní předplatné. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Visual Studio Code](https://code.visualstudio.com/)s následujícími příponami:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Pokud jste nedokončili rychlé zprovoznění [Najít pohyb a generovat události](detect-motion-emit-events-quickstart.md) , nezapomeňte [nastavit prostředky Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Při instalaci nástrojů Azure IoT se může zobrazit výzva k instalaci Docker. Výzvu můžete ignorovat.

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa
Při nastavování prostředků Azure se krátké video provozního provozu zkopíruje do virtuálního počítače Linux v Azure, který používáte jako zařízení IoT Edge. V tomto rychlém startu se k simulaci živého streamu používá videosoubor.

Otevřete aplikaci, jako je [VLC Media Player](https://www.videolan.org/vlc/). Vyberte `Ctrl+N` a potom vložte odkaz na [ukázkové video s mezioddílem na dálnici](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) a začněte přehrávat. Vidíte, že se v provozu na dálnicích pohybuje záběr řady vozidel.

V tomto rychlém startu budete používat Live video Analytics na IoT Edge k detekci objektů, jako jsou například vozidla a osoby. Do centra IoT Edge publikujete přidružené události odvození.

## <a name="overview"></a>Přehled

![Přehled](./media/quickstarts/overview-qs5.png)

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server RTSP (Real-time streaming Protocol). [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a pošle snímky videa na uzel [procesoru filtru snímkové frekvence](media-graph-concept.md#frame-rate-filter-processor) . Tento procesor omezuje kmitočet snímků streamu videa, který se dorazí na uzel [procesoru rozšíření http](media-graph-concept.md#http-extension-processor) . 

Uzel rozšíření HTTP hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes REST do jiného modulu Edge, který spouští model AI za koncovým bodem HTTP. V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů. Uzel procesoru rozšíření HTTP shromáždí výsledky detekce a publikuje události do uzlu [IoT Hub jímka](media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.



## <a name="create-and-deploy-the-media-graph"></a>Vytvoření a nasazení mediálního grafu
    
### <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů

Jako součást požadavků jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte ukázkové soubory.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení hraničního zařízení. Obsahuje některé zástupné hodnoty. Soubor *. env* obsahuje hodnoty pro tyto proměnné.

1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:

    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsna*** seznam operací, které má program spustit.
    * ***Program.cs*** – vzorový programový kód. Tento kód:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md).
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.


1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:

      `"topologyName" : "InferencingWithHttpExtension"`

    * V části `GraphTopologyDelete` upravte název:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

1. Klikněte pravým tlačítkem myši na soubor *Src/Edge/deployment.yolov3.template.jsv* souboru a potom vyberte možnost **generovat manifest nasazení IoT Edge**.

    ![Generovat manifest nasazení IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    *deployment.yolov3.amd64.js* v souboru manifestu se vytvoří ve složce *Src/Edge/config* .

1. Pokud jste dokončili rychlé zprovoznění [Najít pohyb a generovat události](detect-motion-emit-events-quickstart.md) , tento krok přeskočte. 

    V opačném případě v levém dolním rohu poblíž podokna **Azure IoT Hub** vyberte ikonu **Další akce** a pak vyberte **nastavit IoT Hub připojovací řetězec**. Můžete zkopírovat řetězec z *appsettings.jsv* souboru. Nebo pokud chcete mít jistotu, že jste nakonfigurovali správné centrum IoT v rámci Visual Studio Code, použijte [příkaz vybrat IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Nastavit připojovací řetězec IoT Hub](./media/quickstarts/set-iotconnection-string.png)

1. Klikněte pravým tlačítkem na *Src/Edge/config/deployment.yolov3.amd64.jsna* a vyberte **vytvořit nasazení pro jedno zařízení**. 

    ![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)

1. Až budete vyzváni k výběru zařízení IoT Hub, vyberte **lva-Sample-Device**.
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:

    * Modul Live video Analytics s názvem`lvaEdge`
    * `rtspsim`Modul, který simuluje Server RTSP a funguje jako zdroj živého informačního kanálu
    > [!NOTE]
    > Pokud místo toho, které jste zřídili pomocí našeho skriptu pro instalaci, používáte vlastní hraniční zařízení, přečtěte si příslušné hraniční zařízení a spusťte následující příkazy s **právy správce**, abyste mohli načíst a uložit ukázkový videosoubor, který se používá pro tento rychlý Start:  

    ```
    mkdir /home/lvaadmin/samples
    mkdir /home/lvaadmin/samples/input    
    curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
    chown -R lvaadmin /home/lvaadmin/samples/  
    ```
    * `yolov3`Modul, což je model detekce objektu YoloV3, který aplikuje počítač na image a vrací více tříd typů objektů.
 
      ![Moduly, které jsou nasazené na hraničním zařízení](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Příprava na monitorování událostí

Klikněte pravým tlačítkem na zařízení Live video Analytics a vyberte **Spustit sledování předdefinovaného koncového bodu události**. Tento krok potřebujete, pokud chcete monitorovat události IoT Hub v okně **výstup** Visual Studio Code. 

![Spustit monitorování](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Spuštění ukázkového programu

1. Chcete-li spustit relaci ladění, vyberte klávesu F5. V okně **terminálu** se zobrazí zprávy tištěné.
1. *operations.js* kódu začíná s voláními přímých metod `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po dokončení předchozích rychlých startů, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Chcete-li pokračovat, vyberte klávesu ENTER.

   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
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

     * Volání `GraphTopologySet` , které používá předchozí`topologyUrl`
     * Volání `GraphInstanceSet` , které používá následující tělo:

         ```
         {
           "@apiVersion": "1.0",
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

## <a name="interpret-results"></a>Interpretace výsledků

Když spustíte graf médií, výsledky z uzlu procesor rozšíření HTTP procházejí uzlem IoT Hub jímky do služby IoT Hub. Zprávy, které vidíte v okně **výstup** , obsahují `body` oddíl a `applicationProperties` oddíl. Další informace najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách modul Live video Analytics definuje vlastnosti aplikace a obsah těla. 

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP, který běží na kontejneru rtspsim-live555. Pokud je připojení úspěšné, bude vytištěna následující událost. Typ události je `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

V této zprávě si všimněte těchto podrobností:

* Zpráva je událost diagnostiky. `MediaSessionEstablished`indikuje, že zdrojový uzel RTSP (předmět) připojený k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V `applicationProperties` nástroji `subject` označuje, že zpráva byla vygenerována ze zdrojového uzlu RTSP v mediálním grafu.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je událost diagnostiky.
* `eventTime`Určuje čas, kdy došlo k události.
* `body`Obsahuje data o události diagnostiky. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Odvozená událost

Uzel procesoru rozšíření HTTP přijímá výsledky odvození z modulu yolov3. Následně vygeneruje výsledky prostřednictvím uzlu IoT Hub jímka jako odvozené události. 

V těchto událostech je typ nastaven na hodnotu, `entity` aby označoval, že se jedná o entitu, jako je například automobil nebo nákladní automobil. `eventTime`Hodnota je čas UTC při zjištění objektu. 

V následujícím příkladu byly ve stejném snímku videa zjištěny dvě automobily s různou úrovní spolehlivosti.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

V části zprávy si všimněte následujících podrobností:

* V `applicationProperties` aplikaci `subject` odkazuje na uzel v topologii grafu, ze které byla zpráva generována. 
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je analytická událost.
* `eventTime`Hodnota je čas, kdy došlo k události.
* `body`Oddíl obsahuje data o události analýzy. V tomto případě je událost odvozená událost, takže tělo obsahuje `inferences` data.
* `inferences`Část označuje, že `type` je `entity` . Tato část obsahuje další informace o entitě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet další rychlé starty, ponechte prostředky, které jste vytvořili. V opačném případě přejdete na Azure Portal, přejdete do skupin prostředků, vyberete skupinu prostředků, ve které jste spustili tento rychlý Start, a odstraníte všechny prostředky.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte [zabezpečenou verzi modelu YoloV3](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) a nasaďte ji do zařízení IoT Edge. 

Přečtěte si další výzvy pro pokročilé uživatele:

* Místo používání simulátoru RTSP použijte [kameru IP](https://en.wikipedia.org/wiki/IP_camera) , která má podporu pro RTSP. Můžete vyhledat kamery protokolu IP, které podporují protokol RTSP na stránce ONVIF, která je v [souladu](https://www.onvif.org/conformant-products/) s těmito produkty. Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
* Místo virtuálního počítače Azure Linux použijte zařízení AMD64 nebo x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Pak zařízení Zaregistrujte v Azure IoT Hub podle pokynů v tématu [nasazení prvního modulu IoT Edge na zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md).
