---
title: Kurz nepřetržitého nahrávání videa do cloudu a přehrávání z cloudového kurzu – Azure
description: V tomto kurzu se naučíte používat Azure Live video Analytics na Azure IoT Edge k nepřetržitému nahrávání videa do cloudu a streamování libovolné části tohoto videa pomocí Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 2dde1c9d917881d7a16dbc853e4af416dffe5d7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702415"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Kurz: nepřetržité nahrávání videa do cloudu a přehrávání z cloudu

V tomto kurzu se naučíte používat Azure Live video Analytics na Azure IoT Edge k provádění [nepřetržitého nahrávání videa](continuous-video-recording-concept.md) (CVR) do cloudu a streamování libovolné části tohoto videa pomocí Azure Media Services. Tato možnost je užitečná ve scénářích, jako je zabezpečení a dodržování předpisů, kdy je nutné uchovávat archivy záběrů z kamery po dobu dnů nebo týdnů. 

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> * Nastavte příslušné prostředky.
> * Projděte si kód, který provádí CVR.
> * Spusťte vzorový kód.
> * Prohlédněte si výsledky a podívejte se na video.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Navrhované před čtením  

Než začnete, přečtěte si tyto články:

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncepty Media graphu](media-graph-concept.md) 
* [Scénáře nepřetržitého nahrávání videí](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Požadavky

Předpoklady pro tento kurz:

* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a rozšíření [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Může se zobrazit výzva k instalaci Docker. Ignorovat tuto výzvu.
* [Sada SDK .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na vašem vývojovém počítači.
* Dokončete [skript nastavení prostředků živé analýzy videí](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

Na konci tohoto postupu budete mít v předplatném Azure nasazené relevantní prostředky Azure:

* Azure IoT Hub
* Účet služby Azure Storage
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](../../iot-edge/how-to-install-iot-edge.md)

> [!TIP]
> Pokud narazíte na problémy s prostředky Azure, které se vytvoří, přečtěte si náš **[Průvodce odstraňováním potíží](troubleshoot-how-to.md#common-error-resolutions)** a vyřešte některé běžně zjištěné problémy.

## <a name="concepts"></a>Koncepty

Jak je vysvětleno v článku [konceptu Media graphu](media-graph-concept.md) , vám mediální graf umožňuje definovat:

- Kde by se mělo zachytit médium.
- Jak by měl být zpracován.
- Kam mají být doručeny výsledky. 
 
 K provedení CVR je potřeba zachytit video z kamery podporující RTSP a nepřetržitě ji zaznamenat do [prostředku Azure Media Services](terminology.md#asset). Tento diagram znázorňuje grafické znázornění tohoto mediálního grafu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Graf médií":::

V tomto kurzu použijete jeden hraniční modul sestavený pomocí [serveru Live555 Media](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) k simulaci kamery RTSP. V rámci Media graphu použijete zdrojový uzel [RTSP](media-graph-concept.md#rtsp-source) k získání živého informačního kanálu a odešlete toto video do [uzlu jímka assetu](media-graph-concept.md#asset-sink), který zaznamenává video do assetu. Video, které se použije v tomto kurzu, je [ukázkové video s mezioddílem na dálnici](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

Než začnete, ověřte, že jste dokončili třetí odrážku v části [požadavky](#prerequisites). Po dokončení skriptu pro nastavení prostředků vyberte složené závorky, které budou vystavovat strukturu složek. Zobrazí se několik souborů vytvořených v adresáři ~/clouddrive/lva-Sample.

![Nastavení aplikace](./media/quickstarts/clouddrive.png)

Z zájmu tohoto kurzu jsou soubory:

* **~/clouddrive/lva-Sample/Edge-Deployment/.env**: obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
* **~/clouddrive/lva-sample/appsettings.json**: používá Visual Studio Code ke spuštění ukázkového kódu.

Budete potřebovat soubory pro tyto kroky:

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
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Kontrola ukázkových souborů

V Visual Studio Code otevřete src/Edge/deployment.template.jsna. Tato šablona definuje, které moduly Edge nasadíte do hraničního zařízení (virtuální počítač Azure Linux). V části **modulů** jsou dvě položky s následujícími názvy:

* **lvaEdge**: Toto je Live video Analytics v modulu IoT Edge.
* **rtspsim**: Toto je simulátor RTSP.

Pak přejděte do složky src/Cloud-to-Device-Console-App Tady se zobrazí appsettings.jsv souboru, který jste vytvořili společně s několika dalšími soubory:

* **C2D-Console-App. csproj**: soubor projektu pro Visual Studio Code.
* **operations.js**: Tento soubor obsahuje seznam různých operací, které byste spustili.
* **Program. cs**: ukázkový kód programu, který:
    * Načte nastavení aplikace.
    * Vyvolá přímé metody vystavené živým analýzou videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md).
    * Pozastaví, abyste prozkoumali výstup programu v okně **terminálu** a události generované modulem v okně **výstup** .
    * Vyvolá přímé metody pro vyčištění prostředků.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge 

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení a nastavení konfigurace pro tyto moduly. Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

1. Spuštění nástroje Visual Studio Code
1. Nastavte připojovací řetězec IoT Hub tak, že v levém dolním rohu vyberete ikonu **Další akce** vedle PODOKNA **Azure IoT Hub** . Zkopírujte řetězec z src/Cloud-to-Device-Console-App/appsettings.jsv souboru. 

    ![Nastavit připojovací řetězec IoT Hub](./media/quickstarts/set-iotconnection-string.png)
    > [!NOTE]
    > Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Klikněte pravým tlačítkem myši na soubor src/Edge/deployment.template.jsv souboru a vyberte možnost **Generovat Manifest nasazení IoT Edge**. Visual Studio Code používá hodnoty ze souboru. env k nahrazení proměnných nalezených v souboru šablony nasazení. Tato akce vytvoří soubor manifestu ve složce src/Edge/config s názvem **deployment.amd64.jsv**.

   ![Generovat manifest nasazení IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Klikněte pravým tlačítkem na soubor src/Edge/config/deployment.amd64.jsa vyberte **vytvořit nasazení pro jedno zařízení**.

   ![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)
1. Pak budete požádáni o **Výběr zařízení IoT Hub**. V rozevíracím seznamu vyberte lva-Sample-Device.
1. V přibližně 30 sekundách aktualizujte Azure IoT Hub v části vlevo dole. Mělo by se zobrazit hraniční zařízení s nasazenými následujícími moduly:
    * Live video Analytics na IoT Edge (název modulu **lvaEdge**)
    * Simulátor RTSP (název modulu **rtspsim**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Příprava na monitorování modulů 

Při použití nástroje Live video Analytics v modulu IoT Edge k nahrání streamu živého videa posílá události do IoT Hub. Chcete-li zobrazit tyto události, postupujte podle následujících kroků:

1. Otevřete podokno Průzkumník v Visual Studio Code a hledejte **Azure IoT Hub** v levém dolním rohu.
1. Rozbalte uzel **zařízení** .
1. Klikněte pravým tlačítkem myši na soubor lva-Sample-Device a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

    ![Spustit sledování vestavěného koncového bodu události](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="run-the-program"></a>Spuštění programu 

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::
1. V systému použijte src/Cloud-to-Device-Console-App/operations.js.
1. V uzlu **GraphTopologySet** upravte následující položky:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. V dalším kroku v uzlech **GraphInstanceSet** a **GraphTopologyDelete** zajistěte, aby hodnota vlastnosti **topologického** odpovídala hodnotě vlastnosti **Name** v předchozí topologii grafu:

    `"topologyName" : "CVRToAMSAsset"`  
1. Otevřete [topologii](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) v prohlížeči a podívejte se na assetNamePattern. Abyste se ujistili, že máte Asset s jedinečným názvem, můžete změnit název instance grafu v operations.jssouboru (z výchozí hodnoty Sample-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Spusťte ladicí relaci výběrem F5. V okně **terminálu** se zobrazí zprávy, které se vytisknou.
1. operations.jsv souboru začíná s voláními GraphTopologyList a GraphInstanceList. Pokud jste vyčistili prostředky po předchozích rychlých startech nebo kurzech, tato akce vrátí prázdné seznamy a potom se pozastaví, abyste vybrali **ENTER**, jak je znázorněno níže:

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

1. Po výběru **ENTER** v okně **terminálu** se provede další sada přímých volání metody:
   * Volání GraphTopologySet pomocí předchozího topologyUrlu
   * Volání GraphInstanceSet pomocí následujícího textu
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
1. Výstup v okně **terminálu** se nyní pozastaví **stisknutím klávesy ENTER a zobrazí se výzva k pokračování** . V tuto chvíli nevybírejte **ENTER** . Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Pokud teď přepnete do okna **výstup** v Visual Studio Code, zobrazí se zprávy odesílané do IoT Hub pomocí nástroje Live video Analytics v modulu IoT Edge.

   Tyto zprávy jsou popsány v následující části.
1. Instance grafu pokračuje v běhu a zaznamená video. Simulátor RTSP zachovává smyčku zdrojového videa. Nahrávání zabráníte tak, že přejdete zpátky do okna **terminálu** a vyberete **ENTER**. K vyčištění prostředků se provede další řada volání pomocí:

   * Volání GraphInstanceDeactivate k deaktivaci instance grafu.
   * Volání GraphInstanceDelete pro odstranění instance.
   * Volání GraphTopologyDelete k odstranění topologie.
   * Konečné volání GraphTopologyList k zobrazení, že seznam je nyní prázdný.

## <a name="interpret-the-results"></a>Interpretace výsledků 

Když spustíte graf Media Graph, Live video Analytics v modulu IoT Edge odešle určité diagnostické a provozní události do centra IoT Edge. Tyto události jsou zprávy, které se zobrazí v okně **výstup** Visual Studio Code. Obsahují oddíl tělo a oddíl applicationProperties. Informace o tom, co tyto oddíly představuje, najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách jsou vlastnosti aplikace a obsah těla definovány modulem Live video Analytics.

## <a name="diagnostics-events"></a>Diagnostické události 

### <a name="mediasession-established-event"></a>MediaSession vytvořená událost

Po aktivaci instance grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu v modulu rtspsim. V případě úspěchu se zobrazí tato událost:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Zpráva je událost diagnostiky (MediaSessionEstablished). Indikuje, že zdrojový uzel RTSP (předmět) navázal spojení s simulátorem RTSP a začal přijímat (simulované) živé kanály.
* Oddíl Subject v applicationProperties odkazuje na uzel v topologii grafu, ze kterého byla zpráva vygenerována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* Oddíl eventType v applicationProperties označuje, že se jedná o diagnostickou událost.
* Část čas události označuje čas, kdy došlo k události.
* Oddíl Text obsahuje data o diagnostické události, která je v tomto případě podrobnostmi o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

## <a name="operational-events"></a>Provozní události 

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Když uzel jímky assetu začne nahrávat video, vygeneruje tuto událost typu **Microsoft. Media. Graph. Operational. RecordingStarted**:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Oddíl Subject v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu.

Oddíl Text obsahuje informace o umístění výstupu. V tomto případě se jedná o název prostředku Azure Media Services, do kterého se nahraje video. Tuto hodnotu si poznamenejte.

### <a name="recordingavailable-event"></a>Událost RecordingAvailable

Jak název navrhuje, událost RecordingStarted se pošle při spuštění nahrávání, ale data videa se ještě nemusela odeslat do assetu. Když uzel jímka assetu nahrál video data do assetu, vygeneruje tuto událost typu **Microsoft. Media. Graph. Operational. RecordingAvailable**:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Tato událost označuje, že do assetu se zapsala dostatečná data pro aktéry nebo klienty, aby si mohli začít přehrávat video.

Oddíl Subject v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu.

Oddíl Text obsahuje informace o umístění výstupu. V tomto případě se jedná o název prostředku Azure Media Services, do kterého se nahraje video.

### <a name="recordingstopped-event"></a>Událost RecordingStopped

Po deaktivaci instance grafu zastaví uzel jímka assetu nahrávání videa do assetu. Vygeneruje tuto událost typu **Microsoft. Media. Graph. Operational. RecordingStopped**:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Tato událost označuje, že záznam byl zastaven.

Oddíl Subject v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu.

Oddíl Text obsahuje informace o umístění výstupu, což je v tomto případě název Azure Media Services assetu, do kterého se nahraje video.

## <a name="media-services-asset"></a>Prostředek Media Services  

Můžete prozkoumávat Media Services Asset, který byl vytvořen pomocí Media graphu, a to přihlášením k Azure Portal a zobrazením videa.

1. Otevřete webový prohlížeč a přejdete na [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.
1. Vyhledejte účet Media Services mezi prostředky, které máte ve svém předplatném, a otevřete podokno účet.
1. V seznamu **Media Services** vyberte **assety** .

    ![Prostředky Media Services](./media/continuous-video-recording-tutorial/assets.png)
1. Najdete tu materiály s názvem sampleAsset-CVRToAMSAsset-Sample-Graph-1. Toto je vzor názvů, který jste zvolili v souboru topologie grafu.
1. Vyberte asset.
1. Na stránce Podrobnosti o aktivech vyberte **vytvořit novou** v poli **Adresa URL streamování** .

    ![Nový prostředek](./media/continuous-video-recording-tutorial/new-asset.png)

1. V průvodci, který se otevře, přijměte výchozí možnosti a vyberte **Přidat**. Další informace najdete v tématu [přehrávání videa](video-playback-concept.md).

    > [!TIP]
    > Ujistěte se, že [koncový bod streamování běží](../latest/streaming-endpoint-concept.md).
1. Přehrávač by měl načíst video. Vyberte **Přehrát** a zobrazte ho.

> [!NOTE]
> Vzhledem k tomu, že zdroj videa byl kontejner simulující kanál kamery, časová razítka ve videu souvisí s tím, že jste aktivovali instanci grafu a když ji deaktivujete. Pokud chcete zjistit, jak procházet multiday záznam a zobrazit části tohoto archivu, přečtěte si kurz [přehrávání více dní](playback-multi-day-recordings-tutorial.md) . V tomto kurzu vidíte také časová razítka zobrazená na obrazovce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní kurzy, přihlaste se k prostředkům, které jste vytvořili. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento kurz, a odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na [stránce ONVIF – vyhovujících produktů](https://www.onvif.org/conformant-products/) .
* Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge.md). Pak postupujte podle pokynů v tématu [nasazení prvního IoT Edge modulu do](../../iot-edge/quickstart-linux.md) rychlého startu zařízení s nástrojem Virtual Linux a zaregistrujte zařízení ve službě Azure IoT Hub.