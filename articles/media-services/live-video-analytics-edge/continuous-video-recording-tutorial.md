---
title: Kurz nepřetržitého nahrávání videa do cloudu a přehrávání z cloudu – Azure
description: V tomto kurzu se naučíte používat živé analýzy videí na IoT Edge k nepřetržitému nahrávání videa do cloudu a streamování všech částí tohoto videa pomocí Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261856"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Kurz: nepřetržité nahrávání videa do cloudu a přehrávání z cloudu  

V tomto kurzu se naučíte používat živé video analýzy v IoT Edge k provádění [nepřetržitého nahrávání videa](continuous-video-recording-concept.md) (CVR) do cloudu a k streamování všech částí tohoto videa pomocí Media Services. To je užitečné pro scénáře, jako jsou zabezpečení, dodržování předpisů a další, kde je potřeba udržovat archivy záběrů z kamery po dobu více dní (nebo týdnů).

> [!div class="checklist"]
> * Nastavení relevantních prostředků
> * Projděte si kód, který provádí CVR
> * Spuštění ukázkového kódu
> * Prohlédněte si výsledky a podívejte se na video.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Navrhované před čtením  

Doporučujeme si přečíst následující stránky dokumentace.

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncepty Media graphu](media-graph-concept.md) 
* [Scénáře nepřetržitého nahrávání videí](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Požadavky

Předpoklady pro tento kurz jsou následující:

* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí rozšíření [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a rozšíření [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Může se zobrazit výzva k instalaci Docker. Tuto výzvu můžete ignorovat.
* [Sada SDK .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na vašem vývojovém počítači.
* Dokončení [skriptu nastavení prostředků živé analýzy videí](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

Na konci výše uvedeného postupu budete mít k dispozici určité prostředky Azure nasazené v rámci předplatného Azure, včetně těchto:

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>Koncepty

Jak je vysvětleno [zde](media-graph-concept.md), vám mediální graf umožňuje definovat, kde by se měla zachytit média, jak by měla být zpracována a kde by měly být doručeny výsledky. K provedení CVR je potřeba zachytit video z kamery podporující RTSP a průběžně je zaznamenat do [prostředku Azure Media Services](terminology.md#asset). Následující diagram znázorňuje grafické znázornění tohoto mediálního grafu.

![Media Graph](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

V tomto kurzu použijete jeden hraniční modul sestavený pomocí [serveru Live555 Media](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) k simulaci kamery RTSP. V mediálním grafu použijete k získání živého kanálu [zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) a odešlete toto video do [uzlu jímky assetu](media-graph-concept.md#asset-sink) , který bude nahrávat video do assetu.

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Než začnete, ověřte, že jste v části [požadavky](#prerequisites)dokončili 3 odrážky. Po dokončení skriptu nastavení prostředků kliknutím na složené závorky vystavte strukturu složky. Zobrazí se několik souborů vytvořených v adresáři ~/clouddrive/lva-Sample.

![Nastavení aplikace](./media/quickstarts/clouddrive.png)

V tomto kurzu jsou důležité tyto:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

Tyto soubory budete potřebovat pro následující kroky.

1. Naklonujte úložiště sem https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Spusťte Visual Studio Code a otevřete složku, do které jste stáhli úložiště.
1. V Visual Studio Code přejděte do složky src/Cloud-to-Device-Console-App a vytvořte soubor s názvem appSettings. JSON. Tento soubor bude obsahovat nastavení potřebná ke spuštění programu.
1. Zkopírujte obsah ze souboru ~/clouddrive/lva-Sample/appSettings.JSON. Text by měl vypadat takto:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    Připojovací řetězec IoT Hub umožňuje používat Visual Studio Code k posílání příkazů do hraničních modulů přes Azure IoT Hub.
    
1. Potom přejděte do složky "src/Edge" a vytvořte soubor s názvem ". env".
1. Zkopírujte obsah ze souboru ~/clouddrive/lva-Sample/.env. Text by měl vypadat takto:

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

## <a name="examine-the-sample-files"></a>Kontrola ukázkových souborů

V Visual Studio Code otevřete "src/Edge/Deployment. template. JSON". Tato šablona definuje, které hraniční moduly budete nasazovat do hraničního zařízení (virtuální počítač Azure Linux). Všimněte si, že v části "moduly" jsou dvě položky s následujícími názvy:

* lvaEdge – Toto je Live video Analytics v modulu IoT Edge.
* rtspsim – simulátor RTSP

Pak přejděte do složky src/Cloud-to-Device-Console-App. Tady se zobrazí soubor appSettings. JSON, který jste vytvořili spolu s několika dalšími soubory:

* C2D-Console-App. csproj – soubor projektu pro Visual Studio Code.
* Operations. JSON – tento soubor obsahuje seznam různých operací, které byste spustili.
* Program.cs – vzorový programový kód, který provede následující:
    * Načte nastavení aplikace.
    * Vyvolá přímé metody vystavené živým analýzou videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md) .
    * Pozastaví se, abyste prozkoumali výstup programu v okně terminálu a události generované modulem v okně výstup.
    * Vyvolá přímé metody pro vyčištění prostředků.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge 

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení, a nastavení konfigurace pro tyto moduly. Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

1. Spusťte Visual Studio Code
1. Kliknutím na ikonu Další akce vedle podokna AZURE IOT HUB v levém dolním rohu nastavte připojovací řetězec IoTHub. Můžete zkopírovat řetězec ze souboru src/Cloud-to-Device-Console-App/appSettings. JSON. 

    ![Nastavení připojovacího řetězce IOT](./media/quickstarts/set-iotconnection-string.png)
1. Potom klikněte pravým tlačítkem na soubor src/Edge/Deployment. template. JSON a klikněte na vygenerovat IoT Edge manifest nasazení. Visual Studio Code používá hodnoty ze souboru. ENV, aby bylo možné nahradit proměnné nalezené v souboru šablony nasazení. To by mělo vytvořit soubor manifestu ve složce src/Edge/config s názvem "Deployment. amd64. JSON".

   ![Generovat manifest nasazení IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Klikněte pravým tlačítkem na "src/Edge/config/Deployment. amd64. JSON" a klikněte na "vytvořit nasazení pro jedno zařízení".

   ![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)
1. Zobrazí se výzva k výběru zařízení IoT Hub. V rozevíracím seznamu vyberte lva-Sample-Device.
1. V přibližně 30 sekundách aktualizujte IoT Hub Azure v levém dolním rohu a měli byste vidět, že hraniční zařízení má nasazené následující moduly:
    * Live video Analytics na IoT Edge (název modulu "lvaEdge")
    * Simulátor RTSP (název modulu "rtspsim")
 
    ![Centrum IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Příprava na monitorování modulů 

Když použijete Live video Analytics v modulu IoT Edge k záznamu živého streamu videa, pošle se události do IoT Hub. Chcete-li zobrazit tyto události, postupujte podle následujících kroků:

1. Otevřete podokno Průzkumník v Visual Studio Code a vyhledejte Azure IoT Hub v levém dolním rohu.
1. Rozbalte uzel zařízení.
1. Klikněte pravým tlačítkem na lva-Sample-Device a zvolte možnost spustit monitorování integrovaného monitorování událostí.

    ![Spustit sledování vestavěného koncového bodu události](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Spuštění programu 

1. Visual Studio Code přejděte do části src/Cloud-to-Device-Console-App/Operations. JSON.
1. Pod uzlem GraphTopologySet upravte následující položky:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. V dalším kroku se v uzlech GraphInstanceSet a GraphTopologyDelete ujistěte, že hodnota vlastnosti Topology odpovídá hodnotě vlastnosti Name ve výše uvedené topologii grafu:

    `"topologyName" : "CVRToAMSAsset"`  
1. Otevřete [topologii](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) v prohlížeči a podívejte se na assetNamePattern. Abyste se ujistili, že máte Asset s jedinečným názvem, možná budete chtít změnit název instance grafu v souboru Operations. JSON (z výchozí hodnoty "Sample-Graph-1").

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Spusťte ladicí relaci (stiskněte klávesu F5). Začnete zobrazovat některé zprávy, které se vytisknou v okně terminálu.
1. Operace Operations. JSON se spustí s voláními GraphTopologyList a GraphInstanceList. Pokud jste vyčistili prostředky po předchozích rychlých startech nebo kurzech, vrátí tato akce prázdné seznamy a potom se pozastaví, abyste mohli stisknout Enter, například níže:

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
     * Volání GraphTopologySet pomocí topologyUrl výše.
     * Volání GraphInstanceSet s použitím následujícího textu.
     
     ```
     {
       "@apiVersion": "1.0",
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
1. Výstup v okně terminálu se teď pozastaví na příkazovém řádku pro pokračování stisknutím klávesy ENTER. V tuto chvíli nespustí "Enter". Můžete se posunout nahoru a podívat se na datové části odezvy JSON pro přímé metody, které jste vyvolali.
1. Pokud teď přepnete do okna výstup v Visual Studio Code, zobrazí se zprávy, které se odesílají do IoT Hubu, a to pomocí nástroje Live video Analytics v modulu IoT Edge.

     * Tyto zprávy jsou popsány v následující části.
1. Instance grafu bude i nadále běžet a zaznamená video – simulátor RTSP bude pokračovat ve vytváření smyček ve zdrojovém videu. Chcete-li zastavit nahrávání, vraťte se do okna terminálu a stiskněte klávesu ENTER. Pro vyčištění prostředků se provedla další řada volání:

     * Volání GraphInstanceDeactivate k deaktivaci instance grafu
     * Volání GraphInstanceDelete pro odstranění instance
     * Volání GraphTopologyDelete k odstranění topologie
     * Konečné volání GraphTopologyList k zobrazení, že seznam je nyní prázdný

## <a name="interpret-the-results"></a>Interpretace výsledků 

Když spustíte graf Media Graph, Live video Analytics v modulu IoT Edge odešle určité diagnostické a provozní události do centra IoT Edge. Tyto události jsou zprávy, které se zobrazí v okně výstup Visual Studio Code, který obsahuje oddíl "tělo" a "applicationProperties". Informace o tom, co tyto oddíly představuje, najdete v [tomto](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) článku.

Ve zprávách níže jsou vlastnosti aplikace a obsah těla definovány modulem Live video Analytics.

## <a name="diagnostic-events"></a>Diagnostické události 

### <a name="mediasession-established-event"></a>MediaSession vytvořená událost

Po aktivaci instance grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu v modulu rtspsim. V případě úspěchu se tato událost vytiskne:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
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

* Zpráva je diagnostická událost MediaSessionEstablished, která indikuje, že uzel zdroje RTSP (předmět) byl schopný navázat spojení s simulátorem RTSP a zahájit příjem (simulovaného) živého kanálu.
* "Subject" v applicationProperties odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o diagnostické události, což je v tomto případě podrobnosti o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

## <a name="operational-events"></a>Provozní události 

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Když uzel jímky assetu začne nahrávat video, vygeneruje tuto událost typu Microsoft. Media. Graph. Operational. RecordingStarted

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

"Subject" v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu.

Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video. Tuto hodnotu byste si měli poznamenat.

### <a name="recordingavailable-event"></a>Událost RecordingAvailable

Jak název navrhuje, událost RecordingStarted se pošle při spuštění nahrávání, ale data videa se ještě nemusela odeslat do Assetu. Když uzel jímka assetu nahrál video data do assetu, vygeneruje tuto událost typu Microsoft. Media. Graph. Operational. RecordingAvailable

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Tato událost označuje, že do Assetu byly zapsány dostatečné údaje, aby si hráči/klienti mohli zahájit přehrávání videa.

"Subject" v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu.

Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video.

### <a name="recordingstopped-event"></a>Událost RecordingStopped

Když deaktivujete instanci grafu, uzel jímky assetu zastaví nahrávání videa do assetu, vygeneruje tuto událost typu Microsoft. Media. Graph. Operational. RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Tato událost označuje, že záznam byl zastaven.

"Subject" v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu.

Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video.

## <a name="media-services-asset"></a>Prostředek Media Services  

Můžete prozkoumávat Media Services Asset, který byl vytvořen pomocí Media graphu, a to přihlášením k Azure Portal a zobrazením videa.

1. Otevřete webový prohlížeč a přejdete na [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.
1. Vyhledejte účet Media Services mezi prostředky, které máte ve svém předplatném, a otevřete okno účtu.
1. V seznamu Media Services klikněte na prostředky.

    ![Prostředky](./media/continuous-video-recording-tutorial/assets.png)
1. Najdete zde seznam prostředků s názvem sampleAsset-CVRToAMSAsset-Sample-Graph-1 – jedná se o vzor názvů zvolený v souboru topologie grafu.
1. Klikněte na prostředek.
1. Na stránce Podrobnosti o aktivech klikněte na odkaz **vytvořit nový** pod textovým polem Adresa URL streamování.

    ![Nový prostředek](./media/continuous-video-recording-tutorial/new-asset.png)

1. V průvodci, který se otevře, přijměte výchozí možnosti a stiskněte tlačítko Přidat. Další informace najdete v tématu [přehrávání videa](video-playback-concept.md).

    > [!TIP]
    > Ujistěte se, že [koncový bod streamování běží](../latest/streaming-endpoint-concept.md).
1. Přehrávač by měl načíst video a měli byste být schopni **Spustit Play**> * *, aby se zobrazila.

> [!NOTE]
> Vzhledem k tomu, že zdroj videa byl kontejner simulující kanál kamery, časová razítka ve videu souvisejí s tím, že jste aktivovali instanci grafu, a když ji deaktivujete. V [tomto](playback-multi-day-recordings-tutorial.md) kurzu se můžete podívat, jak procházet více dní a zobrazovat části tohoto archivu. V tomto kurzu uvidíte také časová razítka na obrazovce zobrazená na obrazovce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní kurzy, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento kurz, a odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na [stránce ONVIF – vyhovujících produktů](https://www.onvif.org/conformant-products/) .
* Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) a potom postupujte podle pokynů v části [nasazení prvního IoT Edge modulu do](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rychlého startu zařízení s nástrojem Virtual Linux a zaregistrovat zařízení ve službě Azure IoT Hub.
