---
title: Kurz pro nahrávání a přehrávání videa založeného na událostech z cloudového kurzu – Azure
description: V tomto kurzu se naučíte používat Azure Live video Analytics na Azure IoT Edge k nahrání záznamu videa založeného na událostech do cloudu a jeho přehrání z cloudu.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 497c512c4d3874959e26b7a11c312abdda475b4e
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491854"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Kurz: nahrávání videa založeného na událostech do cloudu a přehrávání z cloudu

V tomto kurzu se naučíte používat Azure Live video Analytics na Azure IoT Edge k selektivnímu nahrávání částí zdroje živého videa do Azure Media Services v cloudu. Tento případ použití se v tomto kurzu označuje jako [záznam videa založený na událostech](event-based-video-recording-concept.md) (EVR). Chcete-li zaznamenat části živého videa, použijte model AI pro detekci objektů pro hledání objektů ve videu a záznamu videoklipů pouze v případě, že je zjištěn určitý typ objektu. Naučíte se také, jak přehrát nahrané video klipy pomocí Media Services. Tato možnost je užitečná pro celou řadu scénářů, kdy je potřeba uchovat archiv videoklipů, které vás zajímají. 

V tomto kurzu:

> [!div class="checklist"]
> * Nastavte příslušné prostředky.
> * Projděte si kód, který provádí EVR.
> * Spusťte vzorový kód.
> * Prohlédněte si výsledky a podívejte se na video.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Navrhované před čtením  

