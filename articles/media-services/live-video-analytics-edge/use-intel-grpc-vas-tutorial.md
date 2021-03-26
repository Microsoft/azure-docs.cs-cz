---
title: Analýza živého videa pomocí rozšíření Intel OpenVINO™ DL načtení proudu ImageList – Edge AI přes gRPC
description: V tomto kurzu se dozvíte, jak používat rozšíření Intel OpenVINO™ DL načtení proudu ImageList – Edge AI od společnosti Intel k analýze živého kanálu videa z (simulované) kamery IP.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 07a7daf6363f0e528f84635ed6713ac462f89ca5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562847"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Kurz: Analýza živého videa pomocí rozšíření Intel OpenVINO™ DL načtení proudu ImageList – Edge AI 

V tomto kurzu se dozvíte, jak používat rozšíření Intel OpenVINO™ DL načtení proudu ImageList – Edge AI od společnosti Intel k analýze živého kanálu videa z (simulované) kamery IP. Uvidíte, jak tento server odvození poskytuje přístup k různým modelům pro detekci objektů (osoba, vozidlo nebo kolo), klasifikaci objektů (označení vozidel) a modelu pro sledování objektů (osoba, vozidlo a kolo). Integrace s modulem gRPC umožňuje odesílat snímky videa na odvozený Server AI. Výsledky se pak odesílají do centra IoT Edge. Když spustíte tuto službu odvození na stejném výpočetním uzlu jako Live video Analytics, můžete využít výhod posílání videí pomocí sdílené paměti. To vám umožní spustit Inferencing na kmitočtu snímků živého informačního kanálu videa (např. 30 snímků/s). 

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

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

V tomto rychlém startu budete používat Live video Analytics na IoT Edge společně s rozšířením Intel OpenVINO™ DL načtení proudu ImageList – Edge AI od společnosti Intel ke zjištění objektů, jako jsou například vozidla, ke klasifikaci vozidel nebo ke sledování vozidel, osob nebo kol. Výsledné odvozené události publikujete do centra IoT Edge.

