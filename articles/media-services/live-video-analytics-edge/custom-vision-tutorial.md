---
title: Analýza živého videa pomocí živé analýzy videí v IoT Edge a Azure Custom Vision
description: Naučte se, jak pomocí Custom Vision vytvořit kontejnerový model, který dokáže detekovat nákladní automobil a používat funkci rozšíření AI pro živé video analýzy v IoT Edge (LVA) k nasazení modelu na hraničních zařízeních pro detekci hraček datových toků z živého streamu videa.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: e77521765156a13f0675602ffd0b39f78d8957bb
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016786"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Kurz: Analýza živého videa pomocí živé analýzy videí v IoT Edge a Azure Custom Vision

V tomto kurzu se naučíte, jak pomocí [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) vytvořit kontejnerový model, který dokáže detekovat automobilový zásobník a používat [funkce rozšíření AI](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) pro živé video analýzy v IoT Edge k nasazení modelu na hraničních zařízeních pro detekci hraček z živého streamu videa.

Ukážeme vám, jak spojit sílu Custom Vision – umožňuje komukoli sestavovat a vyvíjet model počítačové vize pouhým nahráváním a popiskem několika imagí, aniž by museli znát data vědy, ML nebo AI a s možnostmi živého videa pro snadnou implementaci vlastního modelu jako kontejneru na hranici a analyzovat simulovaný živý informační kanál. Tento kurz používá jako IoT Edge zařízení virtuální počítač Azure, který je založený na vzorovém kódu napsaném v jazyce C# a sestavuje se v rychlém startu pro [detekci pohybů a generování událostí](detect-motion-emit-events-quickstart.md) .

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Nastavte příslušné prostředky.
> * Vytvoření modelu Custom Vision v cloudu pro detekci hraček a jejich nasazení na hraničních zařízeních
> * Vytvoření a nasazení multimediálního grafu s rozšířením http na vlastní model Vision
> * Spusťte vzorový kód.
> * Prověřte a interpretujte výsledky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Navrhované před čtením  

