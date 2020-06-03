---
title: Kurz nahrávání videa na základě událostí do cloudu a přehrávání z cloudu – Azure
description: V tomto kurzu se naučíte používat živé analýzy videí na IoT Edge k provádění nahrávání videa na základě událostí do cloudu a přehrávání z cloudu.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 92367634a2f5785ecbb102db1e03f3d5f12d744e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300820"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Kurz: nahrávání videa založeného na událostech do cloudu a přehrávání z cloudu

V tomto kurzu se naučíte, jak pomocí živé analýzy videí v IoT Edge selektivně nahrávat části živého zdroje videí do Media Services v cloudu. Tento případ použití se v tomto kurzu označuje jako [záznam videa na základě událostí](event-based-video-recording-concept.md) (EVR). K tomuto účelu použijete model AI detekce objektů pro hledání objektů ve videu a záznam videoklipů pouze v případě, že je zjištěn určitý typ objektu. Naučíte se také, jak přehrávat zaznamenané video klipy pomocí Media Services. To je užitečné pro celou řadu scénářů, kdy je potřeba uchovat archiv videoklipů, které vás zajímají.

> [!div class="checklist"]
> * Nastavení relevantních prostředků
> * Projděte si kód, který provádí EVR
> * Spuštění ukázkového kódu
> * Prohlédněte si výsledky a podívejte se na video.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Navrhované před čtením  

