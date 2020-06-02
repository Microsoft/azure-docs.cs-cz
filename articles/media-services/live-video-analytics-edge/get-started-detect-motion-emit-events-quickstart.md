---
title: Začínáme se službou Live video Analytics v IoT Edge – Azure
description: V tomto rychlém startu se dozvíte, jak začít pracovat se službou Live video Analytics na IoT Edge a detekovat pohyb v živém streamu videa.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262012"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Rychlý Start: Začínáme – Live video Analytics na IoT Edge

V tomto rychlém startu se dozvíte, jak začít se službou Live video Analytics na IoT Edge. Používá virtuální počítač Azure jako zařízení IoT Edge a simulovaný Stream živého videa. Po dokončení kroků nastavení budete moct spustit simulovaný živý datový proud prostřednictvím mediálního grafu, který v tomto datovém proudu detekuje a hlásí jakýkoliv pohyb. Následující diagram znázorňuje grafické znázornění tohoto mediálního grafu.

![Analýza živých videí na základě detekce pohybu](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači pomocí [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Síť, ke které je připojený počítač pro vývoj, by měla povolit protokol AMQP přes port 5671 (aby mohli nástroje Azure IoT komunikovat s Azure IoT Hub).

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
    
Pokud se skript úspěšně dokončí, měli byste vidět všechny výše uvedené prostředky v rámci vašeho předplatného. V rámci výstupu skriptu se vygeneruje tabulka prostředků, ve které se zobrazí seznam názvů IoT Hub. Vyhledejte typ prostředku **Microsoft. Devices/IotHubs**a poznamenejte si jeho název. Budete ho potřebovat v dalším kroku. Skript také vygeneruje několik konfiguračních souborů v adresáři ~/clouddrive/lva-Sample/. budete je potřebovat později v rychlém startu.

## <a name="deploy-modules-on-your-edge-device"></a>Nasazení modulů na hraničním zařízení

Spusťte následující příkaz z Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Výše uvedený příkaz nasadí do hraničního zařízení následující moduly (virtuální počítač Linux):

* Live video Analytics na IoT Edge (název modulu "lvaEdge")
* Simulátor RTSP (název modulu "rtspsim")

Modul simulátoru RTSP simuluje živý Stream videa pomocí videosouboru uloženého ve formátu videa, který jste zkopírovali do vašeho hraničního zařízení, když jste spustili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). V této fázi máte nasazené moduly, ale nejsou aktivní žádné mediální grafy.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Konfigurace rozšíření nástrojů Azure IoT Tools v Visual Studio Code

Spusťte Visual Studio Code a podle níže uvedených pokynů se připojte k Azure IoT Hub pomocí rozšíření Azure IoT Tools.

1. Pomocí **View**  >  **Průzkumníka** zobrazení nebo pouhým stisknutím klávesy CTRL + SHIFT + E přejděte na kartu Průzkumník v Visual Studio Code.
1. Na kartě Průzkumník klikněte v levém dolním rohu na možnost "Azure IoT Hub".
1. Kliknutím na ikonu Další možnosti zobrazte kontextovou nabídku a vyberte možnost nastavit připojovací řetězec IoT Hub.
1. Zobrazí se vstupní pole a potom zadejte připojovací řetězec IoT Hub. Připojovací řetězec pro IoT Hub můžete získat z ~/clouddrive/lva-Sample/appSettings.JSON v Cloud Shell.
1. Pokud bude připojení úspěšné, zobrazí se seznam hraničních zařízení. Mělo by existovat aspoň jedno zařízení s názvem "lva-Sample-Device".
1. Teď můžete spravovat zařízení IoT Edge a pracovat s Azure IoT Hub prostřednictvím kontextové nabídky.
1. Moduly nasazené na hraničním zařízení můžete zobrazit rozbalením uzlu moduly v části "lva-Sample-Device".

    ![lva – uzel Sample-Device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Použití přímých metod

Pomocí modulu můžete analyzovat živé streamy videa vyvoláním přímých metod. Přečtěte si [přímé metody pro Live video Analytics na IoT Edge](direct-methods.md) , abyste pochopili všechny přímé metody poskytované modulem. 

### <a name="invoke-graphtopologylist"></a>Vyvolat GraphTopologyList
Tím se vytvoří výčet všech [topologií grafu](media-graph-concept.md#media-graph-topologies-and-instances) v modulu.

1. Klikněte pravým tlačítkem na modul lvaEdge a v místní nabídce vyberte vyvolat modul Direct Module.
1. V horní části okna Visual Studio Code se zobrazí okno pro úpravu. Do pole pro úpravy zadejte "GraphTopologyList" a stiskněte klávesu ENTER.
1. Potom zkopírujte a vložte níže uvedenou datovou část JSON do pole pro úpravy a stiskněte klávesu ENTER.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Během několika sekund se zobrazí okno výstup v Visual Studio Code automaticky otevírané okno s následující odpovědí.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Výše uvedená odpověď se očekává, že se nevytvořily žádné topologie grafů.
    

### <a name="invoke-graphtopologyset"></a>Vyvolat GraphTopologySet

Pomocí stejných kroků, jako jsou ty, které jsou popsané pro vyvolání GraphTopologyList, můžete vyvolat GraphTopologySet pro nastavení [topologie grafu](media-graph-concept.md#media-graph-topologies-and-instances) pomocí následujícího JSON jako datové části.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


Výsledkem výše uvedené datové části JSON je vytvoření topologie grafu, která definuje tři parametry (dva z nich mají výchozí hodnoty). Topologie má jeden uzel zdroj (zdroj RTSP), uzel jeden procesor (procesor detekce pohybu) a jeden uzel jímky (IoT Hub jímka).

Během několika sekund se v okně výstup zobrazí následující odpověď:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Vrácený stav je 201, což značí, že se vytvořila nová topologie. Jako další kroky zkuste použít následující:

* Znovu zavolejte GraphTopologySet a Všimněte si, že vrácený kód stavu je 200. Stavový kód 200 označuje, že existující topologie byla úspěšně aktualizována.
* Znovu vyvolat GraphTopologySet, ale změňte řetězec popisu. Všimněte si, že stavový kód v odpovědi je 200 a popis se aktualizuje na novou hodnotu.
* Vyvolejte GraphTopologyList, jak je uvedeno v předchozí části, a Všimněte si, že teď můžete ve vrácené datové části Zobrazit topologii "MotionDetection".

### <a name="invoke-graphtopologyget"></a>Vyvolat GraphTopologyGet

Nyní volejte GraphTopologyGet s následující datovou částí

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Během několika sekund by se měla zobrazit následující odpověď v okně výstup:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Všimněte si následujícího v datové části odpovědi:

* Stavový kód je 200, což znamená úspěch.
* Datová část obsahuje časové razítko "vytvořeno" a "lastModified".

### <a name="invoke-graphinstanceset"></a>Vyvolat GraphInstanceSet

Dále vytvořte instanci grafu, která odkazuje na výše uvedenou topologii grafu. Jak je vysvětleno [zde](media-graph-concept.md#media-graph-topologies-and-instances), instance grafů umožňují analyzovat živé video streamy z mnoha kamer se stejnou topologií grafu.

Zavolejte přímo metodu GraphInstanceSet s následující datovou částí.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Je třeba počítat s následujícím:

* Výše uvedená datová část určuje název topologie (MotionDetection), pro který je potřeba vytvořit instanci.
* Datová část obsahuje hodnotu parametru "rtspUrl", která v datové části topologie grafu neobsahovala výchozí hodnotu.

Během několika sekund se v okně výstup zobrazí následující odpověď:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Všimněte si následujícího v datové části odpovědi:

* Stavový kód je 201, což značí, že se vytvořila nová instance.
* Stav je "neaktivní", což znamená, že instance grafu byla vytvořena, ale není aktivována. Další informace najdete v tématu [stavy mediálních grafů](media-graph-concept.md).

Jako další kroky zkuste použít následující:

* Znovu vyvolat GraphInstanceSet se stejnou datovou částí a Všimněte si, že vrácený stavový kód je nyní 200.
* Volejte GraphInstanceSet znovu, ale s jiným popisem a Všimněte si aktualizovaného popisu v datové části odpovědi, což značí, že se instance grafu úspěšně aktualizovala.
* Vyvolejte GraphInstanceSet, ale změňte název na Sample-Graph-2 a sledujte datovou část odpovědi. Všimněte si, že se vytvoří nová instance grafu (to znamená, že stavový kód je 201).

### <a name="invoke-graphinstanceactivate"></a>Vyvolat GraphInstanceActivate

Nyní aktivujte instanci grafu – tím se spustí tok živého videa prostřednictvím modulu. Zavolejte přímo metodu GraphInstanceActivate s následující datovou částí.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Během několika sekund by se měla zobrazit následující odpověď v okně výstup:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 v datové části odpovědi označuje, že se instance grafu úspěšně aktivovala.

### <a name="invoke-graphinstanceget"></a>Vyvolat GraphInstanceGet

Nyní volejte přímý GraphInstanceGet metody s následující datovou částí:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Během několika sekund by se měla zobrazit následující odpověď v okně výstup:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Všimněte si následujícího v datové části odpovědi:

* Stavový kód je 200, což znamená úspěch.
* Stav je "aktivní", což značí, že je instance grafu nyní ve stavu "aktivní".

## <a name="observe-results"></a>Sledovat výsledky

Instance grafu, kterou jsme vytvořili a aktivovali výše, používá uzel procesoru pro detekci pohybu k detekci pohybu v příchozím streamu živého videa a odesílá události do uzlu IoT Hub jímka. Tyto události se pak předají do centra IoT Edge, což se teď může pozorovat. To provedete podle těchto kroků.

1. Otevřete podokno Průzkumník v Visual Studio Code a vyhledejte Azure IoT Hub v levém dolním rohu.
2. Rozbalte uzel zařízení.
3. Pravým tlačítkem clink na lva-Sample-Device a zvolte možnost spustit monitorování integrovaného monitorování událostí.

![Spustit monitorování událostí IoT Hub](./media/quickstarts/start-monitoring-iothub-events.png)

V okně výstup se zobrazí následující zprávy:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Ve zprávě výše si všimněte následujícího:

* Zpráva obsahuje oddíl "tělo" a oddíl "applicationProperties". Informace o tom, co tyto oddíly reprezentují, najdete v článku o [Vytvoření a načtení IoT Hub zprávě](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* "Subject" v applicationProperties odkazuje na uzel v MediaGraph, ze kterého byla zpráva generována. V tomto případě zpráva pochází z procesoru detekce pohybu.
* eventType v applicationProperties značí, že se jedná o analytickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o události analýzy. V tomto případě událost je odvozená událost, takže tělo obsahuje data "časové razítko" a "odvození".
* část "odvozené" označuje, že "typ" je "pohyb" a obsahuje další data o události "Motion".

Pokud necháte MediaGraph běžet pro nějakou dobu, zobrazí se v okně výstup také následující zpráva:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Ve zprávě výše si všimněte následujícího:

* "Subject" v applicationProperties označuje, že zpráva byla vygenerována ze zdrojového uzlu RTSP v mediálním grafu.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "tělo" obsahuje data o diagnostické události. V tomto případě je událost MediaSessionEstablished, a proto tělo.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Vyvolání dalších přímých metod k vyčištění

Nyní volejte přímé metody deaktivace a odstranění instance grafu (v tomto pořadí).

### <a name="invoke-graphinstancedeactivate"></a>Vyvolat GraphInstanceDeactivate

Zavolejte přímo metodu GraphInstanceDeactivate s následující datovou částí.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Během několika sekund by se měla zobrazit následující odpověď v okně výstup:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že instance grafu byla úspěšně dezaktivována.

V části Další kroky zkuste následující postup.

* Vyvolejte GraphInstanceGet, jak je uvedeno v předchozích částech a sledujte hodnotu "State".

### <a name="invoke-graphinstancedelete"></a>Vyvolat GraphInstanceDelete

Vyvolat přímý GraphInstanceDelete metody s následující datovou částí

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Během několika sekund by se měla zobrazit následující odpověď v okně výstup:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 v odpovědi indikuje, že se instance grafu úspěšně odstranila.

### <a name="invoke-graphtopologydelete"></a>Vyvolat GraphTopologyDelete

Zavolejte přímo metodu GraphTopologyDelete s následující datovou částí:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Během několika sekund by se měla zobrazit následující odpověď v okně výstup:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že topologie grafu byla úspěšně odstraněna.

Jako další kroky zkuste použít následující postup.

* Vyvolejte GraphTopologyList a sledujte, že v modulu nejsou žádné topologie grafů.
* Vyvolejte GraphInstanceList se stejnou datovou částí jako GraphTopologyList a sledujte, že nejsou vyčísleny žádné instance grafů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky vytvořené v tomto rychlém startu.

## <a name="next-steps"></a>Další kroky

* Naučte se nahrávat video pomocí živé analýzy videí na IoT Edge
* Přečtěte si další informace o diagnostických zprávách.
