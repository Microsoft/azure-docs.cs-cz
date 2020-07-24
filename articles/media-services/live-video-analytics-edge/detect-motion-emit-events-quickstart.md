---
title: Detekce událostí pohybu a vygenerování – Azure
description: V tomto rychlém startu se dozvíte, jak pomocí nástroje Live video Analytics na IoT Edge detekovat události pohybu a vysílat programově voláním přímých metod.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: fca773d0583bee3bef4e7254bcca95866b2205e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091908"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Rychlý Start: detekce událostí pohybu a vygenerování

V tomto rychlém startu se dozvíte, jak začít se službou Live video Analytics na IoT Edge. V tomto článku se používá virtuální počítač Azure jako zařízení IoT Edge a simulovaný živý video stream. Po dokončení kroků nastavení budete moct spustit simulovaný živý datový proud prostřednictvím mediálního grafu, který v tomto datovém proudu detekuje a hlásí jakýkoliv pohyb. Následující diagram znázorňuje grafické znázornění tohoto mediálního grafu.

![Analýza živých videí na základě detekce pohybu](./media/analyze-live-video/motion-detection.png) 

Tento článek je založen na [vzorovém kódu](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) napsaném v jazyce C#.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure, který má aktivní předplatné. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Visual Studio Code](https://code.visualstudio.com/) s následujícími příponami:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Při instalaci rozšíření Azure IoT Tools se může zobrazit výzva k instalaci Docker. Výzvu můžete ignorovat.

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Tento kurz vyžaduje následující prostředky Azure:

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md)

Pro tento rychlý Start doporučujeme, abyste k nasazení požadovaných prostředků ve vašem předplatném Azure použili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Postup je následující:

