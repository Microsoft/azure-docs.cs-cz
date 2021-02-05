---
title: Detekce pohybu, nahrávání videa do Azure Media Services
description: V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k detekci pohybů v živém datovém streamu a k nahrávání videoklipů do Azure Media Services.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 135e68e2630d74dace6c3a6b70bb3666f77aad89
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575530"
---
# <a name="quickstart-detect-motion-record-video-to-media-services"></a>Rychlý Start: zjištění pohybu, nahrání videa do Media Services

Tento článek vás provede kroky k použití živé analýzy videí v IoT Edge pro [záznam založený na událostech](event-based-video-recording-concept.md). Používá virtuální počítač Linux v Azure jako zařízení IoT Edge a simulovaný živý Stream videa. Tento datový proud videa se analyzuje pro přítomnost přesunu objektů. Když se zjistí pohyb, odesílají se události do Azure IoT Hub a příslušná část streamu videa se zaznamená jako Asset v Azure Media Services.

Tento článek se sestavuje na začátku [Začínáme rychlý Start](get-started-detect-motion-emit-events-quickstart.md).

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  > [!NOTE]
  > Budete potřebovat předplatné Azure s oprávněním pro vytváření instančních objektů (Tato **role vlastníka** poskytuje). Pokud nemáte správná oprávnění, obraťte se na správce účtu, abyste vám udělili správná oprávnění. 