Než začnete, přečtěte si tyto články:

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncepty Media graphu](media-graph-concept.md) 
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)
* [Kurz: vývoj modulu IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Postup úpravy nasazení. * .template.js](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Oddíl, [jak deklarovat trasy v manifestu nasazení IoT Edge](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Požadavky

Předpoklady pro tento kurz:

* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a rozšíření [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Může se zobrazit výzva k instalaci Docker. Ignorovat tuto výzvu.
* [Sada SDK .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na vašem vývojovém počítači.
* Dokončete [skript nastavení prostředků nástroje Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)a [nastavte prostředí](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?pivots=programming-language-csharp#set-up-your-development-environment) .

Na konci tohoto postupu budete mít v předplatném Azure nasazené relevantní prostředky Azure:

* Azure IoT Hub
* Účet služby Azure Storage
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](../../iot-edge/how-to-install-iot-edge.md)

> [!TIP]
> Pokud narazíte na problémy s prostředky Azure, které se vytvoří, přečtěte si náš **[Průvodce odstraňováním potíží](troubleshoot-how-to.md#common-error-resolutions)** a vyřešte některé běžně zjištěné problémy.

## <a name="concepts"></a>Koncepty

Nahrávání videa založeného na událostech odkazuje na proces nahrávání videa aktivovaného událostí. Tato událost může být vygenerována:
- Zpracování samotného hlasového signálu, například při zjištění pohybujícího se objektu ve videu.
- Nezávislý zdroj, například otevření dveří. 

Případně můžete spustit záznam pouze v případě, že služba Inferencing zjistí, že došlo k určité události. V tomto kurzu použijete video o vozidlech pohybujících se na dálnici a zaznamenání videoklipů pokaždé, když se zjistí nákladní vůz.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Graf médií":::

Diagram je obrazové znázornění [mediálního grafu](media-graph-concept.md) a dalších modulů, které provádějí požadovaný scénář. Jsou zapojené čtyři IoT Edge moduly:

* Live video Analytics v modulu IoT Edge.
* Modul Edge, který spouští model AI za koncovým bodem HTTP. Tento modul AI používá model [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů.
* Vlastní modul pro počítání a filtrování objektů, který se označuje jako čítač objektu v diagramu. Sestavíte čítač objektu a nasadíte ho v tomto kurzu.
* [Modul simulátoru RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pro simulaci kamery RTSP.
    
Jak znázorňuje diagram, použijete v mediálním grafu [zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) k zachycení simulovaného živého videa na dálnici a odeslání tohoto videa na dvě cesty:

* První cesta je uzel rozšíření HTTP. Uzel navzorkuje snímky videa na hodnotu nastavenou pomocí `samplingOptions` pole a pak přenáší snímky jako obrázky do modulu AI YOLOv3, což je objektový detektor. Uzel obdrží výsledky, což jsou objekty (vozidla v provozu) zjištěné modelem. Uzel rozšíření HTTP pak tyto výsledky publikuje prostřednictvím uzlu jímky zpráv IoT Hub do centra IoT Edge.
* Modul objectCounter je nastavený tak, aby přijímal zprávy z centra IoT Edge, které zahrnuje výsledky detekce objektu (vozidla v provozu). Modul tyto zprávy zkontroluje a vyhledá objekty určitého typu, které byly nakonfigurovány prostřednictvím nastavení. Když takový objekt najde, tento modul pošle zprávu do centra IoT Edge. Zprávy o nalezených objektech se pak přesměrují do zdrojového uzlu IoT Hub v mediálním grafu. Po přijetí takové zprávy IoT Hub zdrojový uzel v mediálním grafu aktivuje uzel [procesoru brány signálu](media-graph-concept.md#signal-gate-processor) . Uzel procesoru brány signálu se pak otevře po nakonfigurované době. Video prochází bránou do uzlu jímka assetu za tuto dobu. Tato část živého streamu se pak prostřednictvím uzlu [jímka assetu](media-graph-concept.md#asset-sink) zaznamená do [assetu](terminology.md#asset) v účtu Azure Media Services.

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

Než začnete, ověřte, že jste dokončili třetí odrážku v části [požadavky](#prerequisites). Po dokončení skriptu pro nastavení prostředků vyberte složené závorky, které budou vystavovat strukturu složek. Zobrazí se několik souborů vytvořených v adresáři ~/clouddrive/lva-Sample.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Nastavení aplikace":::

Z zájmu tohoto kurzu jsou soubory:

* **~/clouddrive/lva-Sample/Edge-Deployment/.env**: obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
* **~/clouddrive/lva-sample/appsetting.json**: používá Visual Studio Code ke spuštění ukázkového kódu.

Budete potřebovat soubory pro tyto kroky.

1. Naklonujte úložiště z odkazu na GitHub https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Spusťte Visual Studio Code a otevřete složku, do které jste stáhli úložiště.
1. V Visual Studio Code přejděte do složky src/Cloud-to-Device-Console-App-Device-Console-Device-Console-a vytvořte soubor s názvem **appsettings.js**. Tento soubor obsahuje nastavení potřebná ke spuštění programu.
1. Kopírovat obsah z souboru ~/clouddrive/lva-Sample/appsettings.js. Text by měl vypadat takto:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Připojovací řetězec IoT Hub umožňuje používat Visual Studio Code k posílání příkazů do hraničních modulů přes Azure IoT Hub.
    
1. Potom přejděte do složky src/Edge a vytvořte soubor s názvem **. env**.
1. Zkopírujte obsah ze souboru ~/clouddrive/lva-Sample/Edge-Deployment/.env. Text by měl vypadat takto:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Kontrola souboru šablony 

V předchozím kroku jste začali Visual Studio Code a otevřeli složku, která obsahuje vzorový kód.

V Visual Studio Code přejděte na src/Edge. Zobrazí se soubor. ENV, který jste vytvořili, a několik souborů šablon nasazení. Tato šablona definuje, které moduly Edge nasadíte do hraničního zařízení (virtuální počítač Azure Linux). Soubor. env obsahuje hodnoty pro proměnné používané v těchto šablonách, například přihlašovací údaje Media Services.

Otevřete src/Edge/deployment.objectCounter.template.jsna. V části **modulů** jsou čtyři položky, které odpovídají položkám uvedeným v předchozích částech "koncepty":

* **lvaEdge**: Toto je Live video Analytics v modulu IoT Edge.
* **yolov3**: Jedná se o modul AI sestavený pomocí modelu Yolo v3.
* **rtspsim**: Toto je simulátor RTSP.
* **objectCounter**: Jedná se o modul, který hledá konkrétní objekty ve výsledcích z yolov3.

Pro modul objectCounter se podívejte na řetězec ($ {MODULes. objectCounter}), který se používá pro hodnotu image. Vychází z [kurzu](../../iot-edge/tutorial-develop-for-linux.md) vývoje IoT Edge modulu. Visual Studio Code automaticky rozpozná, že kód pro modul objectCounter je pod položkou src/Edge/Module/objectCounter. 

Přečtěte si [Tento oddíl](../../iot-edge/module-composition.md#declare-routes) , jak deklarovat trasy v manifestu nasazení IoT Edge. Pak zkontrolujte trasy v souboru JSON šablony. Všimněte si, jak:

* LVAToObjectCounter se používá k odesílání konkrétních událostí do konkrétního koncového bodu v modulu objectCounter.
* ObjectCounterToLVA se používá k odeslání události triggeru do konkrétního koncového bodu (který by měl být IoT Hub zdrojový uzel) v modulu lvaEdge.
* objectCounterToIoTHub se používá jako ladicí nástroj, který vám může při spuštění tohoto kurzu zobrazit výstup z objectCounter.

> [!NOTE]
> Zkontrolujte požadované vlastnosti modulu objectCounter, který je nastavený tak, aby hledal objekty, které jsou označené jako "nákladní", s úrovní spolehlivosti minimálně 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge 

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení a nastavení konfigurace pro tyto moduly. Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

Pomocí Visual Studio Code se přihlaste k Docker podle [těchto pokynů](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) . Pak vyberte **sestavení a nabízená IoT Edge řešení**. Pro tento krok použijte src/Edge/deployment.objectCounter.template.js.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Sestavování a nabízených IoT Edge řešení":::

Tato akce vytvoří modul objectCounter pro počítání objektů a vloží image do vašeho Azure Container Registry.

* Ověřte, zda máte proměnné prostředí CONTAINER_REGISTRY_USERNAME_myacr a CONTAINER_REGISTRY_PASSWORD_myacr definovány v souboru. env.

Tento krok vytvoří manifest nasazení IoT Edge v umístění src/Edge/config/deployment.objectCounter.amd64.jsna. Klikněte pravým tlačítkem na daný soubor a vyberte **vytvořit nasazení pro jedno zařízení**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Vytvoření nasazení pro jedno zařízení":::

Pokud se jedná o váš první kurz se službou Live video Analytics na IoT Edge, Visual Studio Code vás vyzve k zadání připojovacího řetězce IoT Hub. Můžete ho zkopírovat z appsettings.jsv souboru.

> [!NOTE]
> Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Dále Visual Studio Code požádá o výběr zařízení IoT Hub. Vyberte zařízení IoT Edge, které by mělo být lva-Sample-Device.

V této fázi se spustilo nasazení hraničních modulů do zařízení IoT Edge.
Během přibližně 30 sekund aktualizujte IoT Hub Azure v části vlevo dole v Visual Studio Code. Měli byste vidět, že jsou nasazené čtyři moduly s názvem lvaEdge, rtspsim, yolov3 a objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Nasazené čtyři moduly":::

## <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Pokud se chcete podívat na události z modulu objectCounter a ve službě Live video Analytics v modulu IoT Edge, postupujte podle následujících kroků:

1. Otevřete podokno Průzkumník v Visual Studio Code a hledejte **Azure IoT Hub** v levém dolním rohu.
1. Rozbalte uzel **zařízení** .
1. Klikněte pravým tlačítkem myši na soubor lva-Sample-Device a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Spustit sledování vestavěného koncového bodu události":::
    
## <a name="run-the-program"></a>Spuštění programu

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::
1. <!--In Visual Studio Code, go-->V systému použijte src/Cloud-to-Device-Console-App/operations.js.
1. V uzlu **GraphTopologySet** upravte následující položky:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Dále v uzlech **GraphInstanceSet** a **GraphTopologyDelete** upravte:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Spusťte ladicí relaci výběrem F5. V okně **terminálu** se zobrazí zprávy, které se vytisknou.
1. operations.jsv souboru začíná s voláními GraphTopologyList a GraphInstanceList. Pokud jste vyčistili prostředky po předchozích rychlých startech nebo kurzech, tato akce vrátí prázdné seznamy a pozastaví se, abyste vybrali možnost **zadat**, jak je znázorněno níže:

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
1. Po výběru **ENTER** v okně **terminálu** se provede další sada přímých volání metody:
   * Volání GraphTopologySet pomocí předchozího topologyUrlu
   * Volání GraphInstanceSet pomocí následujícího textu
     
        ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
    
   * Volání GraphInstanceActivate ke spuštění instance grafu a zahájení toku videa
   * Druhé volání GraphInstanceList k zobrazení, že instance grafu je ve stavu spuštěno.
     
1. Výstup v okně **terminálu** se nyní pozastaví **stisknutím klávesy ENTER a zobrazí se výzva k pokračování** . V tuto chvíli nevybírejte **ENTER** . Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Pokud teď přepnete do okna **výstup** v Visual Studio Code, zobrazí se zprávy odesílané do IoT Hub pomocí nástroje Live video Analytics v modulu IoT Edge.

   Tyto zprávy jsou popsány v následující části.
1. Instance grafu pokračuje v běhu a zaznamená video. Simulátor RTSP zachovává smyčku zdrojového videa. Zkontrolujte zprávy, jak je popsáno v následující části. Pak můžete instanci zastavit, přejít zpátky do okna **terminálu** a vybrat **ENTER**. K vyčištění prostředků se provede další řada volání pomocí:

   * Volání GraphInstanceDeactivate k deaktivaci instance grafu.
   * Volání GraphInstanceDelete pro odstranění instance.
   * Volání GraphTopologyDelete k odstranění topologie.
   * Konečné volání GraphTopologyList k zobrazení, že seznam je nyní prázdný.

## <a name="interpret-the-results"></a>Interpretace výsledků 

Když spustíte graf Media Graph, Live video Analytics v modulu IoT Edge odešle určité diagnostické a provozní události do centra IoT Edge. Tyto události jsou zprávy, které se zobrazí v okně **výstup** Visual Studio Code. Obsahují oddíl tělo a oddíl applicationProperties. Informace o tom, co tyto oddíly představuje, najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách jsou vlastnosti aplikace a obsah těla definovány modulem Live video Analytics.

## <a name="diagnostics-events"></a>Diagnostické události

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished 

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu na kontejneru simulátoru RTSP. V případě úspěchu se tato událost vytiskne. Typ události je **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Zpráva je událost diagnostiky (MediaSessionEstablished). Indikuje, že zdrojový uzel RTSP (předmět) navázal spojení s simulátorem RTSP a začal přijímat (simulované) živé kanály.
* Oddíl Subject v applicationProperties odkazuje na uzel v topologii grafu, ze kterého byla zpráva vygenerována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* Oddíl eventType v applicationProperties označuje, že se jedná o diagnostickou událost.
* Část čas události označuje čas, kdy došlo k události. To je čas, kdy video (soubor MKV) začalo dorazit do modulu jako živý datový proud.
* Oddíl Text obsahuje data o diagnostické události, která je v tomto případě podrobnostmi o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="operational-events"></a>Provozní události

Jakmile se mediální graf spustí po nějakou dobu, nakonec získáte událost z modulu objectCounter. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

Část applicationProperties obsahuje dobu události. To je čas, kdy se v modulu objectCounter zjistilo, že výsledky z modulu yolov3 obsahují objekty zájmu.

Můžou se zobrazit další informace o těchto událostech, když se ve videu zjistí další nákladní automobily.

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Skoro hned po čítači objektu odešle událost se zobrazí událost typu **Microsoft. Media. Graph. Operational. RecordingStarted**:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

Oddíl Subject v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu. Oddíl Text obsahuje informace o umístění výstupu. V tomto případě se jedná o název prostředku Azure Media Services, do kterého se nahraje video. Tuto hodnotu si poznamenejte.

### <a name="recordingavailable-event"></a>Událost RecordingAvailable

Když uzel jímka assetu nahrál video do assetu, vygeneruje tuto událost typu **Microsoft. Media. Graph. Operational. RecordingAvailable**:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Tato událost označuje, že do assetu se zapsala dostatečná data pro aktéry nebo klienty, aby si mohli začít přehrávat video. Oddíl Subject v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu. Oddíl Text obsahuje informace o umístění výstupu. V tomto případě se jedná o název prostředku Azure Media Services, do kterého se nahraje video.

### <a name="recordingstopped-event"></a>Událost RecordingStopped

Pokud prohlížíte nastavení aktivace (maximumActivationTime) pro uzel procesoru brány signálu v [topologii](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), uvidíte, že je tato brána nastavená tak, aby se zavřela po 30 sekundách odeslání videa prostřednictvím. Přibližně 30 sekund po události RecordingStarted by se měla zobrazit událost typu **Microsoft. Media. Graph. Operational. RecordingStopped**. Tato událost označuje, že uzel jímky assetu zastavil nahrávání videa do assetu.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Tato událost označuje, že záznam byl zastaven. Oddíl Subject v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu. Oddíl Text obsahuje informace o umístění výstupu. V tomto případě se jedná o název prostředku Azure Media Services, do kterého se nahraje video.

## <a name="media-services-asset"></a>Prostředek Media Services  

Můžete prozkoumávat Media Services Asset, který byl vytvořen grafem, přihlášením k Azure Portal a zobrazením videa.

1. Otevřete webový prohlížeč a přejdete na [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.
1. Vyhledejte účet Media Services mezi prostředky, které máte ve svém předplatném. Otevřete podokno účet.
1. V seznamu **Media Services** vyberte **assety** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Nepřetržité nahrávání videa":::
1. Najdete seznam prostředků s názvem sampleAssetFromEVR-LVAEdge-{DateTime}. Toto je název, který jste zadali ve vlastnosti outputLocation události RecordingStarted. AssetNamePattern v topologii určuje, jak se tento název vygeneroval.
1. Vyberte asset.
1. Na stránce Podrobnosti o aktivech vyberte **vytvořit novou** v poli **Adresa URL streamování** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Nový prostředek":::
1. V průvodci, který se otevře, přijměte výchozí možnosti a vyberte **Přidat**. Další informace najdete v tématu [přehrávání videa](video-playback-concept.md).

    > [!TIP]
    > Ujistěte se, že [koncový bod streamování běží](../latest/streaming-endpoint-concept.md).
1. Přehrávač by měl načíst video. Vyberte **Přehrát** a zobrazte ho.

> [!NOTE]
> Vzhledem k tomu, že zdroj videa byl kontejner simulující kanál kamery, časová razítka ve videu souvisí s tím, že jste aktivovali instanci grafu a když ji deaktivujete. Pokud používáte ovládací prvky pro přehrávání, které jsou integrované v kurzu [přehrávání více dní](playback-multi-day-recordings-tutorial.md) , můžete zobrazit časová razítka na obrazovce, která se zobrazí na obrazovce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní kurzy, přihlaste se k prostředkům, které jste vytvořili. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento kurz, a odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na [stránce ONVIF – vyhovujících produktů](https://www.onvif.org/conformant-products/) .
* Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge.md). Pak postupujte podle pokynů v tématu [nasazení prvního IoT Edge modulu do](../../iot-edge/quickstart-linux.md) rychlého startu zařízení s nástrojem Virtual Linux a zaregistrujte zařízení ve službě Azure IoT Hub.