---
title: Analýza živého videa pomocí OpenVINO™ modelového serveru – rozšíření AI od Intel
description: V tomto kurzu použijete server modelů AI, který poskytuje Intel, k analýze živého kanálu videa z (simulované) kamery IP.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 4ef0b919384839108c003488b0697054ebfa14a0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629160"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Kurz: Analýza živého videa pomocí OpenVINO™ modelového serveru – rozšíření AI od Intel 

V tomto kurzu se dozvíte, jak používat rozšíření OpenVINO™ model Server – AI od společnosti Intel k analýze živého kanálu videa z (simulované) kamery IP. Uvidíte, jak tento server odvození poskytuje přístup k modelům pro detekci objektů (osoba, vozidlo nebo kolo) a modelu pro klasifikaci vozidel. Do tohoto odvozeného serveru se pošle podmnožina snímků v živém obrazovém kanálu a výsledky se odešlou do centra IoT Edge.

Tento kurz používá virtuální počítač Azure jako zařízení IoT Edge a používá simulovaný živý Stream videa. Vychází z ukázkového kódu napsaného v jazyce C# a sestavuje se v rychlém startu pro [detekci pohybů a generování událostí](detect-motion-emit-events-quickstart.md) .

> [!NOTE]
> Tento kurz vyžaduje použití počítače s platformou X86-64 jako hraničního zařízení.

## <a name="prerequisites"></a>Požadavky

* Účet Azure, který zahrnuje aktivní předplatné. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
  > [!NOTE]
  > Budete potřebovat předplatné Azure s oprávněním pro vytváření instančních objektů (Tato **role vlastníka** poskytuje). Pokud nemáte správná oprávnění, obraťte se na správce účtu, abyste vám udělili správná oprávnění. 
