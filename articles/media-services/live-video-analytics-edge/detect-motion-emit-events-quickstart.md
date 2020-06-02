---
title: Detekce událostí pohybu a vygenerování – Azure
description: V tomto rychlém startu se dozvíte, jak pomocí nástroje Live video Analytics na IoT Edge detekovat události pohybu a vysílat programově voláním přímých metod.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262024"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Rychlý Start: detekce událostí pohybu a vygenerování

V tomto rychlém startu se dozvíte, jak začít se službou Live video Analytics na IoT Edge. Používá virtuální počítač Azure jako zařízení IoT Edge a simulovaný Stream živého videa. Po dokončení kroků nastavení budete moct spustit simulovaný živý datový proud prostřednictvím mediálního grafu, který v tomto datovém proudu detekuje a hlásí jakýkoliv pohyb. Následující diagram znázorňuje grafické znázornění tohoto mediálního grafu.

![Analýza živých videí na základě detekce pohybu](./media/analyze-live-video/motion-detection.png) 

Tento článek je založen na [vzorovém kódu](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) napsaném v jazyce C#.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na počítači s následujícími příponami:
    1. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* V systému je nainstalovaná [sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) .

> [!TIP]
> Při instalaci rozšíření Azure IoT Tools se může zobrazit výzva k instalaci Docker. Klidně ho ignorujte.

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

V tomto kurzu jsou vyžadovány následující prostředky Azure.

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

Pro tento rychlý Start doporučujeme použít [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) k nasazení prostředků Azure uvedených výše v předplatném Azure. Postupujte tímto způsobem:

1. Přejděte na https://shell.azure.com.
1. Pokud Cloud Shell používáte poprvé, zobrazí se výzva k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů. Výběrem možnosti vytvořit úložiště vytvořte účet úložiště pro ukládání informací o Cloud Shellch relacích. Tento účet úložiště je oddělený od toho, který skript vytvoří pro použití s vaším účtem Azure Media Services.
1. V rozevíracím seznamu na levé straně okna prostředí vyberte "bash" jako své prostředí.

    ![Výběr prostředí](./media/quickstarts/env-selector.png)

1. Spusťte následující příkaz

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Pokud se skript úspěšně dokončí, měli byste vidět všechny výše uvedené prostředky v rámci vašeho předplatného.

1. Po dokončení skriptu klikněte na složenou závorku a vystavte strukturu složky. Zobrazí se několik souborů vytvořených v adresáři ~/clouddrive/lva-Sample. V tomto rychlém startu jsou důležité tyto:

     * ~/clouddrive/lva-Sample/Edge-Deployment/.env – obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
     * ~/clouddrive/lva-Sample/AppSetting.JSON – používá se Visual Studio Code pro spuštění ukázkového kódu
     
Budete je potřebovat k aktualizaci souborů v Visual Studio Code později v rychlém startu. Můžete je teď chtít zkopírovat do místního souboru.


 ![Nastavení aplikace](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

1. Naklonujte úložiště sem https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Spusťte Visual Studio Code a otevřete složku, do které se úložiště stáhlo.
1. V Visual Studio Code přejděte do složky src/Cloud-to-Device-Console-App a vytvořte soubor s názvem appSettings. JSON. Tento soubor bude obsahovat nastavení potřebná ke spuštění programu.
1. Kopírovat obsah ze souboru ~/clouddrive/lva-Sample/appSettings.JSON vygenerovaného v předchozí části (viz krok 5)

    Text by měl vypadat takto:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Potom přejděte do složky "src/Edge" a vytvořte soubor s názvem ". env".
1. Zkopírujte obsah ze souboru/clouddrive/lva-Sample/Edge-Deployment/.env. Text by měl vypadat takto:

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

1. V Visual Studio Code přejděte na "src/Edge". Zobrazí se soubor. ENV, který jste vytvořili společně s několika soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení s některými zástupnými hodnotami. Soubor. env obsahuje hodnoty pro tyto proměnné.
1. Pak přejděte do složky src/Cloud-to-Device-Console-App. Tady se zobrazí soubor appSettings. JSON, který jste vytvořili spolu s několika dalšími soubory:

    * C2D-Console-App. csproj – soubor projektu pro Visual Studio Code.
    * Operations. JSON – tento soubor obsahuje seznam různých operací, které byste chtěli spustit.
    * Program.cs – vzorový programový kód, který provede následující:
    
        * Načte nastavení aplikace.
        * Vyvolá přímé metody vystavené živým analýzou videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md) . 
        * Pozastaví se, abyste prozkoumali výstup programu v okně terminálu a události generované modulem v okně výstup.
        * Vyvolá přímé metody pro vyčištění prostředků.   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení, a nastavení konfigurace pro tyto moduly. Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