1. Otevřete [Azure Cloud Shell](https://shell.azure.com).
1. Pokud používáte Cloud Shell poprvé, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů. Vyberte **vytvořit úložiště** a vytvořte účet úložiště pro informace o cloud Shell relaci. Tento účet úložiště je oddělený od účtu, který vytvoří skript pro použití s vaším účtem Azure Media Services.
1. V rozevírací nabídce na levé straně okna Cloud Shell vyberte **bash** jako své prostředí.

    ![Výběr prostředí](./media/quickstarts/env-selector.png)

1. Spusťte následující příkaz.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Pokud se skript úspěšně dokončí, měli byste vidět všechny požadované prostředky v rámci vašeho předplatného.

1. Po dokončení skriptu vyberte složené závorky a vystavte strukturu složek. V adresáři *~/clouddrive/lva-Sample* se zobrazí několik souborů. V tomto rychlém startu jsou důležité tyto:

     * ***~/clouddrive/lva-Sample/Edge-Deployment/.env*** – tento soubor obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code používá tento soubor ke spuštění ukázkového kódu.
     
Tyto soubory budete potřebovat při nastavení vývojového prostředí v Visual Studio Code v další části. Můžete je teď chtít zkopírovat do místního souboru.

 ![Nastavení aplikace](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

1. Klonovat úložiště z tohoto umístění: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. V Visual Studio Code otevřete složku, do které se úložiště stáhlo.
1. V Visual Studio Code přejít do složky *Src/Cloud-to-Device-Console-App* Tam vytvořte soubor a pojmenujte ho *appsettings.js*. Tento soubor bude obsahovat nastavení potřebná ke spuštění programu.
1. Zkopírujte obsah z souboru *~/clouddrive/lva-sample/appsettings.jspro* soubor, který jste dříve vytvořili v tomto rychlém startu.

    Text by měl vypadat jako následující výstup.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Přejít do složky *Src/Edge* a vytvořit soubor s názvem *. env*.
1. Zkopírujte obsah souboru */clouddrive/lva-Sample/Edge-Deployment/.env* . Text by měl vypadat jako následující kód.

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
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Kontrola ukázkových souborů

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení, kde jsou proměnné použity pro některé vlastnosti. Soubor *. env* obsahuje hodnoty pro tyto proměnné.
1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:

    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsna*** seznam operací, které má program spustit.
    * ***Program.cs*** – vzorový programový kód. Tento kód:
    
      * Načte nastavení aplikace.
      * Vyvolá přímé metody, které jsou zpřístupněny v rámci analýzy živých videí v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md).
      * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
      * Vyvolá přímé metody pro vyčištění prostředků.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generování a nasazení manifestu nasazení

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení. Definuje také nastavení konfigurace pro tyto moduly. 

Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

1. Otevřete nástroj Visual Studio Code.
1. Vedle podokna **Azure IoT Hub** vyberte ikonu **Další akce** a nastavte připojovací řetězec IoT Hub. Můžete zkopírovat řetězec z *Src/Cloud-to-Device-Console-App/appsettings.jsv* souboru. 

    ![Nastavení připojovacího řetězce IOT](./media/quickstarts/set-iotconnection-string.png)

1. Klikněte pravým tlačítkem na **Src/Edge/deployment.template.jsna** a vyberte **generovat manifest nasazení IoT Edge**.

    ![Vygenerovat manifest nasazení IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Tato akce by měla vytvořit soubor manifestu s názvem *deployment.amd64.js* ve složce *Src/Edge/config* .
1. Klikněte pravým tlačítkem na **Src/Edge/config/deployment.amd64.jsna**, vyberte **vytvořit nasazení pro jedno zařízení**a pak vyberte název hraničního zařízení.

    ![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)

1. Až se zobrazí výzva k výběru zařízení IoT Hub, z rozevírací nabídky zvolte **lva-Sample-Device** .
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:

    * Live video Analytics na IoT Edge (název modulu `lvaEdge` )
    * Simulátor protokolu RTSP (Real-time streaming Protocol) (název modulu `rtspsim` )

Modul simulátoru RTSP simuluje živý Stream videa pomocí videosouboru, který jste zkopírovali do hraničního zařízení, když jste spustili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

V této fázi jsou moduly nasazeny, ale nejsou aktivní žádné mediální grafy.

## <a name="prepare-to-monitor-events"></a>Příprava na monitorování událostí

Pomocí nástroje Live video Analytics v IoT Edge zjistíte pohyb příchozích datových proudů v reálném videu a odešlete události do IoT Hub. Chcete-li zobrazit tyto události, postupujte podle následujících kroků:

1. Otevřete podokno Průzkumník v Visual Studio Code a vyhledejte Azure IoT Hub v levém dolním rohu.
1. Rozbalte uzel **zařízení** .
1. Klikněte pravým tlačítkem na **lva-Sample-Device** a vyberte **Spustit sledování předdefinovaného koncového bodu události**.

    ![Začít monitorovat vestavěný koncový bod události](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Spuštění ukázkového programu

Pomocí těchto kroků spusťte vzorový kód:

1. V Visual Studio Code v nástroji použijte *Src/Cloud-to-Device-Console-App/operations.js*.
1. V uzlu **GraphTopologySet** se ujistěte, že vidíte následující hodnotu:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. V uzlech **GraphInstanceSet** a **GraphTopologyDelete** zajistěte, aby hodnota `topologyName` odpovídala hodnotě `name` vlastnosti v topologii grafu:

    `"topologyName" : "MotionDetection"`
    
1. Spusťte ladicí relaci, a to tak, že vyberete klávesu F5. V okně **terminálu** se zobrazí některé zprávy.
1. *operations.jsv* souboru se spouští s voláními `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po dokončení předchozích rychlých startů, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Chcete-li pokračovat, vyberte klávesu ENTER.

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
     
     * Volání `GraphTopologySet` , které používá předchozí`topologyUrl`
     * Volání `GraphInstanceSet` , které používá následující tělo:
     
         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "MotionDetection",
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
     
     * Volání `GraphInstanceActivate` , které spustí instanci grafu a tok videa
     * Druhé volání, které `GraphInstanceList` ukazuje, že instance grafu je ve stavu spuštěno
1. Výstup v okně **terminálu** pozastaví `Press Enter to continue` . Ještě nevybírejte ENTER. Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Přepněte do okna **výstup** v Visual Studio Code. Zobrazí se zprávy, které modul analýza videa v IoT Edge posílá do služby IoT Hub. Následující část tohoto rychlého startu popisuje tyto zprávy.
1. Mediální graf bude nadále spouštět a tisknout výsledky. Simulátor RTSP zachovává smyčku zdrojového videa. Chcete-li zastavit graf médií, vraťte se do okna **terminálu** a vyberte Enter. 

    Další série volání vyčistí prostředky:
     * Volání `GraphInstanceDeactivate` deaktivuje instanci grafu.
     * Volání `GraphInstanceDelete` Odstraní instanci.
     * Volání pro `GraphTopologyDelete` odstranění topologie.
     * Konečné volání k `GraphTopologyList` ukáže, že seznam je prázdný.

## <a name="interpret-results"></a>Interpretace výsledků

Když spustíte graf médií, výsledky z uzlu procesoru snímače pohybu procházejí uzlem IoT Hub jímky do služby IoT Hub. Zprávy, které vidíte v okně **výstup** Visual Studio Code obsahují `body` oddíl a `applicationProperties` oddíl. Další informace najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách modul Live video Analytics definuje vlastnosti aplikace a obsah těla.

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP, který běží na kontejneru rtspsim-live555. Pokud je připojení úspěšné, bude vytištěna následující událost.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

V předchozím výstupu: 
* Zpráva je událost diagnostiky, `MediaSessionEstablished` . Indikuje, že zdrojový uzel RTSP (předmět) připojený k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V `applicationProperties` aplikaci `subject` odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je událost diagnostiky.
* `eventTime`Hodnota označuje čas, kdy došlo k události.
* `body`Oddíl obsahuje data o diagnostickou událost. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


### <a name="motiondetection-event"></a>Událost MotionDetection

Když se zjistí pohyb, Live video Analytics v modulu IoT Edge odešle událost odvození. `type`Je nastavena na hodnotu `motion` , která označuje, že se jedná o výsledek procesoru detekce pohybu. `eventTime`Hodnota vás upozorní, když (v UTC) k pohybu došlo. 

Tady je příklad této zprávy:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

V tomto příkladu: 

* V `applicationProperties` aplikaci `subject` odkazuje na uzel v grafu médií, ze kterého byla zpráva generována. V tomto případě zpráva pochází z uzlu procesoru detekce pohybu.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je analytická událost.
* `eventTime`Hodnota je čas, kdy došlo k události.
* `body`Hodnota je data o události analýzy. V tomto případě událost je odvozená událost, takže tělo obsahuje `timestamp` `inferences` data a.
* `inferences`Data označují, že `type` je `motion` . Obsahuje další data o této `motion` události.
* `box`Oddíl obsahuje souřadnice ohraničujícího rámečku kolem objektu přesunutí. Hodnoty jsou normalizovány šířkou a výškou videa (v pixelech). Například šířka je 1920 a výška je 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, měli byste zachovat prostředky, které jste vytvořili. V opačném případě v Azure Portal do skupin prostředků vyberte skupinu prostředků, ve které jste spustili tento rychlý Start, a pak odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

Spusťte další rychlé starty, jako je detekce objektu v živém informačním kanálu videa.        
