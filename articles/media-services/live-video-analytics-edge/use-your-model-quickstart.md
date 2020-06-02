---
title: Analýza živého videa pomocí vlastního modelu – Azure
description: V tomto rychlém startu použijete počítačovou vizi k analýze živého kanálu videa z (simulované) kamery IP.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261970"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Rychlý Start: Analýza živého videa pomocí vlastního modelu

V tomto rychlém startu se dozvíte, jak pomocí analýzy živého videa na IoT Edge analyzovat kanál živého videa z (simulované) kamery IP pomocí modelu počítačové vize k detekci objektů. Podmnožina snímků v živém obrazovém kanálu se posílá do odvozené služby a její výsledky se odešlou do centra IoT Edge. Používá virtuální počítač Azure jako zařízení IoT Edge a simulovaný Stream živého videa. Tento článek je založen na vzorovém kódu napsaném v jazyce C#.

Tento článek se sestavuje nad [tímto](detect-motion-emit-events-quickstart.md) rychlým startem. 

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na počítači s následujícími příponami:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* V systému je nainstalovaná [sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) .
* Pokud jste [Tento](detect-motion-emit-events-quickstart.md) rychlý Start ještě nedokončili, dokončete následující kroky:
     * [Nastavení prostředků Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Při instalaci nástrojů Azure IoT se může zobrazit výzva k instalaci Docker. Klidně ho ignorujte.

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa
V rámci výše uvedeného postupu pro nastavení prostředků Azure se (krátké) video provozu na dálnici zkopíruje do virtuálního počítače Linux v Azure, který se používá jako zařízení IoT Edge. Tento videosoubor se použije k simulaci živého datového proudu pro tento kurz.

Můžete použít aplikaci, jako je [VLC Player](https://www.videolan.org/vlc/), spustit ji, stisknout CTRL + N a vložit [Tento](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) odkaz na video a zahájit přehrávání. Uvidíte, že záběr je v provozu na dálnici a na něm se pohybuje mnoho vozidel.

Po dokončení kroků uvedených níže budete pro detekci objektů, jako jsou například vozidla, osoby atd., používat živé video IoT Edge analýzy, a publikovat přidružené události odvození do centra IoT Edge.

## <a name="overview"></a>Přehled

![Přehled](./media/quickstarts/overview-qs5.png)

Výše uvedený diagram ukazuje, jak tok signalizuje v tomto rychlém startu. Modul Edge (popsaný [tady](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simuluje fotoaparát IP, který hostuje server RTSP. [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesor filtru rozpadu rámečku](media-graph-concept.md#frame-rate-filter-processor) . Tento procesor omezuje snímkovou frekvenci streamu videa, která se blíží uzlu [procesoru rozšíření http](media-graph-concept.md#http-extension-processor) . 

Uzel rozšíření HTTP přehraje roli proxy, a to tak, že převede snímky videa na zadaný typ obrázku a přenáší Image přes REST do jiného modulu Edge, který spouští model AI za koncovým bodem HTTP. V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který umožňuje detekci mnoha typů objektů. Uzel procesoru rozšíření HTTP shromažďuje výsledky detekce a publikuje události do [IoT Hubho uzlu jímky](media-graph-concept.md#iot-hub-message-sink ) , který pak tuto událost pošle do [centra IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.



## <a name="create-and-deploy-the-media-graph"></a>Vytvoření a nasazení mediálního grafu
    
### <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů

Jako součást požadavků byste stáhli vzorový kód do složky. Spusťte Visual Studio Code a otevřete složku.

1. V Visual Studio Code přejděte na "src/Edge". Zobrazí se soubor. ENV, který jste vytvořili společně s několika soubory šablon nasazení.

    * Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení s některými zástupnými hodnotami. Soubor. env obsahuje hodnoty pro tyto proměnné.
1. Pak přejděte do složky src/Cloud-to-Device-Console-App. Tady se zobrazí soubor appSettings. JSON, který jste vytvořili spolu s několika dalšími soubory:

    * C2D-Console-App. csproj – Toto je soubor projektu pro Visual Studio Code.
    * Operations. JSON – tento soubor bude mít přehled o různých operacích, které by měl program běžet.
    * Program.cs – jedná se o vzorový programový kód, který provede následující:

        * Načte nastavení aplikace.
        *  Vyvolá přímé metody vystavené živým analýzou videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md) . 
        * Pozastaví se, abyste prozkoumali výstup programu v okně terminálu a události generované modulem v okně výstup.
        * Vyvolá přímé metody pro vyčištění prostředků.   


1. Proveďte následující úpravy souboru Operations. JSON.
    * Změňte odkaz na topologii grafu:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * V části GraphInstanceSet upravte název topologie grafu tak, aby odpovídala hodnotě v odkazu výše.`"topologyName" : "InferencingWithHttpExtension"`
    * V části GraphTopologyDelete upravte název.`"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

1. Klikněte pravým tlačítkem na soubor src/Edge/Deployment. yolov3. template. JSON a klikněte na vygenerovat IoT Edge manifest nasazení.

    ![Generovat manifest nasazení IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. To by mělo vytvořit soubor manifestu ve složce src/Edge/config s názvem "Deployment. yolov3. amd64. JSON".
1. Pokud jste již dříve dokončili [rychlý Start](detect-motion-emit-events-quickstart.md), tento krok přeskočte. Jinak nastavte připojovací řetězec IoTHub kliknutím na ikonu Další akce vedle podokna AZURE IOT HUB v levém dolním rohu. Můžete zkopírovat řetězec ze souboru appSettings. JSON. (Tady je další doporučený postup, který zajistí, že máte správnou IoT Hub nakonfigurovanou v rámci Visual Studio Code pomocí [příkazu vybrat IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![Připojovací řetězec IoTHub](./media/quickstarts/set-iotconnection-string.png)
1. Potom klikněte pravým tlačítkem na "src/Edge/config/Deployment. yolov3. amd64. JSON" a klikněte na "vytvořit nasazení pro jedno zařízení". 

    ![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)
1. Pak budete požádáni o výběr zařízení IoT Hub. V rozevíracím seznamu vyberte lva-Sample-Device.
1. V přibližně 30 sekundách aktualizujte službu Azure IOT hub v levém dolním rohu a měli byste mít hraniční zařízení s nasazenými následujícími moduly:

    1. Modul Live video Analytics s názvem "lvaEdge".
    1. Modul nazvaný "rtspsim", který simuluje Server RTSP, který slouží jako zdroj živého informačního kanálu.
    1. Modul nazvaný "yolov3", který je navržen jako název, je model detekce objektu YOLOv3, který na obrázky aplikuje počítač a vrací více tříd typů objektů.
 
        ![Model detekce objektu YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Klikněte pravým tlačítkem na zařízení Live video Analytics a pak klikněte na spustit monitorování předdefinovaného koncového bodu události. Tento krok je nutný k monitorování událostí IoT Hub a jejich zobrazení v okně výstup Visual Studio Code. 

![Spustit monitorování](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Spuštění ukázkového programu

1. Spusťte ladicí relaci (stiskněte klávesu F5). Začnete zobrazovat některé zprávy, které se vytisknou v okně terminálu.
1. Operace Operations. JSON začíná voláním metody Direct Methods GraphTopologyList a GraphInstanceList. Pokud jste vyčistili prostředky po předchozích rychlých startech, vrátí se tím prázdné seznamy a potom se pozastaví, až se zahájí.
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

1. Po stisknutí klávesy ENTER v okně terminálu budou provedeny další sady přímých volání metody.
     * Volání GraphTopologySet pomocí topologyUrl výše
     * Volání GraphInstanceSet s použitím následujícího textu
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
     * Volání GraphInstanceActivate ke spuštění instance grafu a spuštění toku videa
     * Druhé volání GraphInstanceList k zobrazení, že instance grafu je ve stavu spuštěno.
1. Výstup v okně terminálu se teď pozastaví na příkazovém řádku pro pokračování stisknutím klávesy ENTER. V tuto chvíli nespustí "Enter". Můžete se posunout nahoru a podívat se na datové části odezvy JSON pro přímé metody, které jste vyvolali.
1. Pokud teď přepnete do okna výstup v Visual Studio Code, zobrazí se zprávy, které se odesílají do IoT Hubu, a to pomocí nástroje Live video Analytics v modulu IoT Edge.
     * Tyto zprávy jsou popsány v následující části.
1. Mediální graf bude nadále běžet a tisknout výsledky – simulátor RTSP bude pokračovat ve vytváření smyček zdrojového videa. Chcete-li zastavit graf médií, přejděte zpět do okna terminálu a stiskněte klávesu ENTER. Pro vyčištění prostředků se provedla další řada volání:
     * Volání GraphInstanceDeactivate k deaktivaci instance grafu
     * Volání GraphInstanceDelete pro odstranění instance
     * Volání GraphTopologyDelete k odstranění topologie
     * Konečné volání GraphTopologyList k zobrazení, že seznam je nyní prázdný

## <a name="interpret-results"></a>Interpretace výsledků

Když spustíte graf médií, výsledky z uzlu procesor rozšíření http se odešlou prostřednictvím uzlu IoT Hub jímka do IoT Hub. Zprávy, které vidíte v okně výstup Visual Studio Code, obsahují oddíl "tělo" a část "applicationProperties". Informace o tom, co tyto oddíly představuje, najdete v [tomto](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) článku.

Ve zprávách níže jsou vlastnosti aplikace a obsah těla definovány modulem Live video Analytics. 



### <a name="mediasession-established-event"></a>MediaSession vytvořená událost

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu na kontejneru rtspsim-live55. V případě úspěchu se tato událost vytiskne. Typ události je Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

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

Ve zprávě výše si všimněte následujícího:
* Zpráva je diagnostická událost MediaSessionEstablished, která indikuje, že uzel zdroje RTSP (předmět) byl schopný navázat spojení s simulátorem RTSP a zahájit příjem (simulovaného) živého kanálu.
* "Subject" v applicationProperties označuje, že zpráva byla vygenerována ze zdrojového uzlu RTSP v mediálním grafu.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o diagnostické události, což je v tomto případě podrobnosti o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Odvozená událost

Uzel procesoru rozšíření HTTP přijímá výsledky odvození z modulu yolov3 a generuje je prostřednictvím uzlu jímky IoT Hub jako odvozené události. V těchto událostech je typ nastaven na "entita", což označuje, že se jedná o entitu, jako je například automobil nebo nákladní vůz, a čas události oznamuje, v jakém čase (UTC) byl objekt zjištěn. Níže je příklad, kdy se dvě automobily zjistily s různou úrovní spolehlivosti ve stejném snímku videa.

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

Všimněte si následujících zpráv:

* "Subject" v applicationProperties odkazuje na uzel v topologii grafu, ze které byla zpráva vygenerována. 
* eventType v applicationProperties značí, že se jedná o analytickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o události analýzy. V tomto případě událost je odvozená událost, takže tělo obsahuje "odvozené" data.
* část "odvozené" označuje, že "typ" je "entita" a obsahuje další data o "entitě".

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento rychlý Start, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další výzvy pro pokročilé uživatele:

* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na stránce [ONVIF – vyhovujících](https://www.onvif.org/conformant-products/) produktů.
* Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) a potom postupujte podle pokynů v tomto tématu [nasazení prvního IoT Edge modulu do](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rychlého startu zařízení s nástrojem Virtual Linux a registrace zařízení v Azure IoT Hub.

