---
title: Analýza živého videa pomocí Počítačové zpracování obrazu pro prostorovou analýzu – Azure
description: V tomto kurzu se dozvíte, jak pomocí živé analýzy videí společně s funkcí Počítačové zpracování obrazu prostorová analýza AI z Azure Cognitive Services analyzovat živý kanál videa z (simulované) kamery IP.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: b20cdb9bbc64f9adc49cfbde4ff80576b149dbcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698752"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analýza živého videa pomocí Počítačové zpracování obrazu pro prostorovou analýzu (Preview)

V tomto kurzu se dozvíte, jak používat Live video Analytics společně s [počítačové zpracování obrazu pro službu prostorových analýz AI z Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) k analýze živého kanálu videa z (simulované) kamery IP. Uvidíte, jak tento server odvození vám umožní analyzovat video streamování pro pochopení prostorových vztahů mezi lidmi a pohyby na fyzickém místě.  Do tohoto odvozeného serveru se pošle podmnožina snímků ve videu a výsledky se odešlou do centra IoT Edge a když se splní nějaké podmínky, videoklipy se zaznamenávají a ukládají jako Azure Media Services prostředky.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> * Nastavte prostředky.
> * Projděte si kód.
> * Spusťte vzorový kód.
> * Monitorování událostí.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Budete potřebovat předplatné Azure s oprávněním pro vytváření instančních objektů (Tato **role vlastníka** poskytuje). Pokud nemáte správná oprávnění, obraťte se na správce účtu, abyste vám udělili správná oprávnění. 
## <a name="suggested-pre-reading"></a>Navrhované před čtením