Doporučujeme si přečíst následující stránky dokumentace.

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncepty Media graphu](media-graph-concept.md) 
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)
* [Kurz: vývoj modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Postup úpravy nasazení. *. template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Oddíl, [jak deklarovat trasy v manifestu nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Požadavky

Předpoklady pro tento kurz jsou následující:

* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí rozšíření [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a rozšíření [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Může se zobrazit výzva k instalaci Docker. Tuto výzvu můžete ignorovat.
* [Sada SDK .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na vašem vývojovém počítači.
* Dokončení [skriptu nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) a [nastavení prostředí](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Na konci výše uvedeného postupu budete mít k dispozici určité prostředky Azure nasazené v rámci předplatného Azure, včetně těchto:

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>Koncepty

Nahrávání videa založeného na událostech (EVR) se týká procesu nahrávání videa aktivovaného událostí. Tato událost může být vygenerována ze zpracování samotného grafického signálu (například při zjištění přesunu objektu ve videu) nebo z nezávislého zdroje (například otevření dvířek). Případně můžete spustit záznam pouze v případě, že služba Inferencing zjistí, že došlo k určité události.  V tomto kurzu použijete video o vozidlech, která se pohybují na dálnici, a zaznamenání videoklipů při zjištění nákladní části.

![Graf médií](./media/event-based-video-recording-tutorial/overview.png)

Diagram výše je obrazové znázornění [mediálního grafu](media-graph-concept.md) a dalších modulů, které doplní požadovaný scénář. Jsou zapojené čtyři IoT Edge moduly:

* Live video Analytics v modulu IoT Edge.
* Modul Edge, který spouští model AI za koncovým bodem HTTP. Tento modul AI používá model [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který umožňuje detekci mnoha typů objektů.
* Vlastní modul pro počítání a filtrování objektů (v diagramu výše, na které se říká čítač objektu), který sestavíte a nasadíte v tomto kurzu.
* [Modul simulátoru RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pro simulaci kamery RTSP.
    
    Jak znázorňuje diagram, budete v mediálním grafu používat [zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) k zachycení simulovaného živého videa (provozu na dálnici) a odeslání tohoto videa na dvě cesty.

* První cesta je uzel [procesoru filtru frekvence snímků](media-graph-concept.md#frame-rate-filter-processor) , který zapisuje snímky videa na zadané (zmenšené) snímkové rychlosti snímků. Tyto video snímky jsou odesílány do uzlu rozšíření HTTP, který pak přenáší snímky (jako obrázky) do modulu AI (YOLO V3 – což je detektor objektu) a přijímá výsledky – což budou objekty (vozidla v provozu) zjištěné modelem. Uzel rozšíření HTTP pak tyto výsledky publikuje prostřednictvím uzlu jímky zpráv IoT Hub do centra IoT Edge.
* Modul čítače objektu je nastaven tak, aby přijímal zprávy z centra IoT Edge – což zahrnuje výsledky detekce objektu (vozidla v provozu). Kontroluje, zda tyto zprávy hledají objekty určitého typu (konfigurovány prostřednictvím nastavení). Když takový objekt najde, tento modul pošle zprávu do centra IoT Edge. Zprávy o nalezených objektech se pak přesměrují do zdrojového uzlu IoT Hub v mediálním grafu. Po přijetí takového hlášení IoT Hub zdrojový uzel v mediálním grafu aktivuje uzel [procesoru brány signálu](media-graph-concept.md#signal-gate-processor) , což způsobí, že se druhý otevře v nakonfigurovaném časovém intervalu. Video prochází bránou do uzlu jímka assetu za tuto dobu. Tato část živého streamu se pak zaznamená prostřednictvím uzlu [jímky assetu](media-graph-concept.md#asset-sink) k [assetu](terminology.md#asset) ve vašem účtu Azure Media Service.

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Než začnete, ověřte, že jste v části [požadavky](#prerequisites)dokončili 3 odrážky. Po dokončení skriptu nastavení prostředků kliknutím na složené závorky vystavte strukturu složky. Zobrazí se několik souborů vytvořených v adresáři ~/clouddrive/lva-Sample.

![Nastavení aplikace](./media/quickstarts/clouddrive.png)

V tomto kurzu jsou důležité tyto:

* ~/clouddrive/lva-Sample/Edge-Deployment/.env – obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
* ~/clouddrive/lva-Sample/AppSetting.JSON-používá se Visual Studio Code pro spuštění ukázkového kódu.

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

## <a name="examine-the-template-file"></a>Kontrola souboru šablony 

V předchozím kroku jste spustili Visual Studio Code a otevřeli složku obsahující vzorový kód.

V Visual Studio Code přejděte na "src/Edge". Zobrazí se soubor. ENV, který jste vytvořili, a také několik souborů šablon nasazení. Tato šablona definuje, které hraniční moduly budete nasazovat do hraničního zařízení (virtuální počítač Azure Linux). Soubor. env obsahuje hodnoty pro proměnné, které se používají v těchto šablonách, například přihlašovací údaje služby Media Service.

Otevřete "src/Edge/Deployment. objectCounter. template. JSON". Všimněte si, že v části "moduly" jsou čtyři položky, které odpovídají položkám uvedeným výše (v části věnované koncepcím):

* lvaEdge – Toto je Live video Analytics v modulu IoT Edge.
* yolov3 – jedná se o modul AI sestavený pomocí modelu YOLO v3.
* rtspsim – simulátor RTSP
* objectCounter – jedná se o modul, který hledá konkrétní objekty ve výsledcích z yolov3

Pro modul objectCounter se podívejte na řetězec ($ {MODULes. objectCounter}), který se používá pro hodnotu Image – to je založené na [kurzu](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) vývoje IoT Edgeho modulu. Visual Studio Code automaticky rozpozná, že kód pro modul čítače objektu je pod položkou "src/Edge/modules/objectCounter". 

Přečtěte si [Tento](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) oddíl, jak deklarovat trasy v manifestu nasazení IoT Edge a pak zkontrolujte trasy v souboru JSON šablony. Všimněte si, jak:

* LVAToObjectCounter se používá k odesílání konkrétních událostí do konkrétního koncového bodu v modulu objectCounter.
* ObjectCounterToLVA se používá k odeslání události triggeru do konkrétního koncového bodu (který by měl být IoT Hub zdrojový uzel) v modulu lvaEdge.
* objectCounterToIoTHub se používá jako ladicí nástroj, který vám může při spuštění tohoto kurzu zobrazit výstup z objectCounter.

> [!NOTE]
> Zkontrolujte požadované vlastnosti modulu objectCounter, který je nastavený tak, aby hledal objekty, které jsou označené jako "nákladní", s úrovní spolehlivosti minimálně 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge 

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení, a nastavení konfigurace pro tyto moduly. Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

Pomocí Visual Studio Code postupujte podle [těchto](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) pokynů pro přihlášení do Docker a "Build and push IoT Edge řešení", ale pro tento krok použijte src/Edge/Deployment. objectCounter. template. JSON.

![Sestavování a nabízených IoT Edge řešení](./media/event-based-video-recording-tutorial/build-push.png)

Tím se sestaví modul objectCounter pro počítání objektů a nahrajte image do vašeho Azure Container Registry (ACR).

* Ověřte, zda máte proměnné prostředí CONTAINER_REGISTRY_USERNAME_myacr a CONTAINER_REGISTRY_PASSWORD_myacr definovány v souboru. env.

Výše uvedený krok vytvoří manifest nasazení IoT Edge v souboru src/Edge/config/Deployment. objectCounter. amd64. JSON. Klikněte na soubor pravým tlačítkem a klikněte na vytvořit nasazení pro jedno zařízení.

![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)

Pokud se jedná o první kurz s živým analýzou videa na IoT Edge, Visual Studio Code vás vyzve k zadání připojovacího řetězce IoTHub. Můžete ho zkopírovat ze souboru appSettings. JSON.

V dalším kroku Visual Studio Code požádat o výběr zařízení služby IoT Hub. Vyberte zařízení IoT Edge (mělo by se jednat o "lva-Sample-Device").

V této fázi se spustilo nasazení hraničních modulů do zařízení IoT Edge.
V přibližně 30 sekundách aktualizujte IoT Hub Azure v levém dolním rohu Visual Studio Code a měli byste vidět, že jsou nasazené 4 moduly (Poznamenejte si znovu tyto názvy: lvaEdge, rtspsim, yolov3 a objectCounter).

![4 nasazené moduly](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Pokud chcete zobrazit události z modulu čítače objektů a ze služby Live video Analytics v modulu IoT Edge, postupujte takto:

1. Otevřete podokno Průzkumník v Visual Studio Code a vyhledejte Azure IoT Hub v levém dolním rohu.
1. Rozbalte uzel zařízení.
1. Pravým tlačítkem clink na lva-Sample-Device a zvolte možnost **Spustit sledování integrovaného monitorování událostí**.

![Spustit sledování vestavěného koncového bodu události](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Spuštění programu

1. Visual Studio Code přejděte do části src/Cloud-to-Device-Console-App/Operations. JSON.

1. Pod uzlem GraphTopologySet upravte následující položky:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Potom v části uzly GraphInstanceSet a GraphTopologyDelete upravte,

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
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
    
     * Volání GraphInstanceActivate ke spuštění instance grafu a spuštění toku videa
     * Druhé volání GraphInstanceList k zobrazení, že instance grafu je ve stavu spuštěno.
     
1. Výstup v okně terminálu se teď pozastaví na příkazovém řádku pro pokračování stisknutím klávesy ENTER. V tuto chvíli nespustí "Enter". Můžete se posunout nahoru a podívat se na datové části odezvy JSON pro přímé metody, které jste vyvolali.

1. Pokud teď přepnete do okna výstup v Visual Studio Code, zobrazí se zprávy, které se odesílají do IoT Hubu, a to pomocí nástroje Live video Analytics v modulu IoT Edge.

     * Tyto zprávy jsou popsány v následující části.
     
1. Instance grafu se dál spustí a zaznamená video – simulátor RTSP bude pokračovat ve vytváření smyček zdrojového videa. Zkontrolujte zprávy popsané v níže uvedené části a potom zastavte instanci, vraťte se do okna terminálu a stiskněte klávesu ENTER. Pro vyčištění prostředků se provedla další řada volání:

     * Volání GraphInstanceDeactivate k deaktivaci instance grafu
     * Volání GraphInstanceDelete pro odstranění instance
     * Volání GraphTopologyDelete k odstranění topologie
     * Konečné volání GraphTopologyList k zobrazení, že seznam je nyní prázdný

## <a name="interpret-the-results"></a>Interpretace výsledků 

Když spustíte graf Media Graph, Live video Analytics v modulu IoT Edge odešle určité diagnostické a provozní události do centra IoT Edge. Tyto události jsou zprávy, které se zobrazí v okně výstup Visual Studio Code, který obsahuje oddíl "tělo" a "applicationProperties". Informace o tom, co tyto oddíly představuje, najdete v tématu [Vytvoření a čtení zpráv IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Ve zprávách níže jsou vlastnosti aplikace a obsah těla definovány modulem Live video Analytics.

## <a name="diagnostic-events"></a>Diagnostické události

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished 

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu na kontejneru simulátoru RTSP. V případě úspěchu se tato událost vytiskne. Všimněte si, že typ události je Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

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


* Zpráva je diagnostická událost MediaSessionEstablished, která indikuje, že uzel zdroje RTSP (předmět) byl schopný navázat spojení s simulátorem RTSP a zahájit příjem (simulovaného) živého kanálu.

* "Subject" v applicationProperties odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.

* eventType v applicationProperties značí, že se jedná o diagnostickou událost.

* "čas události" označuje čas, kdy došlo k události, což je čas, kdy se spustilo video přenos dat (soubor MKV) do modulu jako živý datový proud.

* "tělo" obsahuje data o diagnostické události, což je v tomto případě podrobnosti o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="operational-events"></a>Provozní události

Jakmile se mediální graf spustí po nějakou dobu, nakonec získáte událost z modulu objektu Counter. 

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

ApplicationProperties obsahuje čas události, což je čas, kdy modul počítadla objektu zjistil, že výsledky z modulu YOLO V3 obsahují objekty Interest (nákladní automobily).

Můžou se zobrazit další informace o těchto událostech, když se ve videu zjistí další nákladní automobily.

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Skoro hned po čítači objektu odešle událost, zobrazí se událost typu Microsoft. Media. Graph. Operational. RecordingStarted

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

"Subject" v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu. Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video. Tuto hodnotu byste si měli poznamenat.

### <a name="recordingavailable-event"></a>Událost RecordingAvailable

Když uzel jímka assetu nahrál video do assetu, vygeneruje tuto událost typu Microsoft. Media. Graph. Operational. RecordingAvailable

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

Tato událost označuje, že do Assetu byly zapsány dostatečné údaje, aby si hráči/klienti mohli zahájit přehrávání videa. "Subject" v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu. Tělo obsahuje informace o umístění výstupu, což je v tomto případě název Assetu služby Azure Media Service, do kterého se nahraje video.

### <a name="recordingstopped-event"></a>Událost RecordingStopped

Pokud prohlížíte nastavení aktivace (maximumActivationTime) pro uzel procesoru brány signálu v [topologii](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), uvidíte, že je tato brána nastavená tak, aby se zavřela po 30 sekundách od odeslání videa. Přibližně 30 sekund po události RecordingStarted by se měla zobrazit událost typu Microsoft. Media. Graph. Operation. RecordingStopped, která indikuje, že uzel jímky assetu zastavil záznam videa do assetu.

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

Tato událost označuje, že záznam byl zastaven. "Subject" v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu. Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video.

## <a name="media-services-asset"></a>Prostředek Media Services  

Můžete prozkoumávat Media Services Asset, který byl vytvořen grafem, přihlášením k Azure Portal a zobrazením videa.

1. Otevřete webový prohlížeč a přejdete na [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.
1. Vyhledejte účet Media Services mezi prostředky, které máte ve svém předplatném, a otevřete okno účtu.
1. V seznamu Media Services klikněte na Assety.

    ![Prostředky](./media/continuous-video-recording-tutorial/assets.png)
1. Zobrazí se Asset s názvem sampleAssetFromEVR-LVAEdge-{DateTime} – jedná se o název, který byl zadán ve vlastnosti outputLocation události RecordingStarted. AssetNamePattern v topologii určuje, jak se tento název vygeneroval.
1. Klikněte na prostředek.
1. Na stránce Podrobnosti o aktivech klikněte na odkaz **vytvořit nový** pod textovým polem Adresa URL streamování.

    ![Nový prostředek](./media/continuous-video-recording-tutorial/new-asset.png)

1. V průvodci, který se otevře, přijměte výchozí možnosti a stiskněte tlačítko Přidat. Další informace najdete v tématu [přehrávání videa](video-playback-concept.md).

    > [!TIP]
    > Ujistěte se, že [koncový bod streamování běží](../latest/streaming-endpoint-concept.md).
1. Přehrávač by měl načíst video a měli byste být schopni **spustit přehrávání** a zobrazit ho.

> [!NOTE]
> Vzhledem k tomu, že zdroj videa byl kontejner simulující kanál kamery, časová razítka ve videu souvisejí s tím, že jste aktivovali instanci grafu, a když ji deaktivujete. Pokud používáte ovládací prvky přehrávání integrované v kurzu [nahrávání více dní](playback-multi-day-recordings-tutorial.md) , můžete zobrazit časová razítka na obrazovce zobrazená na obrazovce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní kurzy, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento kurz, a odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na [stránce ONVIF – vyhovujících produktů](https://www.onvif.org/conformant-products/) .
* Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) a potom postupujte podle pokynů v části [nasazení prvního IoT Edge modulu do](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rychlého startu zařízení s nástrojem Virtual Linux a zaregistrovat zařízení ve službě Azure IoT Hub.