1. Otevřít Visual Studio Code
1. Kliknutím na ikonu Další akce vedle podokna AZURE IOT HUB v levém dolním rohu nastavte připojovací řetězec IoTHub. Můžete zkopírovat řetězec ze souboru src/Cloud-to-Device-Console-App/appSettings. JSON. 

    ![Nastavení připojovacího řetězce IOT](./media/quickstarts/set-iotconnection-string.png)
1. Potom klikněte pravým tlačítkem na soubor src/Edge/Deployment. template. JSON a klikněte na vygenerovat IoT Edge manifest nasazení.
    ![Generovat manifest nasazení IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    To by mělo vytvořit soubor manifestu ve složce src/Edge/config s názvem "Deployment. amd64. JSON".
1. Klikněte pravým tlačítkem na "src/Edge/config/Deployment. amd64. JSON" a klikněte na "vytvořit nasazení pro jedno zařízení" a vyberte název hraničního zařízení.

    ![Vytvoření nasazení pro jedno zařízení](./media/quickstarts/create-deployment-single-device.png)
1. Zobrazí se výzva k výběru zařízení IoT Hub. V rozevíracím seznamu vyberte lva-Sample-Device.
1. V přibližně 30 sekundách aktualizujte službu Azure IOT hub v levém dolním rohu a měli byste vidět, že hraniční zařízení má nasazené následující moduly:

    * Live video Analytics na IoT Edge (název modulu "lvaEdge")
    * Simulátor RTSP (název modulu "rtspsim")

Modul simulátoru RTSP simuluje živý Stream videa pomocí videosouboru uloženého ve formátu videa, který jste zkopírovali do vašeho hraničního zařízení, když jste spustili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). V této fázi máte nasazené moduly, ale nejsou aktivní žádné mediální grafy.

## <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Pomocí nástroje Live video Analytics v IoT Edge můžete detekovat pohyb v příchozím streamu živého videa a posílat události do IoT Hub. Chcete-li zobrazit tyto události, postupujte podle následujících kroků:

1. Otevřete podokno Průzkumník v Visual Studio Code a vyhledejte Azure IoT Hub v levém dolním rohu.
1. Rozbalte uzel zařízení.
1. Pravým tlačítkem clink na lva-Sample-Device a zvolte možnost spustit monitorování integrovaného monitorování událostí.

    ![Spustit sledování vestavěného koncového bodu události](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Spuštění ukázkového programu

Pomocí následujícího postupu spusťte vzorový kód.
1. V Visual Studio Code přejděte na "src/Cloud-to-Device-Console-App/Operations. JSON".
1. V rámci uzlu GraphTopologySet zajistěte následující:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. V dalším kroku se v uzlech GraphInstanceSet a GraphTopologyDelete ujistěte, že hodnota vlastnosti Topology odpovídá hodnotě vlastnosti Name ve výše uvedené topologii grafu:

    `"topologyName" : "MotionDetection"`
    
1. Spusťte ladicí relaci (stiskněte klávesu F5). Začnete zobrazovat některé zprávy, které se vytisknou v okně terminálu.
1. Operace Operations. JSON se spustí s voláními GraphTopologyList a GraphInstanceList. Pokud jste vyčistili prostředky po předchozích rychlých startech, vrátí tato akce prázdné seznamy a potom se pozastaví, až zadáte ENTER.

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
     
     * Volání GraphInstanceActivate ke spuštění instance grafu a spuštění toku videa.
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

* Zpráva je diagnostická událost MediaSessionEstablished, která indikuje, že uzel zdroje RTSP (předmět) byl schopný navázat spojení s simulátorem RTSP a zahájit příjem (simulovaného) živého kanálu.
* "Subject" v applicationProperties odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o diagnostické události, což je v tomto případě podrobnosti o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="motion-detection-event"></a>Událost detekce pohybu

Když se zjistí pohyb, modul Edge (Live video Analytics Edge) pošle odvozenou událost. Typ je nastaven na "pohyb", aby označoval, že se jedná o výsledek procesoru detekce pohybu, a čas události oznamuje, v jakém čase (UTC) došlo k pohybu. Níže je příklad:

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

* "Subject" v applicationProperties odkazuje na uzel v grafu médií, ze kterého byla zpráva vygenerována. V tomto případě zpráva pochází z uzlu procesoru detekce pohybu.
* eventType v applicationProperties značí, že se jedná o analytickou událost.
* "čas události" označuje čas, kdy došlo k události.
"tělo" obsahuje data o události analýzy. V tomto případě událost je odvozená událost, takže tělo obsahuje data "časové razítko" a "odvození".
* "odvození" data označují, že "typ" je "pohyb" a obsahuje další data o této události "pohybu".
* oddíl Box obsahuje souřadnice ohraničujícího rámečku kolem objektu přesunutí. Hodnoty jsou normalizovány šířkou a výškou videa v pixelech (např. Šířka 1920 a výška 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento rychlý Start, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

Spusťte další rychlé starty, jako je detekce objektu v živém informačním kanálu videa.        