Než začnete, přečtěte si tyto články:

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncepty Media graphu](media-graph-concept.md)
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)
* [Kurz: vývoj modulu IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Nasazení Live video Analytics na Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Předpoklady

Níže jsou uvedené požadavky pro připojení modulu pro prostorovou analýzu do nástroje Live video Analytics.

* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači. Ujistěte se, že máte [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Ujistěte se, že je síť, ke které je připojený váš vývojový počítač, povolená prostřednictvím portu 5671 rozšířený protokol front zpráv. Tato instalace umožňuje, aby nástroje Azure IoT komunikovaly se službou Azure IoT Hub.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) s AKCELERACí GPU  
    Doporučujeme, abyste používali Azure Stack Edge s akcelerací GPU, ale kontejner běží na jakémkoli jiném zařízení s [grafickým procesorem NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Služba rozpoznávání Azure počítačové zpracování obrazu kontejner](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pro prostorovou analýzu.  
    Aby bylo možné použít tento kontejner, je nutné mít Počítačové zpracování obrazu prostředek pro získání asociovaného **klíče rozhraní API** a **identifikátoru URI koncového bodu**. Klíč rozhraní API je k dispozici na stránkách Azure Portal Počítačové zpracování obrazu přehledu a klíčů. Klíč a koncový bod jsou požadovány ke spuštění kontejneru.

## <a name="overview"></a>Přehled

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Přehled prostorové analýzy":::
 
Tento diagram znázorňuje způsob, jakým se v tomto kurzu Flow signalizují. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server protokolu RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa na `MediaGraphCognitiveServicesVisionExtension` uzel procesoru.

Uzel MediaGraphCognitiveServicesVisionExtension hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes **sdílenou paměť** do jiného modulu Edge, který spouští operace AI za koncovým bodem gRPC. V tomto příkladu je tento modul Edge modul pro prostorovou analýzu. Uzel procesoru MediaGraphCognitiveServicesVisionExtension má dvě věci:

* Shromáždí výsledky a publikuje události do uzlu [IoT Hub jímka](media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Zachytí také 30 sekundový klip ze zdroje RTSP pomocí [procesoru brány signálu](media-graph-concept.md#signal-gate-processor) a uloží jej jako Media Services prostředek.

## <a name="create-the-computer-vision-resource"></a>Vytvoření prostředku Počítačové zpracování obrazu

Je potřeba vytvořit prostředek Azure typu Počítačové zpracování obrazu buď v [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) nebo prostřednictvím rozhraní příkazového řádku Azure. Až se vaše žádost o přístup ke kontejneru schválí a vaše ID předplatného Azure je zaregistrované, budete moct prostředek vytvořit. Pokud chcete https://aka.ms/csgate Odeslat svůj případ použití a ID vašeho předplatného Azure, klikněte na Přejít.  Prostředek Azure je potřeba vytvořit pomocí stejného předplatného Azure, které je k dispozici na formuláři žádosti o přístup.

### <a name="gathering-required-parameters"></a>Shromažďují se požadované parametry.

Existují tři primární parametry pro všechny požadované kontejnery Cognitive Services, včetně kontejneru prostorových analýz. Licenční smlouvu s koncovým uživatelem (EULA) se musí nacházet s hodnotou přijmout. Kromě toho je potřeba i identifikátor URI koncového bodu i klíč rozhraní API.

### <a name="keys-and-endpoint-uri"></a>Klíče a identifikátor URI koncového bodu

Klíč se používá ke spuštění kontejneru prostorové analýzy a je k dispozici na `Keys and Endpoint` stránce Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na tuto stránku a vyhledejte klíče a identifikátor URI koncového bodu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Identifikátor URI koncového bodu":::

## <a name="set-up-azure-stack-edge"></a>Nastavení Azure Stack Edge

Postupujte podle [těchto kroků](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) a nastavte Azure Stack Edge a pokračujte podle následujících kroků a nasaďte živé video analýzy a moduly prostorové analýzy.

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

1. Klonovat úložiště z tohoto umístění: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. V Visual Studio Code otevřete složku, do které se úložiště stáhlo.
1. V Visual Studio Code přejít do složky src/Cloud-to-Device-Console-App Tam vytvořte soubor a pojmenujte ho appsettings.js. Tento soubor bude obsahovat nastavení potřebná ke spuštění programu.
1. Pomocí následujících kroků Získejte IotHubConnectionString z Azure Stack Edge:

    * Přejděte na IoT Hub v Azure Portal a `Shared access policies` v levém navigačním podokně klikněte na.
    * Klikněte na `iothubowner` načíst sdílené přístupové klíče.
    * Zkopírujte  `Connection String – primary key` a vložte ho do vstupního pole na VSCode.
    
        Připojovací řetězec bude vypadat takto: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Zkopírujte níže uvedený obsah do souboru. Ujistěte se, že jste proměnné nahradili.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Přejít do složky src/Edge a vytvořit soubor s názvem. env.
1. Zkopírujte obsah souboru/clouddrive/lva-Sample/Edge-Deployment/.env. Text by měl vypadat jako následující kód.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Nastavení šablony nasazení  

Vyhledejte soubor nasazení v/src/Edge/deployment.spatialAnalysis.template.jsna. Šablona obsahuje modul lvaEdge, modul rtspsim a náš modul pro prostorovou analýzu.

V souboru šablony nasazení je třeba věnovat pár věcí:

1. Nastavte vazbu portu.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` v modulu lvaEdge a prostorové analýzy createOptions by měl být stejný a nastavený na Host.
1. Aby simulátor RTSP fungoval, ujistěte se, že jste nastavili rozsahy svazků. Další informace najdete v tématu [nastavení připojení svazku Docker](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Připojte se ke sdílené složce SMB](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) a zkopírujte [ukázkový videosoubor Bulldozer](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) do místní sdílené složky.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Podívejte se, že modul rtspsim má následující konfiguraci:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generování a nasazení manifestu nasazení

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení. Definuje také nastavení konfigurace pro tyto moduly.

Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

1. Otevřete Visual Studio Code.
1. Vedle podokna AZURE IOT HUB vyberte ikonu Další akce a nastavte připojovací řetězec IoT Hub. Můžete zkopírovat řetězec ze `src/cloud-to-device-console-app/appsettings.json` souboru.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Prostorová analýza: připojovací řetězec":::
1. Klikněte pravým tlačítkem `src/edge/deployment.spatialAnalysis.template.json` a vyberte generovat IoT Edge manifest nasazení.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Prostorová analýza: nasazení amd64 JSON":::
    
    Tato akce by měla vytvořit soubor manifestu s názvem deployment.amd64.jsve složce src/Edge/config.
1. Klikněte pravým tlačítkem myši `src/edge/config/deployment.spatialAnalysis.amd64.json` , vyberte vytvořit nasazení pro jedno zařízení a pak vyberte název hraničního zařízení.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Prostorová analýza: JSON šablony nasazení":::   
1. Až se zobrazí výzva k výběru zařízení IoT Hub, zvolte název Azure Stack Edge z rozevírací nabídky.
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:
    
    * Live video Analytics na IoT Edge (název modulu lvaEdge).
    * Simulátor protokolu RTSP (Real-Time streaming Protocol) (název modulu rtspsim).
    * Prostorová analýza (název modulu spatialAnalysis).
    
V případě úspěšného nasazení bude ve výstupu vypadat zpráva:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Pak můžete najít `lvaEdge` moduly, `rtspsim` `spatialAnalysis` a `rtspsim` v části zařízení/moduly a jejich stav musí být spuštěno.

## <a name="prepare-to-monitor-events"></a>Příprava na monitorování událostí

Chcete-li zobrazit tyto události, postupujte podle následujících kroků:

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::
1. Otevřete podokno Průzkumník a vyhledejte Azure IoT Hub v levém dolním rohu.
1. Rozbalte uzel zařízení.
1. Pravým tlačítkem myši klikněte na Azure Stack Edge a vyberte spustit sledování integrovaný koncový bod události.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Prostorová analýza: spustit monitorování":::
     
## <a name="run-the-program"></a>Spuštění programu

Existuje program. cs, který vyvolá přímé metody v src/Cloud-to-Device-Console-App/operations.jsna. Musíme nastavit operations.jsna a poskytnout topologii pro použití pomocí Media graphu.  

V operations.js:

* Nastavte topologii takto:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Vytvořte například instanci grafu, nastavte parametry v topologii zde:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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
},
```

>[!Note]
Podívejte se na použití MediaGraphRealTimeComputerVisionExtension pro připojení k modulu prostorové analýzy. Nastavte $ {grpcUrl} na **TCP://spatialAnalysis: <PORT_NUMBER>**, například TCP://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Spusťte ladicí relaci a postupujte podle pokynů pro **terminál** . nastaví se topologie, nastaví se instance grafu, aktivuje se instance grafu a nakonec se odstraní prostředky.

## <a name="interpret-results"></a>Interpretace výsledků

Když se vytvoří instance mediálního grafu, měla by se zobrazit událost MediaSessionEstablished, kterou najdete v [ukázkové události MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Modul prostorové analýzy také pošle události AI Insight do živé analýzy videí a následně na IoTHub, zobrazí se také ve **výstupu**. ENTITA je rozpoznána objekty a událost je spaceanalytics události. Tento výstup se předává do živé analýzy videí.

Ukázkový výstup pro personZoneEvent (operace cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Další kroky

Vyzkoušejte různé operace, které `spatialAnalysis` modul nabízí jako **PersonCount** a **personDistance** , přepnutím příznaku Enabled v uzlu grafu souboru manifestu nasazení.
>[!Tip]
> Použijte [vzorový videosoubor](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) , který má v rámci rámce více než jednu osobu.

> [!NOTE]
> V jednom okamžiku můžete spustit pouze jednu operaci. Ujistěte se prosím, že je nastavená hodnota pouze jeden příznak na **hodnotu true** a ostatní jsou nastaveny na **hodnotu NEPRAVDA**.