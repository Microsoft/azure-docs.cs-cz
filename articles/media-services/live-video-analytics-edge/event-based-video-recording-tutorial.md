---
title: Kurz nahrávání videa na základě událostí do cloudu a přehrávání z cloudu – Azure
description: V tomto kurzu se naučíte používat živé analýzy videí na IoT Edge k provádění nahrávání videa na základě událostí do cloudu a přehrávání z cloudu.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: daab1f06d8950aa7710c7e808ea6362ee3bfd626
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261928"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Kurz: nahrávání videa založeného na událostech do cloudu a přehrávání z cloudu

V tomto kurzu se naučíte, jak pomocí živé analýzy videí v IoT Edge selektivně nahrávat části živého zdroje videí do Media Services v cloudu. Tento případ použití se v tomto kurzu označuje jako [záznam videa na základě událostí](event-based-video-recording-concept.md) (EVR). K tomuto účelu použijete model AI detekce objektů pro hledání objektů ve videu a záznam videoklipů pouze v případě, že je zjištěn určitý typ objektu. Naučíte se také, jak přehrávat zaznamenané video klipy pomocí Media Services. To je užitečné pro celou řadu scénářů, kde je potřeba uchovat archiv videoklipů, které vás zajímají.

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
* [Nahrávání videa založeného na událostech](event-based-video-recording-concept.md)
<!--* [Quickstart: Event-based recording based on motion events]()-->
* [Kurz: vývoj modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Postup úpravy nasazení. *. template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Oddíl, [jak deklarovat trasy v manifestu nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Požadavky

Předpoklady pro tento kurz jsou následující:

* Instalace [Docker](https://docs.docker.com/desktop/) na vašem vývojovém počítači
* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí rozšíření [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a rozšíření [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Může se zobrazit výzva k instalaci Docker. Tuto výzvu můžete ignorovat.
* [Sada SDK .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na vašem vývojovém počítači.
* Dokončení [skriptu nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) a [nastavení prostředí](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Na konci výše uvedeného postupu budete mít k dispozici určité prostředky Azure nasazené v rámci předplatného Azure, včetně těchto:

* IoT Hub
* Účet úložiště
* Účet Media Services
* Virtuální počítač se systémem Linux

## <a name="concepts"></a>Koncepty

![Media Graph](./media/event-based-video-recording-tutorial/overview.png)

Nahrávání videa založeného na událostech (EVR) se týká procesu nahrávání videa aktivovaného událostí. Tato událost může být způsobena zpracováním samotného hlasového signálu (například při zjištění přesunu objektu ve videu) nebo z nezávislého zdroje (například otevřením dveří). Případně můžete spustit záznam pouze v případě, že externí služba Inferencing zjistí, že došlo k určité události.  V tomto kurzu použijete video o vozidlech, která se pohybují na dálnici, a zaznamenání videoklipů při zjištění nákladní části.

Diagram výše je obrazové znázornění [mediálního grafu](media-graph-concept.md) a dalších modulů, které doplní požadovaný scénář. Jsou zapojené čtyři IoT Edge moduly:

* Live video Analytics v modulu IoT Edge
* Modul AI sestavený pomocí [modelu Yolo V3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)
* Vlastní modul pro počítání a filtrování objektů (v diagramu výše označovaných jako čítač objektu), který budete sestavovat a nasazovat v tomto kurzu
* [Modul simulátoru RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pro simulaci kamery RTSP
    
Jak diagram znázorňuje, použijete [zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) v mediálním grafu pro zachycení živého videa a pošle toto video na dvě cesty.

* První cesta je uzel [procesoru filtru frekvence snímků](media-graph-concept.md#frame-rate-filter-processor) , který vypisuje snímky videa v zadané míře snímků. Tyto video rámce sever jako vstup pro uzel rozšíření HTTP. Uzel rozšíření HTTP odesílá snímky (jako obrázky) do modulu AI (YOLO V3 – což je detektor objektu) a přijímá výsledky – což bude objekty zjištěné modelem. Uzel rozšíření HTTP pak tyto výsledky publikuje prostřednictvím jímky zpráv IoT Hub do centra IoT Edge.
* Modul čítače objektu je nastaven tak, aby přijímal zprávy z centra IoT Edge – což zahrnuje výsledky detekce objektu (vozidla v provozu). Kontroluje zprávy, které hledají objekty určitého typu (nakonfigurované přes vlastnost s dvojitou vlastností), a vytvoří výstup zprávy do centra IoT Edge. Tyto zprávy se pak přesměrují zpátky do zdrojového uzlu IoT Hub v mediálním grafu. Po přijetí zprávy spustí zdrojový uzel IoT Hub v mediálním grafu uzel [procesoru brány signálu](media-graph-concept.md#signal-gate-processor) , aby otevřel bránu po nakonfigurovaném časovém intervalu. Video prochází bránou do uzlu jímka assetu za tuto dobu. Tato část živého streamu se pak zaznamená prostřednictvím uzlu [jímky assetu](media-graph-concept.md#asset-sink) k [assetu](terminology.md#asset) ve vašem účtu Azure Media Service.

## <a name="set-up-the-environment"></a>Nastavení prostředí

1. Naklonujte úložiště sem https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
2. Spusťte Visual Studio Code (VSCode) a otevřete složku, do které se úložiště stáhne.
3. V VSCode přejděte do složky src/Cloud-to-Device-Console-App a vytvořte soubor s názvem appSettings. JSON. Tento soubor bude obsahovat nastavení potřebná ke spuštění programu.
3. Po provedení skriptu pro nastavení prostředků zkopírujte obsah ze souboru clouddrive/lva-Sample/appSettings. JSON. Podívejte se na [#4 požadovaných součástí](event-based-video-recording-tutorial.md#prerequisites). Po dokončení skriptu nastavení prostředků kliknutím na složené závorky vystavte strukturu složky. Zobrazí se tři soubory vytvořené v rámci clouddrive/lva-Sample. Důležité je, že v současnosti jsou soubory. ENV a AppSetting. JSON. Budete je potřebovat k aktualizaci souborů v Visual Studio Code později v rychlém startu. Můžete je teď chtít zkopírovat do místního souboru.

    ![Nastavení aplikace](./media/quickstarts/clouddrive.png)

    Text ze souboru clouddrive/lva-Sample/appSettings. JSON by měl vypadat takto:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Potom přejděte do složky "src/Edge" a vytvořte soubor s názvem ". env".
1. Zkopírujte obsah ze souboru clouddrive/lva-Sample/. env. Text by měl vypadat takto:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```
## <a name="examine-the-template-file"></a>Kontrola souboru šablony 

Během instalace prostředí se spustí Visual Studio Code a otevře se složka obsahující vzorový kód.

V Visual Studio Code přejděte na "src/Edge". Zobrazí se soubor. ENV, který jste vytvořili, a také několik souborů šablon nasazení. Tyto šablony definují, které hraniční moduly budete nasazovat do virtuálního počítače se systémem Linux. Soubor. env obsahuje hodnoty proměnných používaných v těchto šablonách, jako je například připojovací řetězec IoT Hub, který umožňuje odesílat příkazy do hraničních modulů přes Azure IoT Hub.

Otevřete "src/Edge/Deployment. objectCounter. template. JSON". Všimněte si, že v části "moduly" jsou čtyři položky, které odpovídají položkám uvedeným výše (v části věnované koncepcím):

* lvaEdge – Toto je Live video Analytics v modulu IoT Edge.
* yolov3 – Toto je odvozená služba sestavená pomocí modelu YOLO v3.
* rtspsim – simulátor RTSP
* objectCounter – jedná se o modul, který hledá konkrétní objekty ve zprávách vrácených pomocí yolov3.

Pro modul objectCounter se podívejte na řetězec, který se používá pro hodnotu "image" – to je založené na kurzu vývoje IoT Edgeho modulu. Visual Studio Code automaticky rozpozná, že kód pro modul čítače objektu je pod položkou "src/Edge/modules/objectCounter". Přečtěte si část postup deklarace směrování v manifestu nasazení IoT Edge a pak zkontrolujte trasy v souboru JSON šablony. Všimněte si, jak:

* LVAToObjectCounter se používá k odesílání konkrétních událostí do konkrétního koncového bodu v modulu objectCounter.
* ObjectCounterToLVA se používá k odeslání události triggeru do konkrétního koncového bodu (který by měl být IoT Hub zdrojový uzel) v modulu lvaEdge.
* objectCounterToIoTHub se používá jako ladicí nástroj – pro lepší zobrazení výstupu z objectCounter při spuštění tohoto kurzu.

> [!NOTE]
> Požadované vlastnosti pro modul objectCounter – je nastavená tak, aby hledala objekty, které jsou označené jako "nákladní", s úrovní spolehlivosti minimálně 50%.

## <a name="deploy-the-edge-modules"></a>Nasazení hraničních modulů

Pomocí Visual Studio Code postupujte podle pokynů pro přihlášení do Docker a "Build and push IoT Edge řešení", ale pro tento krok použijte src/Edge/Deployment. objectCounter. template. JSON.

![Sestavování a nabízených IoT Edge řešení](./media/event-based-video-recording-tutorial/build-push.png)

Tím se vytvoří modul objectCounter pro počítání objektů a vložení obrázku do vaší Azure Container Registry (ACR).

* Ověřte, že máte proměnné prostředí CONTAINER_REGISTRY_USERNAME_myacr a CONTAINER_REGISTRY_PASSWORD_myacr definované v souboru. env.

Výše uvedený krok vytvoří manifest nasazení IoT Edge v souboru src/Edge/config/Deployment. objectCounter. amd64. JSON.

V Visual Studio Code přejděte na src/Edge/config/Deployment. objectCounter. amd64. JSON, klikněte pravým tlačítkem na soubor a vyberte vytvořit nasazení pro jedno zařízení. 

Pokud se jedná o první kurz s živým analýzou videa na IoT Edge, Visual Studio Code vás vyzve k zadání připojovacího řetězce IoTHub. Můžete ho zkopírovat ze souboru appSettings. JSON.

V dalším kroku Visual Studio Code požádat o výběr zařízení služby IoT Hub. Vyberte zařízení IoT Edge (mělo by se jednat o "lva-Sample-Device").

V této fázi se spustilo nasazení hraničních modulů do zařízení IoT Edge.
V přibližně 30 sekundách aktualizujte službu Azure IOT hub v levém dolním rohu Visual Studio Code a měli byste vidět, že jsou nasazené 4 moduly (Poznamenejte si názvy: lvaEdge, rtspsim, yolov3 a objectCounter).

![4 nasazené moduly](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Klikněte pravým tlačítkem myši na hraniční zařízení ("lva-Sample-Device") a klikněte na "spustit monitorování předdefinovaného koncového bodu události". Live video Analytics v modulu IoT Edge vygeneruje [provozní](#operational-events) a [diagnostické](#diagnostic-events) události do centra IoT Edge a tyto události můžete zobrazit v okně "výstup" v Visual Studio Code.

## <a name="run-the-program"></a>Spuštění programu

1. Visual Studio Code přejděte do části src/Cloud-to-Device-Console-App/Operations. JSON.
1. Pod uzlem GraphTopologySet nastavte následující: "topologyUrl": " https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json " 
1. Dále pod uzlem GraphInstanceSet upravte "Topology": "EVRtoAssetsOnObjDetect"
1. Stiskněte klávesu F5. Tím se spustí ladicí relace.
1. V okně terminálu se zobrazí odpovědi na volání [přímé metody](direct-methods.md) , které provádí program, do nástroje Live video Analytics v modulu IoT Edge, což je:

    1. GraphTopologyList – načte seznam topologie grafů, které byly přidány do modulu, pokud existují.

        Pokračujte stisknutím klávesy ENTER.
    1. GraphInstanceList – načte seznam instancí grafů, které byly vytvořeny, pokud existují.

        Pokračujte stisknutím klávesy ENTER.
    1. GraphTopologySet – přidá do modulu tuto topologii s názvem "EVRtoAssetsOnObjDetect".
    1. GraphInstanceSet – vytvoří instanci výše uvedené topologie, nahrazování parametrů
    1. V zájmu je parametr rtspUrl. Odkazuje na soubor MKV, který se stáhl do virtuálního počítače se systémem Linux, do umístění, ze kterého se simulátor RTSP přečte.
    1. GraphInstanceActivate – spustí graf médií, který způsobuje tok videa
    1. GraphInstanceList – Pokud chcete zobrazit, že teď máte instanci v modulu, na kterém běží

        V tuto chvíli byste měli pozastavit a *ne* stisknout Enter.
1. V okně výstup se zobrazí provozní a diagnostické zprávy, které se odesílají do IoT Hub, a to díky analýze živých videí v IoT Edge modulu.
1. Mediální graf bude nadále běžet a tisknout události – simulátor RTSP bude pokračovat ve vytváření smyček zdrojového videa. Aby bylo možné zastavit mediální graf, můžete znovu spustit příkaz Enter v okně terminálu. Program bude odesílat tyto akce:

    1. GraphInstanceDeactivate – zastavení instance grafu a zastavení nahrávání videa
    1. GraphInstanceDelete – odstranění instance z modulu
    1. GraphInstanceList – k zobrazení, že v modulu nejsou žádné instance

> [!NOTE]
> Topologie grafu se neodstranila. Pokud to potřebujete udělat, spusťte tento krok s následujícím textem JSON:

```
{
    "@apiVersion" : "1.0",
    "name" : "EVRtoAssetsOnObjDetect"
}
```

## <a name="examine-the-output"></a>Prozkoumání výstupu
 
Live video Analytics v IoT Edge generuje [provozní](#operational-events) a [diagnostické](#diagnostic-events) události do centra IoT Edge, což je text zobrazený v okně výstup Visual Studio Code postupovat podle formátu streamování pro zasílání zpráv, který je vytvořený pro komunikaci typu zařízení-Cloud, IoT Hub:

* Sada vlastností aplikace Slovník vlastností řetězce, které může aplikace definovat a přistupovat, aniž by bylo nutné deserializovat tělo zprávy. IoT Hub tyto vlastnosti nikdy nezmění
* Neprůhledný binární text

Ve zprávách níže jsou vlastnosti aplikace a obsah těla definovány pomocí nástroje Live video Analytics v modulu IoT Edge. Další informace najdete v tématu [monitorování a protokolování](monitoring-logging.md). 

## <a name="diagnostic-events"></a>Diagnostické události

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished 

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu na kontejneru simulátoru RTSP. V případě úspěchu se tato událost vytiskne. Všimněte si, že typ události je Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [2:02:54 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589749373980489 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T21:02:53.981Z",
    "dataVersion": "1.0"
  }
}
```

Je třeba počítat s následujícím:

* "Subject" v applicationProperties odkazuje na uzel v MediaGraph, ze kterého byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o diagnostické události – jedná se o zprávu SDP.

Zapište čas události – to je čas, kdy se zapnulo video přenosů (soubor MKV) do modulu jako živý datový proud.

## <a name="operational-events"></a>Provozní události

Jakmile se mediální graf spustí po nějakou dobu, nakonec získáte událost z modulu objektu Counter. 

```
[IoTHubMonitor] [2:03:21 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T21:03:21.062Z"
  }
}
```

ApplicationProperties obsahuje pouze čas události, což je čas, kdy modul zjistil, že výsledky z modulu YOLO V3 obsahují objekty Interest (nákladní automobily).

Můžou se zobrazit další informace o těchto událostech, když se ve videu zjistí další nákladní automobily.

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Skoro hned po čítači objektů odeslat událost se zobrazí událost typu Microsoft. Media. Graph. Operational. RecordingStarted

```
[IoTHubMonitor] [2:03:22 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T21:03:22.532Z",
    "dataVersion": "1.0"
  }
}
```

"Subject" v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu.

Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video. Tuto hodnotu byste si měli poznamenat.

### <a name="recordingavailable-event"></a>Událost RecordingAvailable

Když uzel jímka assetu nahrál video do assetu, vygeneruje tuto událost typu Microsoft. Media. Graph. Operational. RecordingAvailable

```
[IoTHubMonitor] [2:03:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T21:03:31.808Z",
    "dataVersion": "1.0"
  }
}
```

Tato událost označuje, že do Assetu byly zapsány dostatečné údaje, aby si hráči/klienti mohli zahájit přehrávání videa.

"Subject" v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu.

Tělo obsahuje informace o umístění výstupu, což je v tomto případě název Assetu služby Azure Media Service, do kterého se nahraje video.

### <a name="recordingstopped-event"></a>Událost RecordingStopped

Pokud prohlížíte nastavení aktivace (maximumActivationTime) pro uzel procesoru brány signálu v topologii), uvidíte, že je brána nastavená tak, aby se zavřela po 30 sekundách videa. Přibližně 30 sekund po události RecordingStarted by se měla zobrazit událost typu Microsoft. Media. Graph. Operation. RecordingStopped, která indikuje, že uzel jímky Assetu zastavil záznam videa do Assetu.

```
[IoTHubMonitor] [2:03:52 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T21:03:52.040Z",
    "dataVersion": "1.0"
  }
}
```

Tato událost označuje, že záznam byl zastaven.

"Subject" v applicationProperties odkazuje na uzel AssetSink v grafu, který vygeneroval tuto zprávu.

Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video.

## <a name="media-services-asset"></a>Prostředek Media Services  

Můžete prozkoumávat Media Services Asset, který byl vytvořen grafem, přihlášením k Azure Portal a zobrazením videa.

1. Otevřete webový prohlížeč a přejdete na [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.
1. Vyhledejte účet Media Services mezi prostředky, které máte ve svém předplatném, a otevřete okno účtu.
1. V seznamu Media Services klikněte na prostředky.

    ![Prostředky](./media/continuous-video-recording-tutorial/assets.png)
1. V seznamu se zobrazí Asset s názvem sampleAssetFromCVR-LVAEdge-{DateTime} – jedná se o vzor pojmenování zvolený v souboru topologie Media graphu.
1. Klikněte na prostředek.
1. Na stránce Podrobnosti o aktivech klikněte na odkaz **vytvořit nový** pod textovým polem Adresa URL streamování.

    ![Nový prostředek](./media/continuous-video-recording-tutorial/new-asset.png)

1. V průvodci, který se otevře, přijměte výchozí možnosti a stiskněte tlačítko Přidat. Další informace najdete v tématu [přehrávání videa](video-playback-concept.md).

    > [!TIP]
    > Ujistěte se, že [koncový bod streamování běží](../latest/streaming-endpoint-concept.md).
1. Přehrávač by měl načíst video a měli byste být schopni **Spustit Play**> * *, aby se zobrazila.

> [!NOTE]
> Vzhledem k tomu, že zdroj videa byl kontejner simulující kanál kamery, časová razítka ve videu souvisejí s tím, že jste aktivovali instanci grafu, a když ji deaktivujete. Další informace najdete v tématu o tom, jak procházet [více dní nahrávání](playback-multi-day-recordings-tutorial.md) a zobrazovat části tohoto archivu. V tomto kurzu si také můžete zobrazit časová razítka na obrazovce zobrazená na obrazovce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní kurzy, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento kurz, a odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na [stránce ONVIF – vyhovujících produktů](https://www.onvif.org/conformant-products/) .
* Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) a potom postupujte podle pokynů v části [nasazení prvního IoT Edge modulu do](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rychlého startu zařízení s nástrojem Virtual Linux a zaregistrovat zařízení ve službě Azure IoT Hub.
