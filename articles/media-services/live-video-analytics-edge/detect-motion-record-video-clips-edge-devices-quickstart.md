---
title: Detekce pohybu, nahrávání videa u hraničních zařízení – Azure
description: V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého informačního kanálu z kamery (simulované) IP adresy, zjistit, jestli je k dispozici nějaký pohyb, a pokud ano, zaznamenejte videoklip MP4 do místního systému souborů na hraničním zařízení.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262078"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Rychlý Start: zjištění pohybu, záznam videa na hraničních zařízeních
 
V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého informačního kanálu z kamery (simulované) IP adresy, zjistit, jestli je k dispozici nějaký pohyb, a pokud ano, zaznamenejte videoklip MP4 do místního systému souborů na hraničním zařízení. Používá virtuální počítač Azure jako zařízení IoT Edge a simulovaný Stream živého videa. Tento článek je založen na vzorovém kódu napsaném v jazyce C#.

Tento článek se sestavuje nad [tímto](detect-motion-emit-events-quickstart.md) rychlým startem. 

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na počítači s následujícími příponami:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* V systému je nainstalovaná [sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) .
* Pokud jste [Tento](detect-motion-emit-events-quickstart.md) rychlý Start ještě nedokončili, dokončete následující kroky:
     * [Nastavení prostředků Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Nastavení vývojového prostředí](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Generování a nasazení manifestu nasazení IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Příprava na monitorování událostí](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Při instalaci nástrojů Azure IoT se může zobrazit výzva k instalaci Docker. Klidně ho ignorujte.

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa
V rámci výše uvedeného postupu pro nastavení prostředků Azure se do virtuálního počítače Linux v Azure, který se používá jako zařízení IoT Edge, zkopíruje (krátké) video o zaparkované dávce. Tento videosoubor se použije k simulaci živého datového proudu pro tento kurz.

Můžete použít aplikaci, jako je [VLC Player](https://www.videolan.org/vlc/), spustit ji, stisknout CTRL + N a vložit [Tento](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) odkaz na video o zaparkované dávce. tím spustíte přehrávání. V symbolech 5 sekund se bílá Auto pohybuje přes sérii parkovacích míst.

Po dokončení níže uvedeného postupu budete pro detekci pohybu auta používat Live video Analytics na IoT Edge a zaznamenáte si video klip počínaje kolem této 5 sekundové značky.

## <a name="overview"></a>Přehled

![overview](./media/quickstarts/overview-qs4.png)

Výše uvedený diagram ukazuje, jak tok signalizuje v tomto rychlém startu. Modul Edge (popsaný [tady](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simuluje fotoaparát IP, který hostuje server RTSP. [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru detekce pohybu](media-graph-concept.md#motion-detection-processor) . Zdroj RTSP pošle stejné snímky videa do uzlu [procesoru služby signálu](media-graph-concept.md#signal-gate-processor) , který zůstane uzavřený, dokud ho událost neaktivuje.

Když procesor pro detekci pohybů zjistí, že se pohyb nachází ve videu, pošle událost na uzel procesoru brány signálu, který ho aktivuje. Brána se otevře po nakonfigurované době trvání a odeslání snímků videa do uzlu [jímky souborů](media-graph-concept.md#file-sink) . Tento uzel jímky zaznamená video jako soubor MP4 do místního systému souborů vašeho hraničního zařízení v nakonfigurovaném umístění.

V tomto rychlém startu budete:

1. Vytvoření a nasazení mediálního grafu
1. Interpretace výsledků
1. Vyčištění prostředků

## <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů
Jako součást požadavků byste stáhli vzorový kód do složky. Spusťte Visual Studio Code a otevřete složku.

1. V Visual Studio Code přejděte na "src/Edge". Zobrazí se soubor. ENV, který jste vytvořili společně s několika soubory šablon nasazení.
    * Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení s některými zástupnými hodnotami. Soubor. env obsahuje hodnoty pro tyto proměnné.
1. Pak přejděte do složky src/Cloud-to-Device-Console-App. Tady se zobrazí soubor appSettings. JSON, který jste vytvořili spolu s několika dalšími soubory:
    * C2D-Console-App. csproj – Toto je soubor projektu pro Visual Studio Code
    * Operations. JSON – tento soubor bude mít přehled o různých operacích, které by měl program běžet.
    * Program.cs – jedná se o vzorový programový kód, který provede následující:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody vystavené živým analýzou videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md) . 
        * Pozastaví se, abyste prozkoumali výstup programu v okně terminálu a události generované modulem v okně výstup.
        * Vyvolá přímé metody pro vyčištění prostředků.   

1. Proveďte následující úpravy souboru Operations. JSON.
    * Změňte odkaz na topologii grafu:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * V části GraphInstanceSet upravte název topologie grafu tak, aby odpovídala hodnotě v odkazu výše.`"topologyName" : "EVRToFilesOnMotionDetection"`
    * Také upravte adresu URL RTSP tak, aby odkazovala na požadovaný videosoubor.`"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * V části GraphTopologyDelete upravte název.`"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Kontrola – Kontrola stavu modulů
V kroku [generování a nasazení manifestu nasazení IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) v části Visual Studio Code rozbalte uzel "lva-Sample-Device" v části Azure IoT Hub (v levém dolním rohu), měli byste vidět následující moduly nasazené

    1. Modul Live video Analytics s názvem "lvaEdge"
    1. Modul nazvaný "rtspsim", který simuluje Server RTSP, který funguje jako zdroj živého informačního kanálu videa

        ![Moduly](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Kontrola – Příprava na monitorování událostí
Ověřte, že jste dokončili kroky pro [přípravu monitorování událostí](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events) .

![Spustit sledování vestavěného koncového bodu události](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Spuštění ukázkového programu

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
Při spuštění grafu médií jsou výsledky z uzlu procesoru detektoru pohybu odesílány prostřednictvím uzlu IoT Hub jímka do IoT Hub. Zprávy, které vidíte v okně výstup Visual Studio Code, obsahují oddíl "tělo" a část "applicationProperties". Informace o tom, co tyto oddíly představuje, najdete v [tomto](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) článku.

Ve zprávách níže jsou vlastnosti aplikace a obsah těla definovány modulem Live video Analytics.

## <a name="mediasession-established-event"></a>MediaSession vytvořená událost

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP běžícímu na kontejneru rtspsim-live555. V případě úspěchu se tato událost vytiskne:

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

* Zpráva je diagnostická událost MediaSessionEstablished, která indikuje, že uzel zdroje RTSP (předmět) byl schopný navázat spojení s simulátorem RTSP a zahájit příjem (simulovaného) živého kanálu.
* "Subject" v applicationProperties odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o diagnostické události, což je v tomto případě podrobnosti o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="recording-started-event"></a>Událost zahájení záznamu

Jak je vysvětleno [zde](#overview), je při zjištění pohybu aktivován uzel procesoru signálu signálu a uzel jímky souborů v mediálním grafu začne zapisovat do souboru MP4. Uzel jímky souborů odesílá provozní událost. Typ je nastaven na "pohyb", aby označoval, že se jedná o výsledek procesoru detekce pohybu, a čas události oznamuje, v jakém čase (UTC) došlo k pohybu. Níže je příklad:

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

* "Subject" v applicationProperties odkazuje na uzel v grafu médií, ze kterého byla zpráva vygenerována. V tomto případě zpráva pochází z uzlu jímky souborů.
* eventType v applicationProperties značí, že se jedná o provozní událost.
* "čas události" označuje čas, kdy došlo k události. Všimněte si, že tato 5-6 sekund po zahájení toku MediaSessionEstablished a videa. To odpovídá druhé značce 5-6, když se [auto začalo přesunout](#review-the-sample-video) do parkovací dávky.
* "tělo" obsahuje data o provozní události, která je v tomto případě "outputType" a "outputLocation" data.
* "outputType" značí, že tyto informace se týkají cesty k souboru.
* "outputLocation" poskytuje umístění souboru MP4 v rámci modulu Edge

## <a name="recording-stopped-and-available-events"></a>Záznam zastaveno a dostupné události

Pokud provedete kontrolu vlastností uzlu procesoru brány signálu v [topologii grafu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), uvidíte, že doba aktivace byla nastavena na 5 sekund. Přibližně 5 sekund po přijetí události RecordingStarted se zobrazí
* Událost RecordingStopped, která indikuje, že se záznam zastavil
* Událost RecordingAvailable, která označuje, že soubor MP4 se teď dá použít k zobrazení

Dvě události se obvykle generují s několika sekundami.

### <a name="playing-back-the-mp4-clip"></a>Přehrávání klipu MP4

1. Soubory MP4 jsou zapsány do adresáře na hraničním zařízení, které jste nakonfigurovali v souboru. env prostřednictvím této klávesy OUTPUT_VIDEO_FOLDER_ON_DEVICE. Pokud jste ho opustili na výchozí hodnotu, výsledky by měly být v/Home/lvaadmin/Samples/Output/.
1. Přejít do skupiny prostředků, najít virtuální počítač a připojit se pomocí bastionu

    ![Skupina prostředků](./media/quickstarts/resource-group.png)
 
    ![Virtuální počítač](./media/quickstarts/virtual-machine.png)
1. Po přihlášení (pomocí přihlašovacích údajů, které byly vygenerovány během [tohoto](detect-motion-emit-events-quickstart.md#set-up-azure-resources) kroku), přejděte na příkazovém řádku do příslušného adresáře (výchozí:/Home/lvaadmin/Samples/Output) a měli byste vidět soubory MP4. [Soubory](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) můžete přetáhnout do místního počítače a znovu je přehrát přes [VLC Player](https://www.videolan.org/vlc/) nebo jakýkoli jiný přehrávač MP4.

    ![Výstup](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento rychlý Start, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

* Spouštějte [živé video analýzy pomocí vlastního](use-your-model-quickstart.md) rychlého startu modelu, který ukazuje, jak použít AI na živé video kanály.
* Přečtěte si další výzvy pro pokročilé uživatele:

    * Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) s podporou pro RTSP. V případě, že hledáte zařízení, která jsou v souladu s profily G, S nebo T, můžete vyhledat kamery s podporou protokolu RTSP na stránce [ONVIF – vyhovujících produktů](https://en.wikipedia.org/wiki/IP_camera) .
    * Použijte zařízení AMD64 nebo x64 Linux (vs. pomocí virtuálního počítače Azure Linux). Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) a potom postupujte podle pokynů v části [nasazení prvního IoT Edge modulu do](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rychlého startu zařízení s nástrojem Virtual Linux a zaregistrovat zařízení ve službě Azure IoT Hub.