* [Visual Studio Code](https://code.visualstudio.com/)s následujícími příponami:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Pokud jste nedokončili rychlý Start pro [detekci pohybů a generování událostí](detect-motion-emit-events-quickstart.md) , nezapomeňte provést postup pro [nastavení prostředků Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Při instalaci nástrojů Azure IoT se může zobrazit výzva k instalaci Docker. Výzvu můžete ignorovat.

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa

Při nastavování prostředků Azure se krátké video dávky za parkování zkopíruje do virtuálního počítače Linux v Azure, který používáte jako zařízení IoT Edge. V tomto rychlém startu se k simulaci živého streamu používá videosoubor.

Otevřete aplikaci, jako je [VLC Media Player](https://www.videolan.org/vlc/). Vyberte CTRL + N a pak vložte odkaz na [video](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) a začněte přehrávání. Uvidíte záběry vozidel v sérii parkovacích míst, většinu z nich zaparkované a jeden přesun.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

V tomto rychlém startu budete používat Live video Analytics na IoT Edge spolu s rozšířením serverového™ OpenVINO – AI od společnosti Intel ke zjištění objektů, jako jsou například vozidla nebo jejich klasifikace. Výsledné odvozené události publikujete do centra IoT Edge.

## <a name="overview"></a>Přehled

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Přehled":::

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server protokolu RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru rozšíření http](media-graph-concept.md#http-extension-processor) . 

Uzel rozšíření HTTP hraje roli proxy serveru. Slouží k tomu, že vypíše příchozí snímky videa nastavené vámi `samplingOptions` a také převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes REST do jiného modulu Edge, který spouští modely AI za koncovým bodem HTTP. V tomto příkladu je to modul Edge OpenVINO™ model Server – AI rozšíření od společnosti Intel. Uzel procesoru rozšíření HTTP shromáždí výsledky detekce a publikuje události do uzlu [IoT Hub jímka](media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

V tomto kurzu:

1. Vytvořte a nasaďte mediální graf a změňte ho.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>O OpenVINO™ modelovém serveru – rozšíření AI od společnosti Intel

Intel® distribuce sady [nástrojů OpenVINO™ Toolkit](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (Open Visual neuronovéing a optimalizace sítě) je bezplatná softwarová sada, která vývojářům a odborníkům přes data zrychlí úlohy počítačového zpracování obrazu, zjednodušuje rozmístění a nasazení hloubkového učení a umožňuje snadné a heterogenní spouštění napříč platformami Intel® od Edge až po Cloud. Obsahuje sadu nástrojů pro nasazení Intel® pro hloubkové učení s modulem pro optimalizaci a odvozování modelů a [otevřený model](https://github.com/openvinotoolkit/open_model_zoo) úložiště ve více než 40 optimalizovaných předem vyškolených modelů.

Aby bylo možné vytvářet složitá, vysoce výkonná řešení živé analýzy videí, je třeba, aby se analýza živých videí v modulu IoT Edge spojila s výkonným modulem odvození, který může využít škálování na hraničních zařízeních. V tomto kurzu se požadavky na odvození odesílají do [rozšíření OpenVINO™ model Server – AI od společnosti Intel](https://aka.ms/lva-intel-ovms), což je modul Edge, který je navržený tak, aby fungoval s živým analýzou videí v IoT Edge. Tento modul serveru odvození obsahuje OpenVINO™ model Server (OVMS), odvozený Server, který využívá sadu OpenVINO™ Toolkit, která je vysoce optimalizovaná pro úlohy počítačové vize a vyvinutá pro architektury Intel®. Do OVMS bylo přidáno rozšíření pro snadné výměny snímků videa a odvození výsledků mezi odvozeným serverem a živým analýzou videí v IoT Edge modul, takže vám umožní spustit libovolný model podporovaný OpenVINO™ Toolkit (můžete přizpůsobit modul odvození serveru úpravou [kódu](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)). Můžete dál vybírat z nejrůznějších mechanismů akcelerace, které poskytuje Intel® hardware. Mezi ně patří procesory (Atom, Core, Xeon), FPGA, VPUs.

V počáteční verzi tohoto odvozeného serveru máte přístup k následujícím [modelům](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Detekce vozidel (odvozená adresa URL: http://{Module-Name}: 4000/vehicleDetection)
- Rozpoznávání osob/vozidel a kol (adresa URL odvození: http://{Module-Name}: 4000/personVehicleBikeDetection)
- Klasifikace vozidel (odvozená adresa URL: http://{Module-Name}: 4000/vehicleClassification)
- Rozpoznávání tváře (odvozená adresa URL: http://{Module-Name}: 4000/faceDetection)

> [!NOTE]
> Stažením a použitím modulu Edge: OpenVINO™ Server modelu – rozšíření AI od společnosti Intel a Zahrnutého softwaru vyjadřujete souhlas s podmínkami a ujednáními v rámci [licenční smlouvy](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Společnost Intel se zavazuje respektovat lidská práva a zabránit complicity v zneužívání lidských práv. Přečtěte si téma [globální zásady lidských práv pro lidská práva společnosti Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkty a software Intel jsou určeny jenom v aplikacích, které nezpůsobují nebo přispívají k porušení mezinárodní uznávané lidského práva.

## <a name="create-and-deploy-the-media-graph"></a>Vytvoření a nasazení mediálního grafu

### <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů

Jako součást požadavků jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte ukázkové soubory.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení hraničního zařízení. Obsahuje některé zástupné hodnoty. Soubor *. env* obsahuje hodnoty pro tyto proměnné.

1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:

    * ***C2D-Console-App. csproj** _ – soubor projektu pro Visual Studio Code.
    _ ***operations.jsv** _ – seznam operací, které má program spustit.
    _ ***Program.cs** _ – ukázkový kód programu. Tento kód:

        _ Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md).
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.


1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:

      `"topologyName" : "InferencingWithOpenVINO"`

    * V části `GraphTopologyDelete` upravte název:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

1. Klikněte pravým tlačítkem myši na soubor *Src/Edge/deployment.openvino.template.jsv* souboru a potom vyberte možnost **generovat manifest nasazení IoT Edge**.

    ![Generovat manifest nasazení IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.yolov3.amd64.js* v souboru manifestu se vytvoří ve složce *Src/Edge/config* .

1. Pokud jste dokončili rychlé zprovoznění [Najít pohyb a generovat události](detect-motion-emit-events-quickstart.md) , tento krok přeskočte. 

    V opačném případě v levém dolním rohu poblíž podokna **Azure IoT Hub** vyberte ikonu **Další akce** a pak vyberte **nastavit IoT Hub připojovací řetězec**. Můžete zkopírovat řetězec z *appsettings.jsv* souboru. Nebo pokud chcete mít jistotu, že jste nakonfigurovali správné centrum IoT v rámci Visual Studio Code, použijte [příkaz vybrat IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Nastavit připojovací řetězec IoT Hub](./media/quickstarts/set-iotconnection-string.png)

1. Klikněte pravým tlačítkem na *Src/Edge/config/deployment.openvino.amd64.jsna* a vyberte **vytvořit nasazení pro jedno zařízení**. 

    ![Vytvoření nasazení pro jedno zařízení](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Až budete vyzváni k výběru zařízení IoT Hub, vyberte **lva-Sample-Device**.
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:

    * Modul Live video Analytics s názvem **lvaEdge**
    * Modul **rtspsim** , který simuluje Server RTSP a funguje jako zdroj živého kanálu videa
    * Modul **openvino** , což je modul rozšíření Openvino™ modelů – AI od Intel 

### <a name="prepare-to-monitor-events"></a>Příprava na monitorování událostí

Klikněte pravým tlačítkem na zařízení Live video Analytics a vyberte **Spustit sledování předdefinovaného koncového bodu události**. Tento krok potřebujete, pokud chcete monitorovat události IoT Hub v okně **výstup** Visual Studio Code. 

![Spustit monitorování](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Spuštění ukázkového programu pro detekci vozidel
Pokud otevřete [topologii grafu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) pro tento kurz v prohlížeči, uvidíte, že hodnota `inferencingUrl` byla nastavena na `http://openvino:4000/vehicleDetection` , což znamená, že odvozený server vrátí výsledky po zjištění vozidel, pokud jsou v živém videu.

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::
1. Chcete-li spustit relaci ladění, vyberte klávesu F5. V okně **terminálu** se zobrazí zprávy tištěné.
1. *operations.js* kódu začíná s voláními přímých metod `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po dokončení předchozích rychlých startů, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Chcete-li pokračovat, vyberte klávesu ENTER.

    V okně **terminálu** se zobrazí další sada volání přímých metod:

     * Volání `GraphTopologySet` , které používá předchozí `topologyUrl`
     * Volání `GraphInstanceSet` , které používá následující tělo:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
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

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP, který běží na kontejneru rtspsim-live555. Pokud je připojení úspěšné, bude vytištěna následující událost. Typ události je **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

V této zprávě si všimněte těchto podrobností:

* Zpráva je událost diagnostiky. `MediaSessionEstablished` indikuje, že zdrojový uzel RTSP (předmět) připojený k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V `applicationProperties` nástroji `subject` označuje, že zpráva byla vygenerována ze zdrojového uzlu RTSP v mediálním grafu.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je událost diagnostiky.
* `eventTime`Určuje čas, kdy došlo k události.
* `body`Obsahuje data o události diagnostiky. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Odvozená událost

Uzel procesoru rozšíření HTTP přijímá výsledky odvození z modulu rozšíření OpenVINO™ model serveru – AI. Následně vygeneruje výsledky prostřednictvím uzlu IoT Hub jímka jako odvozené události. 

V těchto událostech je typ nastaven na hodnotu, `entity` aby označoval, že se jedná o entitu, jako je například automobil nebo nákladní automobil. `eventTime`Hodnota je čas UTC při zjištění objektu. 

V následujícím příkladu se zjistila dvě vozidla s hodnotami spolehlivosti vyššími než 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

V části zprávy si všimněte následujících podrobností:

* V `applicationProperties` aplikaci `subject` odkazuje na uzel v topologii grafu, ze které byla zpráva generována. 
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je analytická událost.
* `eventTime`Hodnota je čas, kdy došlo k události.
* `body`Oddíl obsahuje data o události analýzy. V tomto případě je událost odvozená událost, takže tělo obsahuje `inferences` data.
* `inferences`Část označuje, že `type` je `entity` . Tato část obsahuje další informace o entitě.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Spuštění ukázkového programu pro detekci osob nebo vozidel nebo kol
Chcete-li použít jiný model, budete muset upravit topologii grafu a také `operations.json` soubor.

Zkopírujte [topologii grafu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) do místního souboru, řekněme `C:\TEMP\topology.json` . Otevřete tuto kopii a upravte hodnotu `inferencingUrl` na `http://openvino:4000/personVehicleBikeDetection` .

V dalším kroku, v Visual Studio Code, navštivte složku *Src/Cloud-to-Device-Console-App-Device-Console* -a otevřete `operations.json` soubor. Upravte řádek `topologyUrl` na:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Nyní můžete opakovat výše uvedené kroky a znovu spustit ukázkový program s novou topologií. Výsledky odvození budou podobné (ve schématu) k modelu detekce vozidel, a to pouze s `subtype` nastavením na `personVehicleBikeDetection` .

## <a name="run-the-sample-program-to-classify-vehicles"></a>Spuštění ukázkového programu pro klasifikaci vozidel
V Visual Studio Code otevřete místní kopii `topology.json` z předchozího kroku a upravte hodnotu `inferencingUrl` na `http://openvino:4000/vehicleClassification` . Pokud jste spustili předchozí příklad pro detekci osob nebo vozidel nebo kol, nemusíte `operations.json` soubor upravovat znovu.

Nyní můžete opakovat výše uvedené kroky a znovu spustit ukázkový program s novou topologií. Ukázka výsledku klasifikace je následující.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Spuštění ukázkového programu pro detekci ploch
V Visual Studio Code otevřete místní kopii `topology.json` z předchozího kroku a upravte hodnotu `inferencingUrl` na `http://openvino:4000/faceDetection` . Pokud jste spustili předchozí příklad pro detekci osob nebo vozidel nebo kol, nemusíte `operations.json` soubor upravovat znovu.

Nyní můžete opakovat výše uvedené kroky a znovu spustit ukázkový program s novou topologií. Výsledek detekce vzorku je následující (Poznámka: výše použité video o zaparkování neobsahuje žádné zjistitelné plošky – pro účely tohoto modelu byste měli jiné video).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet další rychlé starty nebo kurzy, ponechte prostředky, které jste vytvořili. V opačném případě Azure Portal, přejít do skupin prostředků, vyberte skupinu prostředků, ve které jste spustili tento kurz, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další výzvy pro pokročilé uživatele:

* Místo používání simulátoru RTSP použijte [kameru IP](https://en.wikipedia.org/wiki/IP_camera) , která má podporu pro RTSP. Můžete vyhledat kamery protokolu IP, které podporují protokol RTSP na stránce ONVIF, která je v [souladu](https://www.onvif.org/conformant-products/) s těmito produkty. Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
* Místo virtuálního počítače Azure Linux použijte zařízení AMD64 nebo x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge.md). Pak zařízení Zaregistrujte v Azure IoT Hub podle pokynů v tématu [nasazení prvního modulu IoT Edge na zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md).