* [Visual Studio Code](https://code.visualstudio.com/) na počítači pomocí [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Pokud jste dříve nedokončili [Začínáme rychlý Start](get-started-detect-motion-emit-events-quickstart.md) , Projděte si následující kroky:
    * [Nastavení prostředků Azure](get-started-detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Nasazení modulů](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)
    * [Konfigurace editoru Visual Studio Code](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)
    > [!TIP]
    > Pokud narazíte na problémy s prostředky Azure, které se vytvoří, přečtěte si náš **[Průvodce odstraňováním potíží](troubleshoot-how-to.md#common-error-resolutions)** a vyřešte některé běžně zjištěné problémy.
## <a name="review-the-sample-video"></a>Kontrola ukázkového videa

V rámci výše uvedeného postupu pro nastavení prostředků Azure se do virtuálního počítače Linux v Azure, který se používá jako zařízení IoT Edge, zkopíruje (krátké) video o zaparkované dávce. Tento videosoubor se použije k simulaci živého datového proudu pro tento kurz.

Můžete použít aplikaci, jako je [VLC Player](https://www.videolan.org/vlc/), spustit ji, stisknout `Ctrl+N` a vložit [ukázkový odkaz ukázka](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) rádiového videa pro spuštění přehrávání. V symbolech 5 sekund se bílá Auto pohybuje přes sérii parkovacích míst.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Po dokončení níže uvedeného postupu budete pro detekci pohybu auta používat Live video Analytics na IoT Edge a zaznamenáte si video klip počínaje kolem této 5 sekundové značky. Diagram níže je vizuální znázornění celkového toku.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/topology.svg" alt-text="Nahrávání videa založeného na událostech na prostředky na základě událostí pohybu":::

## <a name="use-direct-method-calls"></a>Použití volání přímých metod

Pomocí modulu můžete analyzovat živé streamy videa vyvoláním přímých metod. Přečtěte si [přímé metody pro Live video Analytics na IoT Edge](direct-methods.md) , abyste pochopili všechny přímé metody poskytované modulem. 

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::

### <a name="invoke-graphtopologylist"></a>Vyvolat GraphTopologyList
Tento krok vytvoří výčet všech [topologií grafu](media-graph-concept.md#media-graph-topologies-and-instances) v modulu.

1. Klikněte pravým tlačítkem na modul lvaEdge a v místní nabídce vyberte vyvolat modul Direct Module.
1. V horní části okna Visual Studio Code se zobrazí okno pro úpravu. Do pole pro úpravy zadejte "GraphTopologyList" a stiskněte klávesu ENTER.
1. V dalším kroku zkopírujte a vložte níže uvedenou datovou část JSON do pole pro úpravy a stiskněte klávesu ENTER.
    
```
{
    "@apiVersion" : "2.0"
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

Pomocí stejných kroků, jako jsou ty, které jsou popsané pro vyvolání GraphTopologyList, můžete vyvolat GraphTopologySet pro nastavení [topologie grafu](media-graph-concept.md#media-graph-topologies-and-instances) pomocí následujícího JSON jako datové části. Vytvoří se topologie grafu s názvem "EVRtoAssetsOnMotionDetection".

```
{
    "@apiVersion": "2.0",
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
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
            "default" : "dummyPassword"
        },
        {
            "name": "rtspUrl",
            "type": "String",
            "description": "rtsp Url"
        },
        {
            "name": "motionSensitivity",
            "type": "String",
            "description": "motion detection sensitivity",
            "default" : "medium"
        },
        {
            "name": "hubSinkOutputName",
            "type": "String",
            "description": "hub sink output name",
            "default" : "iothubsinkoutput"
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
          "sensitivity": "${motionSensitivity}",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection"
            },
            {
              "nodeName": "rtspSource"
            }
          ],
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S"
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "name": "assetSink",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "segmentLength": "PT0M30S",
          "localMediaCacheMaximumSizeMiB": "2048",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "inputs": [
            {
              "nodeName": "signalGateProcessor"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "${hubSinkOutputName}",
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

Výsledkem výše uvedené datové části JSON je vytvoření topologie grafu, která definuje pět parametrů (čtyři z nich mají výchozí hodnoty). Topologie má jeden zdrojový uzel ([zdroj RTSP](media-graph-concept.md#rtsp-source)), dva uzly procesoru ([procesor pro detekci pohybu](media-graph-concept.md#motion-detection-processor) a [procesor brány signálu](media-graph-concept.md#signal-gate-processor)a dva uzly jímky (IoT Hub jímka a [jímka assetu](media-graph-concept.md#asset-sink)). Vizuální znázornění topologie je uvedeno výše.

Během několika sekund se v okně **výstup** zobrazí následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to assets based on motion events",
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
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
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
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
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

Vrácený stav je 201, což značí, že se vytvořila nová topologie grafu. Vyzkoušejte následující přímé metody jako další kroky:

* Znovu zavolejte GraphTopologySet a ověřte, že vrácený kód stavu je 200. Stavový kód 200 označuje, že existující topologie grafu byla úspěšně aktualizována.
* Znovu vyvolat GraphTopologySet, ale změňte řetězec popisu. Ověřte, zda je stavový kód v odpovědi 200 a zda je popis aktualizován na novou hodnotu.
* Vyvolejte GraphTopologyList, jak je uvedeno v předchozí části, a zkontrolujte, že se teď v vracené datové části zobrazuje topologie grafu "EVRtoAssetsOnMotionDetection".

### <a name="invoke-graphtopologyget"></a>Vyvolat GraphTopologyGet

Nyní volejte GraphTopologyGet s následující datovou částí
```

{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Během několika sekund by se měla v okně výstup zobrazit následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
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
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
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
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
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

Všimněte si následujících vlastností v datové části odpovědi:

* Stavový kód je 200, což znamená úspěch.
* Datová část obsahuje časové razítko "vytvořeno" a "lastModified".

### <a name="invoke-graphinstanceset"></a>Vyvolat GraphInstanceSet

Dále vytvořte instanci grafu, která odkazuje na výše uvedenou topologii grafu. Jak je vysvětleno [zde](media-graph-concept.md#media-graph-topologies-and-instances), instance grafů umožňují analyzovat živé video streamy z mnoha kamer se stejnou topologií grafu.

Nyní volejte metodu GraphInstanceSet Direct s následující datovou částí:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2",
    "properties" : {
        "topologyName" : "EVRtoAssetsOnMotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

Všimněte si, že:

* Výše uvedená datová část určuje název topologie grafu (EVRtoAssetsOnMotionDetection), pro který je nutné vytvořit instanci grafu.
* Datová část obsahuje hodnotu parametru "rtspUrl", která v datové části topologie nemá výchozí hodnotu.

Během několika sekund se v okně výstup zobrazí následující odpověď:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Všimněte si následujících vlastností v datové části odpovědi:

* Stavový kód je 201, což značí, že se vytvořila nová instance.
* Stav je "neaktivní", což znamená, že instance grafu byla vytvořena, ale není aktivována. Další informace najdete v tématu stavy [mediálních grafů](media-graph-concept.md) .

Vyzkoušejte následující přímé metody jako další kroky:

* Znovu vyvolat GraphInstanceSet se stejnou datovou částí a Všimněte si, že vrácený stavový kód je nyní 200.
* Zavolejte znovu GraphInstanceSet, ale s jiným popisem, a poznamenejte si aktualizovaný popis v datové části odpovědi, což značí, že se instance grafu úspěšně aktualizovala.
* Vyvolejte GraphInstanceSet, ale změňte název na Sample-Graph-3 a sledujte datovou část odpovědi. Všimněte si, že se vytvoří nová instance grafu (to znamená, že stavový kód je 201). Nezapomeňte tyto duplicitní instance vyčistit, až budete hotovi s rychlým startem.

### <a name="prepare-for-monitoring-events"></a>Příprava na monitorování událostí

Vytvořené médium používá uzel procesoru detekce pohybu k detekci pohybu a tyto události jsou přenášeny do IoT Hub. Chcete-li provést přípravu na sledování takových událostí, postupujte podle těchto kroků.

1. Otevřete podokno Průzkumník v Visual Studio Code a vyhledejte Azure IoT Hub v levém dolním rohu.
1. Rozbalte uzel zařízení.
1. Klikněte pravým tlačítkem na lva-Sample-Device a zvolte možnost spustit sledování integrovaného monitorování událostí.

    ![Spustit monitorování integrovaného monitorování událostí](./media/quickstarts/start-monitoring-iothub-events.png)
    
    > [!NOTE]
    > Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    Během několika sekund se v okně výstup zobrazí následující zprávy:

```
[IoTHubMonitor] Start monitoring message arrived in built-in endpoint for all devices ...
[IoTHubMonitor] Created partition receiver [0] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [1] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [2] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [3] for consumerGroup [$Default]
```

### <a name="invoke-graphinstanceactivate"></a>Vyvolat GraphInstanceActivate

Nyní aktivujte instanci grafu – tím se spustí tok živého videa prostřednictvím modulu. Zavolejte přímo metodu GraphInstanceActivate s následující datovou částí:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Během několika sekund by se měla v okně výstup zobrazit následující odpověď.

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

Nyní volejte metodu GraphInstanceGet Direct s následující datovou částí:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Během několika sekund by se měla v okně výstup zobrazit následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Active",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Všimněte si následujících vlastností v datové části odpovědi:

* Stavový kód je 200, což znamená úspěch.
* Stav je "aktivní", což značí, že je instance grafu nyní ve stavu "aktivní".

## <a name="observe-results"></a>Sledovat výsledky

Instance grafu, kterou jste vytvořili a aktivovali výše, používá uzel procesoru pro detekci pohybu k detekci pohybu v příchozím streamu živého videa a odesílá události do IoT Hub jímky. Tyto události se pak předají do IoT Hub, které se teď můžou pozorovat. V okně výstup se zobrazí následující zprávy.

```
[IoTHubMonitor] [4:33:04 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-12T23:33:04.077Z",
    "dataVersion": "1.0"
  }
}
[IoTHubMonitor] [4:33:09 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143039375044290,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/processors/md",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-12T23:33:09.381Z",
    "dataVersion": "1.0"
  }
}
```

Všimněte si následujících vlastností ve výše uvedených zprávách.

* Každá zpráva obsahuje oddíl "tělo" a oddíl "applicationProperties". Informace o tom, co tyto oddíly reprezentují, najdete v článku o [Vytvoření a načtení IoT Hub zprávě](../../iot-hub/iot-hub-devguide-messages-construct.md).
* První zprávou je událost diagnostiky, MediaSessionEstablished říká, že se zdrojový uzel RTSP (předmět) mohl navázat spojení s simulátorem RTSP a začít přijímat (simulované) živé kanály.
* "Subject" v applicationProperties odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* eventType v applicationProperties značí, že se jedná o diagnostickou událost.
* "čas události" označuje čas, kdy došlo k události.
* "tělo" obsahuje data o diagnostické události – jedná se o zprávu [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .
* Druhá zpráva je analytická událost. Můžete ověřit, že se pošle přibližně 5 sekund po zprávě MediaSessionEstablished, která odpovídá prodlevě mezi začátkem videa a v případě automobilových jednotek prostřednictvím parkovací dávky.
* "Subject" v applicationProperties odkazuje na uzel procesoru detekce pohybu v grafu, který vygeneroval tuto zprávu.
* Událost je odvozená událost, takže tělo obsahuje data "časové razítko" a "odvozené".
* část "odvozené" označuje, že "typ" je "pohyb" a obsahuje další data o události "Motion".

Následující zpráva se zobrazí jako následující.

```
[IoTHubMonitor] [4:33:10 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-12T23:33:10.392Z",
    "dataVersion": "1.0"
  }
}
```

* Třetí zprávou je provozní událost. Můžete ověřit, že se pošle skoro hned po zprávě o detekci pohybu, která se používá jako Trigger ke spuštění záznamu.
* "Subject" v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu.
* Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video. Poznamenejte si tuto hodnotu – budete ji používat později v rychlém startu.

V topologii byl uzel procesoru signálu signálu nakonfigurovaný s dobami aktivace 30 sekund, což znamená, že topologie grafu bude nahrávat přibližně 30 sekund do assetu. Během zaznamenávání videa bude uzel procesoru detekce pohybu nadále generovat odvozené události, které se zobrazí v okně výstup. Po určité době se zobrazí následující zpráva.

```
[IoTHubMonitor] [4:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-12T23:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

* Tato zpráva je také provozní událostí. Událost RecordingAvailable označuje, že do Assetu byly zapsány dostatečné údaje, aby si hráči/klienti mohli zahájit přehrávání videa.
* "Subject" v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu.
* Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video.

Pokud necháte instanci grafu pokračovat v běhu, zobrazí se tato zpráva.

```
[IoTHubMonitor] [4:33:40 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-12T23:33:40.014Z",
    "dataVersion": "1.0"
  }
}
```

* Tato zpráva je také provozní událostí. Událost RecordingStopped označuje, že záznam byl zastaven.
* Všimněte si, že uplynula přibližně 30 sekund od události RecordingStarted, která odpovídá hodnotám časů aktivace v uzlu procesoru brány signálu.
* "Subject" v applicationProperties odkazuje na uzel jímky assetu v grafu, který vygeneroval tuto zprávu.
* Tělo obsahuje informace o umístění výstupu, což je v tomto případě název assetu služby Azure Media Service, do kterého se nahraje video.

Pokud necháte instanci grafu pořád spuštěnou, simulátor RTSP dosáhne konce videosouboru a zastaví nebo odpojí. Uzel zdroje RTSP se pak znovu připojí k simulátoru a proces se zopakuje.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Vyvolat další volání přímé metody k vyčištění

Nyní volejte přímé metody deaktivace a odstranění instance grafu (v tomto pořadí).

### <a name="invoke-graphinstancedeactivate"></a>Vyvolat GraphInstanceDeactivate

Vyvolat metodu GraphInstanceDeactivate Direct s následující datovou částí:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Během několika sekund by se měla v okně výstup zobrazit následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že instance grafu byla úspěšně dezaktivována.

V části Další kroky zkuste následující postup:

* Vyvolejte GraphInstanceGet, jak je uvedeno v předchozích částech a sledujte hodnotu "State".

### <a name="invoke-graphinstancedelete"></a>Vyvolat GraphInstanceDelete

Vyvolat přímý GraphInstanceDelete metody s následující datovou částí

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
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

Vyvolat metodu GraphTopologyDelete Direct s následující datovou částí:

```
{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Během několika sekund by se měla v okně výstup zobrazit následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že se topologie MediaGraph úspěšně odstranila.

Vyzkoušejte následující přímé metody jako další kroky:

* Vyvolejte GraphTopologyList a sledujte, že v modulu nejsou žádné topologie grafů.
* Vyvolejte GraphInstanceList se stejnou datovou částí jako GraphTopologyList a sledujte, že nejsou vyčísleny žádné instance grafů.

## <a name="playing-back-the-recorded-video"></a>Přehrávání zaznamenaného videa

Dále můžete použít Azure Portal k přehrání videa, které jste si poznamenali.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)do vyhledávacího pole zadejte "Media Services".
1. Vyhledejte účet Azure Media Services a otevřete ho.
1. Vyhledejte položku assets v seznamu Media Services a vyberte ji.

    ![Položka assetů v seznamu Media Services](./media/quickstarts/asset-entity.png)
1. Pokud je v tomto rychlém startu první použití Azure Media Services, zobrazí se v seznamu jenom prostředky vygenerované z tohoto rychlého startu a ten si můžete vybrat nejstarší.
1. V opačném případě použijte název assetu, který byl poskytnut jako outputLocation v provozních událostech výše.
1. Na stránce s podrobnostmi, které se otevře, klikněte na odkaz vytvořit nový hned pod textovým polem adresy URL streamování.

    ![Adresa URL streamování](./media/quickstarts/asset-streaming-url.png)
1. V podokně, které se otevře pro "Přidání lokátoru streamování", přijměte výchozí hodnoty a potom v dolní části stiskněte Přidat.
1. Na stránce Podrobnosti o aktivech by se teď měl přehrávač videa načíst do prvního snímku videa a stisknout tlačítko Přehrát. Zkontrolujte, jestli se zobrazuje část videa, kde se klec pohybuje v parkovací dávce.

    ![Zobrazovaný](./media/quickstarts/asset-playback.png)

> [!NOTE]
> Vzhledem k tomu, že se simulované živé video spouští při aktivaci grafu, nevztahují se hodnoty nesouvisející s časem a nezveřejňují se prostřednictvím tohoto zástupce přehrávače. Kurz nepřetržitého nahrávání a přehrávání videa ukazuje, jak můžete zobrazit časová razítka.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky vytvořené v rámci tohoto rychlého startu.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak volat živý videozáznamy na IoT Edge [přímé metody](direct-methods.md) prostřednictvím kódu programu.
* Přečtěte si další informace o diagnostických zprávách.    
