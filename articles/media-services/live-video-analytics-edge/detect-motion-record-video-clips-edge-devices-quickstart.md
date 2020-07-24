---
title: Detekce videa o pohybových &ch na hraničních zařízeních – Azure
description: V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého informačního kanálu z kamery (simulované) IP adresy, zjistit, jestli je k dispozici nějaký pohyb, a pokud ano, zaznamenejte videoklip MP4 do místního systému souborů na hraničním zařízení.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 14dcc7b298244a1d53a9b820c641ea87c4f9a016
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091857"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Rychlý Start: detekce a nahrávání videa na hraničních zařízeních
 
V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého kanálu videa z (simulované) kamery IP. Ukazuje, jak zjistit, jestli je k dispozici nějaký pohyb, a pokud ano, zaznamenejte si videoklip ve formátu MP4 do místního systému souborů na hraničním zařízení. Rychlý Start používá virtuální počítač Azure jako zařízení IoT Edge a používá simulovaný živý Stream videa. 

Tento článek je založen na vzorovém kódu napsaném v jazyce C#. Sestavuje se v rychlém startu k [detekci pohybu a generování událostí](detect-motion-emit-events-quickstart.md) . 

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Visual Studio Code](https://code.visualstudio.com/)s následujícími příponami:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Pokud jste nedokončili rychlé zprovoznění [Najít pohyb a generovat události](detect-motion-emit-events-quickstart.md) , postupujte podle těchto kroků:
     1. [Nastavení prostředků Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Nastavení vývojového prostředí](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Generování a nasazení manifestu nasazení IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Příprava na monitorování událostí](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Při instalaci nástrojů Azure IoT se může zobrazit výzva k instalaci Docker. Bez obav tuto výzvu ignorujte.

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa
Při nastavování prostředků Azure pro účely tohoto rychlého startu se do virtuálního počítače se systémem Linux ve službě Azure, který se používá jako zařízení IoT Edge, zkopíruje krátké video dávky. Tento videosoubor se použije k simulaci živého datového proudu pro tento kurz.

Otevřete aplikaci, jako je [VLC Media Player](https://www.videolan.org/vlc/), vyberte CTRL + N a vložte [Tento odkaz](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) na video o zaparkované dávce a začněte přehrávat. V symbolech 5 sekund se bílá Auto pohybuje přes sérii parkovacích míst.

Proveďte následující kroky, pokud chcete zjistit pohyb auta a zaznamenání videoklipu, který začíná značkou 5 sekund, pomocí analýzy živých videí na IoT Edge.

## <a name="overview"></a>Přehled

![overview](./media/quickstarts/overview-qs4.png)

Předchozí diagram ukazuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje kameru protokolu IP, která je hostitelem serveru RTSP (Real-time streaming Protocol). [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru detekce pohybu](media-graph-concept.md#motion-detection-processor) . Zdroj RTSP pošle stejné snímky videa do uzlu [procesoru signálu signálu](media-graph-concept.md#signal-gate-processor) , který zůstane uzavřený, dokud ho událost nespustí.

Když procesor detekce pohybů detekuje pohyb ve videu, pošle událost na uzel procesoru brány signálu, který ho aktivuje. Brána se otevře po nakonfigurované době trvání a odeslání snímků videa do uzlu [jímky souborů](media-graph-concept.md#file-sink) . Tento uzel jímky zaznamená video jako soubor MP4 v místním systému souborů vašeho hraničního zařízení. Soubor je uložený v nakonfigurovaném umístění.

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.

## <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů
Jako součást požadavků pro tento rychlý Start jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte vzorový kód.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení, kde jsou proměnné použity pro některé vlastnosti. Soubor *. env* obsahuje hodnoty pro tyto proměnné.
1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:
    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsv*** seznamu operací, které má program spustit.
    * ***Program.cs*** – vzorový programový kód. Tento kód:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md). 
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.

1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Upravte adresu URL RTSP tak, aby odkazovala na videosoubor:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * V části `GraphTopologyDelete` upravte název:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Kontrola – zkontrolujte stav modulů.

V kroku [generování a nasazení IoT Edge manifestu nasazení](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) rozbalte v části Visual Studio Code uzel **lva-Sample-Device** v rámci služby **Azure IoT Hub** (v části vlevo dole). Měly by se zobrazit následující moduly nasazené:

* Modul Live video Analytics s názvem **lvaEdge**
* Modul **rtspsim** , který simuluje Server RTSP, který funguje jako zdroj živého informačního kanálu videa

  ![Moduly](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Kontrola – Příprava na monitorování událostí
Ujistěte se, že jste dokončili postup [přípravy na monitorování událostí](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Spustit sledování vestavěného koncového bodu události](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Spuštění ukázkového programu

1. Spusťte ladicí relaci, a to tak, že vyberete klávesu F5. Okno **terminálu** vytiskne některé zprávy.
1. *operations.jsv* kódu volá přímé metody `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po předchozích rychlých startech, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Vyberte klávesu ENTER.

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

     * Volání `GraphTopologySet` , které používá`topologyUrl` 
     * Volání `GraphInstanceSet` , které používá následující tělo:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "EVRToFilesOnMotionDetection",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
1. Výstup v okně **terminálu** pozastaví `Press Enter to continue` . Ještě nevybírejte ENTER. Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Přepněte do okna **výstup** v Visual Studio Code. Zobrazí se zprávy o tom, že modul analýzy živých videí v IoT Edge odesílá do služby IoT Hub. Následující část tohoto rychlého startu popisuje tyto zprávy.

1. Mediální graf bude nadále spouštět a tisknout výsledky. Simulátor RTSP zachovává smyčku zdrojového videa. Chcete-li zastavit graf médií, vraťte se do okna **terminálu** a vyberte Enter. 

    Další série volání čistí prostředky:
     * Volání `GraphInstanceDeactivate` deaktivuje instanci grafu.
     * Volání `GraphInstanceDelete` Odstraní instanci.
     * Volání pro `GraphTopologyDelete` odstranění topologie.
     * Konečné volání `GraphTopologyList` ukazuje, že seznam je nyní prázdný.

## <a name="interpret-results"></a>Interpretace výsledků 
Když spustíte graf médií, výsledky z uzlu procesoru snímače pohybu procházejí uzlem IoT Hub jímky do služby IoT Hub. Zprávy, které vidíte v okně **výstup** Visual Studio Code obsahují `body` oddíl a `applicationProperties` oddíl. Další informace najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách modul Live video Analytics definuje vlastnosti aplikace a obsah těla.

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP, který běží na kontejneru rtspsim-live555. Pokud je připojení úspěšné, bude vytištěna následující událost.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

V předchozím výstupu: 

* Zpráva je událost diagnostiky, `MediaSessionEstablished` . Indikuje, že zdrojový uzel RTSP (předmět) navázal připojení k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V `applicationProperties` aplikaci `subject` odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je událost diagnostiky.
* `eventTime`Hodnota je čas, kdy došlo k události.
* `body`Oddíl obsahuje data o diagnostickou událost. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Když se zjistí pohyb, aktivuje se uzel procesoru signálu signálu a v mediálním grafu se zahájí zápis souboru MP4. Uzel jímky souborů odesílá provozní událost. `type`Je nastavena na hodnotu `motion` , která označuje, že se jedná o výsledek procesoru detekce pohybu. `eventTime`Hodnota je čas UTC, kdy došlo k pohybu. Další informace o tomto procesu najdete v části [Přehled](#overview) v tomto rychlém startu.

Tady je příklad této zprávy:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

V předchozí zprávě: 

* V `applicationProperties` aplikaci `subject` odkazuje na uzel v grafu médií, ze kterého byla zpráva generována. V tomto případě zpráva pochází z uzlu jímky souborů.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je funkční.
* `eventTime`Hodnota je čas, kdy došlo k události. Tato doba je 5 až 6 sekund po `MediaSessionEstablished` a po spuštění videa se spustí Flow. Tato doba odpovídá označení 5 až 6 sekund, když se [auto začalo přesunout](#review-the-sample-video) do parkovací dávky.
* `body`Oddíl obsahuje data o provozní události. V tomto případě data obsahují `outputType` a `outputLocation` .
* `outputType`Proměnná označuje, že tyto informace se týkají cesty k souboru.
* `outputLocation`Hodnota je umístění souboru MP4 v modulu Edge.

### <a name="recordingstopped-and-recordingavailable-events"></a>Události RecordingStopped a RecordingAvailable

Pokud provedete kontrolu vlastností uzlu procesoru brány signálu v [topologii grafu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), uvidíte, že časy aktivace jsou nastavené na 5 sekund. Přibližně 5 sekund po `RecordingStarted` přijetí události získáte:

* `RecordingStopped`Událost, která označuje, že záznam byl zastaven.
* `RecordingAvailable`Událost, která označuje, že soubor MP4 se teď dá použít k zobrazení.

Dvě události se obvykle generují během několika sekund.

## <a name="play-the-mp4-clip"></a>Přehrát klip MP4

Soubory MP4 jsou zapsány do adresáře na hraničním zařízení, které jste nakonfigurovali v souboru *. env* pomocí klíče OUTPUT_VIDEO_FOLDER_ON_DEVICE. Pokud jste použili výchozí hodnotu, výsledky by se měly nacházet ve složce */Home/lvaadmin/Samples/Output/* .

Přehrání klipu MP4:

1. Přejít do skupiny prostředků, najděte virtuální počítač a pak se připojte pomocí Azure bastionu.

    ![Skupina prostředků](./media/quickstarts/resource-group.png)
    
    ![Virtuální počítač](./media/quickstarts/virtual-machine.png)

1. Přihlaste se pomocí přihlašovacích údajů, které jste vygenerovali při [nastavování prostředků Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Na příkazovém řádku přejdete do odpovídajícího adresáře. Výchozí umístění je */Home/lvaadmin/Samples/Output*. Měli byste vidět soubory MP4 v adresáři.

    ![Výstup](./media/quickstarts/samples-output.png) 

1. Pomocí [zabezpečeného kopírování (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) zkopírujte soubory do místního počítače. 
1. Hrajte soubory pomocí [VLC Media Playeru](https://www.videolan.org/vlc/) nebo jiného přehrávače MP4.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, potom ponechejte prostředky, které jste vytvořili. V opačném případě v Azure Portal do skupin prostředků vyberte skupinu prostředků, ve které jste spustili tento rychlý Start, a pak odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

* Postup pro použití AI na živé video kanály najdete v kurzu [spuštění živého videa s](use-your-model-quickstart.md) využitím vlastního rychlého startu.
* Přečtěte si další výzvy pro pokročilé uživatele:

    * Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) , který podporuje RTSP. Kamery protokolu IP, které podporují protokol RTSP, najdete na stránce vyhovující [ONVIF produktů](https://www.onvif.org/conformant-products) . Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
    * Místo použití virtuálního počítače se systémem Linux v Azure použijte zařízení AMD64 nebo x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Pak postupujte podle pokynů v části [nasazení prvního IoT Edge modulu do zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md) a zaregistrujte zařízení ve službě Azure IoT Hub.