Než začnete, doporučujeme, abyste si přečetli následující články: 

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Přehled služby Azure Custom Vision](../../cognitive-services/custom-vision-service/overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncepty Media graphu](media-graph-concept.md)
* [Analýza živého videa bez nahrávání videa](analyze-live-video-concept.md)
* [Používání živé analýzy videí pomocí vlastního modelu](use-your-model-quickstart.md)
* [Kurz: vývoj modulu IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Postup úpravy nasazení. * .template.js](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Požadavky

Předpoklady pro tento kurz:

* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a rozšíření [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Může se zobrazit výzva k instalaci Docker. Ignorovat tuto výzvu.
* [Sada SDK .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na vašem vývojovém počítači.
* Ujistěte se, že máte:
    
    * [Nastavení prostředků Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Nastavení vývojového prostředí](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa

V tomto kurzu se k simulaci živého streamu používá soubor [videa pro odvození auta](https://lvamedia.blob.core.windows.net/public/t2.mkv) . Video můžete prošetřit pomocí aplikace, jako je [VLC Media Player](https://www.videolan.org/vlc/). Vyberte CTRL + N a pak vložte odkaz na video o [odvození auta](https://lvamedia.blob.core.windows.net/public/t2.mkv) , kde se má začít přehrávat. Při sledování videa se ve videu zobrazí ve videu Poznámka, 36 že se ve videu objeví nákladní vůz. Vlastní model byl vyškolen k detekci tohoto konkrétního nákladní automobilu. V tomto kurzu budete používat Live video Analytics na IoT Edge k detekci takových hraček a k publikování přidružených událostí odvození do centra IoT Edge.

## <a name="overview"></a>Přehled

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Přehled Custom Vision":::

Tento diagram znázorňuje způsob, jakým se v tomto kurzu Flow signalizují. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server protokolu RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a pošle snímky videa na uzel [procesoru filtru snímkové frekvence](media-graph-concept.md#frame-rate-filter-processor) . Tento procesor omezuje kmitočet snímků streamu videa, který se dorazí na uzel [procesoru rozšíření http](media-graph-concept.md#http-extension-processor) .
Uzel rozšíření HTTP hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes REST do jiného modulu Edge, který spouští model AI za koncovým bodem HTTP. V tomto příkladu je tento hraniční modul modelem automobilového rozpoznávání, sestavený pomocí Custom Vision. Uzel procesoru rozšíření HTTP shromáždí výsledky detekce a publikuje události do uzlu [IoT Hub jímka](media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Sestavení a nasazení modelu detekce Custom Vision hraček 

Jak název Custom Vision navrhuje, můžete ho využít k sestavení vlastního detektoru nebo třídění vlastního objektu v cloudu. Poskytuje jednoduché, snadno použitelné a intuitivní rozhraní k vytváření vlastních modelů visionů, které je možné nasadit v cloudu nebo na hraničních zařízeních prostřednictvím kontejnerů. 

Pro vytvoření rozpoznávání automobilového vozíku doporučujeme, abyste provedli vytváření objektového rozpoznávání pomocí webového portálu v [rychlém startu](../../cognitive-services/custom-vision-service/get-started-build-detector.md) pomocí tohoto vlastního záměru.

Další poznámky:
 
* Pro tento kurz nepoužívejte ukázkové image uvedené v [části požadavky](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)v rychlém startu v článku. Místo toho jsme využili určitou sadu imagí pro sestavování vlastního modelu Vision detektoru hraček. [tyto image](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) doporučujeme použít, když budete požádáni o [Výběr vašich školicích imagí](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) v rychlém startu.
* V části s označením obrázku v rychlém startu nezapomeňte tagovat nákladní automobil, který je vidět na obrázku, pomocí značky – "Delivery nákladní vůz".

Pokud je model připravený podle vaší spokojenosti, můžete ho exportovat do kontejneru Docker pomocí tlačítka exportovat na kartě výkon. Ujistěte se prosím, že jako typ kontejnerové platformy zvolíte Linux. Jedná se o platformu, na které se kontejner spustí. Počítač, na který jste kontejner stáhli, může být buď Windows, nebo Linux. Následující pokyny byly založené na souboru kontejneru staženém na počítač s Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Přehled Custom Vision"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Tento příkaz testuje kontejner v místním počítači a v případě, že má image stejný odměr, jako jsme provedli model, takže výstup by měl vypadat přibližně takto: návrh dodacího vozíku byl zjištěn s pravděpodobností 90,12%.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Kontrola ukázkových souborů

1. V VSCode přejděte do části "src/Edge". Zobrazí se soubor. ENV, který jste vytvořili společně s několika soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení s některými zástupnými hodnotami. Soubor. env obsahuje hodnoty pro tyto proměnné.
1. Pak přejděte do složky src/Cloud-to-Device-Console-App. Tady se zobrazí appsettings.jsv souboru, který jste vytvořili společně s několika dalšími soubory:

    * C2D-Console-App. csproj – Toto je soubor projektu pro VSCode.
    * operations.json – tento soubor zobrazí seznam různých operací, které by měl program spustit.
    * Program.cs – jedná se o vzorový programový kód, který provede následující:

        * Načte nastavení aplikace.
        * Vyvolejte Live video Analytics v přímých způsobech IoT Edge modulu pro vytvoření topologie, vytvoření instance grafu a aktivace grafu.
        * Pozastaví se, abyste prozkoumali výstup grafu v okně terminálu a události odeslané do služby IoT Hub v okně výstup.
        * Deaktivujte instanci grafu, odstraňte instanci grafu a odstraňte topologii grafu.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Generování a nasazení manifestu nasazení

1. V VSCode přejděte do části "src/Cloud-to-Device-Console-App/operations.json"

1. V části GraphTopologySet se ujistěte, že platí následující podmínky:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. V části GraphInstanceSet zajistěte: 
    1. "topologie": "InferencingWithHttpExtension"
    1. Do horní části pole parametrů přidejte následující: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Změňte hodnotu parametru rtspUrl na – "rtsp://rtspsim:554/media/t2.mkv".    
1. V části GraphTopologyDelete zajistěte "název": "InferencingWithHttpExtension"
1. Klikněte pravým tlačítkem na soubor src/Edge/deployment.customvision.template.json a klikněte na **vygenerovat IoT Edge nasazení manifestu**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Přehled Custom Vision" spolu s algoritmem SHA pro bitovou kopii. 
    1. Můžete to také ověřit kontrolou služby Azure Container Registry na Azure Portal. Tady se zobrazí název úložiště společně se značkou. 
1. Kliknutím na ikonu Další akce vedle podokna AZURE IOT HUB v levém dolním rohu nastavte připojovací řetězec IoTHub. Můžete zkopírovat řetězec z appsettings.jsv souboru. (Tady je další doporučený postup, abyste měli jistotu, že máte správnou IoT Hub nakonfigurovanou v VSCode pomocí [příkazu vybrat IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Přehled Custom Vision" a klikněte na **vytvořit nasazení pro jedno zařízení**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Přehled Custom Vision":::
1. Pak budete požádáni o výběr zařízení IoT Hub. V rozevíracím seznamu vyberte lva-Sample-Device.
1. V přibližně 30 sekundách aktualizujte službu Azure IOT hub v levém dolním rohu a měli byste mít hraniční zařízení s nasazenými následujícími moduly:

    * Live video Analytics v modulu IoT Edge s názvem "lvaEdge".
    * Modul s názvem `rtspsim` , který simuluje Server RTSP, který funguje jako zdroj živého informačního kanálu.
    * Modul s názvem `cv` , který je navržen jako název, je model detekce nákladní automobil Custom Vision hraček, který používá vlastní vize na obrázcích a vrací více typů značek. (Náš model byl vyškolený jenom v jedné značce – "Delivery nákladní vůz").

## <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Klikněte pravým tlačítkem na zařízení Live video Analytics a vyberte **Spustit sledování předdefinovaného koncového bodu události**. Tento krok potřebujete, pokud chcete monitorovat události IoT Hub v okně výstup Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Přehled Custom Vision":::

## <a name="run-the-sample-program"></a>Spuštění ukázkového programu

Pokud otevřete topologii grafu pro tento kurz v prohlížeči, uvidíte, že hodnota inferencingUrl byla nastavena na hodnotu http://cv:80/image , což znamená, že odvozený server vrátí výsledky po zjištění hraček nákladní auta, pokud jsou v živém videu.

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Přehled Custom Vision":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Přehled Custom Vision"
              }
            ]
          }
        }
   ```
    
   * Volání GraphInstanceActivate, které spustí instanci grafu a tok videa.
   * Druhé volání GraphInstanceList, které ukazuje, že instance grafu je ve stavu spuštěno.
    
1. Po stisknutí klávesy ENTER se výstup v okně terminálu pozastaví a zobrazí se výzva k pokračování. Ještě nevybírejte ENTER. Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Přepněte do okna výstup v Visual Studio Code. Zobrazí se zprávy, které modul analýza videa v IoT Edge posílá do služby IoT Hub. Následující část tohoto kurzu se zabývá těmito zprávami.
1. Mediální graf bude nadále spouštět a tisknout výsledky. Simulátor RTSP zachovává smyčku zdrojového videa. Chcete-li zastavit graf médií, vraťte se do okna terminálu a vyberte Enter.
Další série volání vyčistí prostředky:
    
   * Volání GraphInstanceDeactivate deaktivuje instanci grafu.
   * Volání GraphInstanceDelete odstraní instanci.
   * Volání GraphTopologyDelete odstraní topologii.
   * Konečné volání GraphTopologyList ukazuje, že seznam je prázdný.
    
## <a name="interpret-the-results"></a>Interpretace výsledků

Když spustíte graf médií, výsledky z uzlu procesor rozšíření HTTP procházejí uzlem IoT Hub jímky do služby IoT Hub. Zprávy, které vidíte v okně výstup, obsahují část tělo a část applicationProperties. Další informace najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách modul Live video Analytics definuje vlastnosti aplikace a obsah těla.

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP, který běží na kontejneru rtspsim-live555. Pokud je připojení úspěšné, bude vytištěna následující událost. Typ události je Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

V této zprávě si všimněte těchto podrobností:

* Zpráva je událost diagnostiky. MediaSessionEstablished označuje, že zdrojový uzel RTSP (předmět) připojený k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V applicationProperties subjekt indikuje, že se zpráva vygenerovala ze zdrojového uzlu RTSP v mediálním grafu.
* V applicationProperties, eventType označuje, že tato událost je událost diagnostiky.
* Čas události označuje čas, kdy došlo k události.
* Tělo obsahuje data o události diagnostiky. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Odvozená událost

Uzel procesoru rozšíření HTTP přijímá výsledky odvození z kontejneru Custom Vision a generuje výsledky prostřednictvím uzlu IoT Hub jímka jako odvozené události.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Všimněte si následujících zpráv:

* Předmět v applicationProperties odkazuje na uzel v MediaGraph, ze kterého byla zpráva generována. V tomto případě zpráva pochází z procesoru rozšíření http.
* Typ eventType v applicationProperties označuje, že se jedná o událost odvození analýzy.
* Čas události označuje čas, kdy došlo k události.
* "tělo" obsahuje data o události analýzy. V tomto případě událost je odvozená událost, takže tělo obsahuje pole odvozených se nazývá "předpovědi".
* oddíl "předpovědi" obsahuje seznam předpovědi, kde je v rámci tohoto rámce nalezen nákladní vůz s příznakem "tag = Delivery nákladní vůz". Stejně jako byste si převolali, doručení je vlastní značkou, kterou jste zadali pro vlastní školený model pro automobilový nákladní vůz, a model je Inferencing a identifikuje se hračka ve vstupním videu s různými výsledky spolehlivosti pravděpodobnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní kurzy nebo rychlé starty, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento kurz, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další výzvy pro pokročilé uživatele:

* Místo používání simulátoru RTSP použijte [kameru IP](https://en.wikipedia.org/wiki/IP_camera) , která má podporu pro RTSP. Můžete vyhledat kamery protokolu IP, které podporují protokol RTSP na stránce ONVIF, která je v [souladu](https://www.onvif.org/conformant-products/) s těmito produkty. Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
* Místo virtuálního počítače Azure Linux použijte zařízení AMD64 nebo x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge-linux.md). 

Pak zařízení Zaregistrujte v Azure IoT Hub podle pokynů v tématu [nasazení prvního modulu IoT Edge na zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md).