## <a name="overview"></a>Přehled

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Přehled LVA MediaGraph":::

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server protokolu RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru rozšíření gRPC](media-graph-concept.md#grpc-extension-processor) . 

Uzel procesoru rozšíření gRPC přijímá jako vstup Dekódovatelné snímky videa a přenáší takové snímky do koncového bodu [gRPC](terminology.md#grpc) vystaveného serverem gRPC. Uzel podporuje přenos dat pomocí [sdílené paměti](https://en.wikipedia.org/wiki/Shared_memory) nebo přímý vkládání obsahu do textu zpráv gRPC. Kromě toho má uzel vestavěný formátovací modul obrázků pro škálování a kódování snímků videa před jejich přenosem na koncový bod gRPC. Měřítko obsahuje možnosti pro poměr stran obrázku, který má být zachován, doplněn nebo roztažen. Image Encoder podporuje formáty JPEG, PNG nebo BMP. Přečtěte si další informace [o procesoru.](media-graph-extension-concept.md#grpc-extension-processor)

V tomto kurzu:

1. Nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>O Intel OpenVINO™ DL načtení proudu ImageList – modul rozšíření AI pro Edge


Modul rozšíření AI OpenVINO™ DL načtení proudu ImageList-Edge je mikroslužba založená na videu od společnosti Intel pro zpracování videí, která poskytuje kanály video Analytics vytvořené pomocí OpenVINO™ DL načtení proudu ImageList. Vývojáři mohou odesílat Dekódovatelné snímky videa do modulu rozšíření AI, který provádí zjišťování, klasifikaci nebo sledování a vrací výsledky. Modul rozšíření AI zveřejňuje rozhraní API gRPC, která jsou kompatibilní s platformami video Analytics, jako je Live video Analytics na IoT Edge od Microsoftu. 

Aby bylo možné vytvářet složitá, vysoce výkonná řešení živé analýzy videí, je třeba, aby se analýza živých videí v modulu IoT Edge spojila s výkonným modulem odvození, který může využít škálování na hraničních zařízeních. V tomto kurzu se požadavky na odvození odesílají do [rozšíření Intel OpenVINO™ DL načtení proudu ImageList – Edge AI –]()modul Edge, který je navržený tak, aby fungoval s živým analýzou videí v IoT Edge. 

V počáteční verzi tohoto odvozeného serveru máte přístup k následujícím [modelům](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options):

- object_detection pro ![ detekci objektu person_vehicle_bike_detection pro vozidlo](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification pro ![ klasifikaci objektů vehicle_attributes_recognition pro vozidlo](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking pro ![ sledování objektu person_vehicle_bike_tracking pro vozidlo na osobu](./media/use-intel-openvino-tutorial/object-tracking.png)

Pomocí předem načteného zjišťování objektů, klasifikace objektů a kanálů sledování objektů můžete rychle začít. Kromě toho se dodává s předem načtenými modely [Person-vehikula-Crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) a [vozidlo-atributy Recognition-recognition-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md).

> [!NOTE]
> Stažením a použitím modulu Edge: rozšíření OpenVINO™ DL načtení proudu ImageList – Edge AI od společnosti Intel a Zahrnutého softwaru vyjadřujete souhlas s podmínkami a ujednáními v rámci [licenční smlouvy](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Společnost Intel se zavazuje respektovat lidská práva a zabránit complicity v zneužívání lidských práv. Přečtěte si téma [globální zásady lidských práv pro lidská práva společnosti Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkty a software Intel jsou určeny jenom v aplikacích, které nezpůsobují nebo přispívají k porušení mezinárodní uznávané lidského práva.

Můžete použít flexibilitu různých kanálů pro konkrétní případ použití jednoduše změnou proměnných prostředí kanálu v šabloně nasazení. To vám umožní rychle změnit model kanálu a v kombinaci s živým analýzou videí je to v řádu sekund ke změně kanálu médií a odvozeného modelu.  

## <a name="create-and-deploy-the-media-graph"></a>Vytvoření a nasazení mediálního grafu

### <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů

Jako součást požadavků jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte ukázkové soubory.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení hraničního zařízení. Obsahuje některé zástupné hodnoty. Soubor *. env* obsahuje hodnoty pro tyto proměnné.

1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:

    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsna*** seznam operací, které má program spustit.
    * ***Program. cs*** – ukázkový kód programu Tento kód:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md).
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.


1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * V části `GraphTopologyDelete` upravte název:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

1. Klikněte pravým tlačítkem myši na soubor *Src/Edge/deployment.openvino.grpc.cpu.template.jsv* souboru a potom vyberte možnost **generovat manifest nasazení IoT Edge**.

    ![Generovat manifest nasazení IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.openvino.grpc.cpu.amd64.js* v souboru manifestu se vytvoří ve složce *Src/Edge/config* .

> [!NOTE]
> Zahrnuli jsme také *deployment.openvino.grpc.gpu.template.jsdo* šablony, která umožňuje podporu GPU pro modul rozšíření AI Intel OpenVINO DL načtení proudu ImageList-Edge. Tyto šablony odkazují na Image centra Docker od společnosti Intel.

Výše zmíněné šablony odkazují na obrázek Intel Docker Hub. Pokud místo toho chcete hostovat kopii na vlastní Azure Container Registry, můžete postupovat podle kroků 1 a 2 níže:
1. Připojte se přes SSH k zařízení s nainstalovanými nástroji Docker CLI (tj. vaše hraniční zařízení) a zasuňte/zastavte/vložte kontejner pomocí těchto kroků:
    * Načíst Image od společnosti Intel z Docker Hub:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Označit obrázek od společnosti Intel vlastním názvem Azure Container Registry. Nahraďte {YOUR ACR NAME} názvem vaší ACR, který najdete v souboru. env:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Nahrajte označený obrázek do Azure Container Registry:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Nyní je třeba upravit šablony, aby odkazovaly na novou image hostovanou v Azure Container Registry.
    * Klikněte pravým tlačítkem na *deployment.openvino.grpc.cpu.template.js* a přejděte do části modulu *lavExtension* a nahraďte:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        textem:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Opakujte krok 2 pro *deployment.openvino.grpc.gpu.template.jsna*


3. Pokud jste dokončili rychlé zprovoznění [Najít pohyb a generovat události](detect-motion-emit-events-quickstart.md) , tento krok přeskočte. 

    V opačném případě v levém dolním rohu poblíž podokna **Azure IoT Hub** vyberte ikonu **Další akce** a pak vyberte **nastavit IoT Hub připojovací řetězec**. Můžete zkopírovat řetězec z *appsettings.jsv* souboru. Nebo pokud chcete mít jistotu, že jste nakonfigurovali správné centrum IoT v rámci Visual Studio Code, použijte [příkaz vybrat IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Nastavit připojovací řetězec IoT Hub](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Klikněte pravým tlačítkem na *Src/Edge/config/deployment.openvino.grpc.cpu.template.jsna* a vyberte **vytvořit nasazení pro jedno zařízení**. 

    ![Vytvoření nasazení pro jedno zařízení](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Až budete vyzváni k výběru zařízení IoT Hub, vyberte **lva-Sample-Device**.
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:

    * Modul Live video Analytics s názvem **lvaEdge**
    * Modul **rtspsim** , který simuluje Server RTSP a funguje jako zdroj živého kanálu videa
    * Modul **lvaExtension** , což je rozšíření Intel OPENVINO™ DL načtení proudu ImageList – Edge AI 

### <a name="prepare-to-monitor-events"></a>Příprava na monitorování událostí

Klikněte pravým tlačítkem na zařízení Live video Analytics a vyberte **Spustit sledování předdefinovaného koncového bodu události**. Tento krok potřebujete, pokud chcete monitorovat události IoT Hub v okně **výstup** Visual Studio Code. 

![Spustit monitorování](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Spuštění ukázkového programu pro detekci vozidel, osob nebo kol
Pokud otevřete [topologii grafu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) pro tento kurz v prohlížeči, uvidíte, že hodnota `grpcExtensionAddress` byla nastavena na `tcp://lvaExtension:5001` , v porovnání s ukázkou *httpExtensionOpenVINO* nemusíte měnit adresu URL serveru gRPC. Místo toho dáte pokyn, aby modul spouštěl konkrétní kanál pomocí proměnných prostředí, jak je uvedeno výše. Ve výchozí šabloně jsme tuto možnost nastavili na: "object_detection" pro "person_vehicle_bike_detection". Můžete experimentovat s jinými podporovanými kanály. 

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
             "topologyName": "InferencingWithOpenVINOgRPC",
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
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

V této zprávě si všimněte těchto podrobností:

* Zpráva je událost diagnostiky. `MediaSessionEstablished` indikuje, že zdrojový uzel RTSP (předmět) připojený k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V `applicationProperties` nástroji `subject` označuje, že zpráva byla vygenerována ze zdrojového uzlu RTSP v mediálním grafu.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je událost diagnostiky.
* `eventTime`Určuje čas, kdy došlo k události.
* `body`Obsahuje data o události diagnostiky. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Odvozená událost

Uzel procesoru rozšíření gRPC přijímá výsledky odvození z rozšíření Intel OpenVINO™ DL načtení proudu ImageList – Edge AI. Následně vygeneruje výsledky prostřednictvím uzlu IoT Hub jímka jako odvozené události. 

V těchto událostech je typ nastaven na hodnotu, `entity` aby označoval, že se jedná o entitu, jako je například automobil nebo nákladní automobil. `eventTime`Hodnota je čas UTC při zjištění objektu. 

V následujícím příkladu vidíte, že se identifikovalo vozidlo, typ vozidla (van) a barva (bílá), vše s úrovní spolehlivosti nad 0,9. při použití modelu sledování objektů mu také bylo přiřazeno ID k entitě.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
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
Pokud chcete použít jiný model, budete muset změnit šablonu nasazení. Chcete-li přepínat mezi podporovanými modely, můžete změnit proměnné prostředí umístěné v modulu lvaExtenstion. Podporované hodnoty a kombinace pro modely najdete [v tomto dokumentu na GitHubu](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) .

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Zkopírujte šablonu a uložte ji pod novým názvem pro každý možný kanál. Tímto způsobem můžete přepínat mezi modely vytvořením nového nasazení založeného na jedné z těchto šablon.

Po změně proměnných můžete šablonu znovu nasadit do zařízení. Nyní můžete opakovat výše uvedené kroky a znovu spustit ukázkový program s novým kanálem. Výsledky odvození budou podobné (ve schématu), ale zobrazují více nebo méně informací v závislosti na zvoleném modelu kanálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet další rychlé starty nebo kurzy, ponechte prostředky, které jste vytvořili. V opačném případě Azure Portal, přejít do skupin prostředků, vyberte skupinu prostředků, ve které jste spustili tento kurz, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další výzvy pro pokročilé uživatele:

* Místo používání simulátoru RTSP použijte [kameru IP](https://en.wikipedia.org/wiki/IP_camera) , která má podporu pro RTSP. Můžete vyhledat kamery protokolu IP, které podporují protokol RTSP na stránce ONVIF, která je v [souladu](https://www.onvif.org/conformant-products/) s těmito produkty. Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
* Místo virtuálního počítače Azure Linux použijte zařízení s platformou Intel x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge.md). Pak zařízení Zaregistrujte v Azure IoT Hub podle pokynů v tématu [nasazení prvního modulu IoT Edge na zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md).