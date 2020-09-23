---
title: Začínáme se službou Live video Analytics v IoT Edge – Azure
description: V tomto rychlém startu se dozvíte, jak začít pracovat se službou Live video Analytics na IoT Edge. Naučte se detekovat pohyb v živém streamu videa.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0d1aaf34ad38b50403a3cbefbc953f9140f2fe82
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884938"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Rychlý Start: Začínáme – Live video Analytics na IoT Edge

V tomto rychlém startu se dozvíte, jak začít se službou Live video Analytics na IoT Edge. Používá virtuální počítač Azure jako zařízení IoT Edge. Používá také simulovaný živý Stream videa. 

Po dokončení kroků nastavení budete moct spustit simulovaný živý datový proud prostřednictvím mediálního grafu, který v tomto datovém proudu detekuje a hlásí jakýkoliv pohyb. Následující diagram graficky znázorňuje, že se jedná o mediální graf.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Analýza živých videí na základě detekce pohybu":::

## <a name="prerequisites"></a>Požadavky

* Účet Azure, který má aktivní předplatné. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači. Ujistěte se, že máte [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Ujistěte se, že je síť, ke které je připojený váš vývojový počítač, povolená prostřednictvím portu 5671 rozšířený Protokol AMQP (Message Queueing Protocol). Tato instalace umožňuje, aby nástroje Azure IoT komunikovaly se službou Azure IoT Hub.

> [!TIP]
> Při instalaci rozšíření Azure IoT Tools se může zobrazit výzva k instalaci Docker. Bez obav tuto výzvu ignorujte.

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Tento kurz vyžaduje následující prostředky Azure:

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md)

Pro tento rychlý Start doporučujeme, abyste k nasazení požadovaných prostředků ve vašem předplatném Azure použili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Postup je následující:

1. Přejít na [Azure Cloud Shell](https://shell.azure.com).
1. Pokud používáte Cloud Shell poprvé, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů. Vyberte **vytvořit úložiště** a vytvořte účet úložiště pro informace o cloud Shell relaci. Tento účet úložiště je oddělený od účtu, který vytvoří skript pro použití s vaším účtem Azure Media Services.
1. V rozevírací nabídce na levé straně okna Cloud Shell vyberte **bash** jako své prostředí.

    ![Výběr prostředí](./media/quickstarts/env-selector.png)

1. Spusťte následující příkaz.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Pokud se skript úspěšně dokončí, měli byste vidět všechny požadované prostředky v rámci vašeho předplatného. Ve výstupu skriptu obsahuje tabulka prostředků název centra IoT. Vyhledejte typ prostředku `Microsoft.Devices/IotHubs` a poznamenejte si jeho název. Tento název budete potřebovat v dalším kroku. 

Skript také vygeneruje několik konfiguračních souborů v adresáři *~/clouddrive/lva-Sample/* . Tyto soubory budete potřebovat později v rychlém startu.

## <a name="deploy-modules-on-your-edge-device"></a>Nasazení modulů na hraničním zařízení

Z Cloud Shell spusťte následující příkaz.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Tento příkaz nasadí následující moduly do hraničního zařízení, které je v tomto případě virtuálním počítačem se systémem Linux.

* Live video Analytics na IoT Edge (název modulu `lvaEdge` )
* Simulátor protokolu RTSP (Real-time streaming Protocol) (název modulu `rtspsim` )

Modul simulátoru RTSP simuluje živý Stream videa pomocí videosouboru, který jste zkopírovali do hraničního zařízení, když jste spustili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Moduly jsou nyní nasazeny, ale nejsou aktivní žádné mediální grafy.

## <a name="configure-the-azure-iot-tools-extension"></a>Konfigurace rozšíření Azure IoT Tools

Podle těchto pokynů se připojte ke službě IoT Hub pomocí rozšíření Azure IoT Tools.

1. V Visual Studio Code vyberte možnost **Zobrazit**  >  **Průzkumníka**. Nebo vyberte CTRL + SHIFT + E.
1. V levém dolním rohu karty **Průzkumník** vyberte **Azure IoT Hub**.
1. Kliknutím na ikonu **Další možnosti** zobrazíte kontextovou nabídku. Pak vyberte **nastavit IoT Hub připojovací řetězec**.
1. Po zobrazení vstupního pole zadejte připojovací řetězec IoT Hub. V Cloud Shell můžete získat připojovací řetězec z *~/clouddrive/lva-sample/appsettings.jsna*.

Pokud je připojení úspěšné, zobrazí se seznam hraničních zařízení. Měli byste vidět aspoň jedno zařízení s názvem **lva-Sample-Device**. Pomocí místní nabídky teď můžete spravovat zařízení IoT Edge a pracovat s Azure IoT Hub. Chcete-li zobrazit moduly nasazené na hraničním zařízení, v části **lva-Sample-Device**rozbalte uzel **moduly** .

![lva – uzel Sample-Device](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Pokud jste [ručně nasadili Live video Analytics na IoT Edge](deploy-iot-edge-device.md) yourselves na hraničním zařízení (třeba zařízení ARM64), zobrazí se v části Azure IoT Hub modul pod tímto zařízením. Tento modul můžete vybrat a postupovat podle dalších kroků níže.

## <a name="use-direct-method-calls"></a>Použití volání přímých metod

Pomocí modulu můžete analyzovat živé streamy videa vyvoláním přímých metod. Další informace najdete v tématu [přímé metody analýzy živých videí v IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Vyvolat GraphTopologyList

Chcete-li vytvořit výčet všech [topologií grafu](media-graph-concept.md#media-graph-topologies-and-instances) v modulu:

1. V Visual Studio Code klikněte pravým tlačítkem myši na modul **lvaEdge** a vyberte **vyvolat modul metodu Direct**.
1. Do zobrazeného pole zadejte *GraphTopologyList*.
1. Zkopírujte následující datovou část JSON a vložte ji do pole. Pak vyberte klávesu ENTER.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Během několika sekund se v okně **výstup** zobrazuje následující odpověď.

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
    
    Tato odpověď se očekává, protože se nevytvořily žádné topologie grafů.
    

### <a name="invoke-graphtopologyset"></a>Vyvolat GraphTopologySet

Pomocí kroků pro vyvolání `GraphTopologyList` můžete vyvolat `GraphTopologySet` pro nastavení [topologie grafu](media-graph-concept.md#media-graph-topologies-and-instances). Jako datovou část použijte následující JSON.

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

Tato datová část JSON vytvoří topologii grafu, která definuje tři parametry. Dva z těchto parametrů mají výchozí hodnoty. Topologie má jeden uzel zdroj (zdroj RTSP), uzel jeden procesor (procesor detekce pohybu) a jeden uzel jímky (IoT Hub jímka).

Během několika sekund se v okně **výstup** zobrazí následující odpověď.

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

Vrácený stav je 201. Tento stav označuje, že se vytvořila nová topologie. 

Vyzkoušejte následující další kroky:

1. `GraphTopologySet`Znovu vyvolat. Vrácený kód stavu je 200. Tento kód označuje, že se úspěšně aktualizovala existující topologie.
1. `GraphTopologySet`Znovu vyvolat, ale změnit řetězec popisu. Vrácený kód stavu je 200 a popis se aktualizuje na novou hodnotu.
1. Vyvolat `GraphTopologyList` , jak je uvedeno v předchozí části. Nyní můžete zobrazit `MotionDetection` topologii ve vrácené datové části.

### <a name="invoke-graphtopologyget"></a>Vyvolat GraphTopologyGet

Vyvolat `GraphTopologyGet` pomocí následující datové části.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Během několika sekund se v okně **výstup** zobrazí následující odpověď:

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

V datové části odpovědi si všimněte těchto podrobností:

* Stavový kód je 200, což znamená úspěch.
* Datová část zahrnuje `created` časové razítko a `lastModified` časové razítko.

### <a name="invoke-graphinstanceset"></a>Vyvolat GraphInstanceSet

Vytvořte instanci grafu, která odkazuje na předchozí topologii grafu. Instance grafů umožňují analyzovat živé video streamy z mnoha kamer pomocí stejné topologie grafu. Další informace najdete v tématu [topologie a instance mediálních grafů](media-graph-concept.md#media-graph-topologies-and-instances).

Volejte přímou metodu `GraphInstanceSet` pomocí následující datové části.

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

Všimněte si, že tato datová část:

* Určuje název topologie ( `MotionDetection` ), pro který je nutné vytvořit instanci.
* Obsahuje hodnotu parametru pro `rtspUrl` , která neobsahovala výchozí hodnotu v datové části topologie grafu.

Během několika sekund se v okně **výstup** zobrazí následující odpověď:

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

V datové části odpovědi si všimněte, že:

* Stavový kód je 201, což značí, že byla vytvořena nová instance.
* Stav je `Inactive` , což znamená, že instance grafu byla vytvořena, ale není aktivována. Další informace najdete v tématu [stavy mediálních grafů](media-graph-concept.md).

Vyzkoušejte následující další kroky:

1. `GraphInstanceSet`Znovu vyvolat pomocí stejné datové části. Všimněte si, že vrácený kód stavu je 200.
1. `GraphInstanceSet`Znovu vyvolat, ale použijte jiný popis. Všimněte si aktualizovaného popisu v datové části odpovědi, což značí, že se instance grafu úspěšně aktualizovala.
1. Vyvolejte `GraphInstanceSet` , ale změňte název na `Sample-Graph-2` . V datové části odpovědi si všimněte nově vytvořené instance grafu (to znamená stavový kód 201).

### <a name="invoke-graphinstanceactivate"></a>Vyvolat GraphInstanceActivate

Nyní aktivujte instanci grafu, aby bylo možné spustit tok živého videa prostřednictvím modulu. Volejte přímou metodu `GraphInstanceActivate` pomocí následující datové části.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Během několika sekund se v okně **výstup** zobrazí následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že se instance grafu úspěšně aktivovala.

### <a name="invoke-graphinstanceget"></a>Vyvolat GraphInstanceGet

Nyní volejte přímou metodu `GraphInstanceGet` pomocí následující datové části.

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Během několika sekund se v okně **výstup** zobrazí následující odpověď.

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

V datové části odpovědi si všimněte následujících údajů:

* Stavový kód je 200, což znamená úspěch.
* Stav je `Active` , což značí, že je instance grafu nyní aktivní.

## <a name="observe-results"></a>Sledovat výsledky

Instance grafu, kterou jsme vytvořili a aktivovali, používá uzel procesoru detekce pohybu k detekci pohybu v příchozím datovém proudu živého videa. Odesílá události do uzlu IoT Hub jímka. Tyto události jsou předávány do centra IoT Edge. 

Chcete-li sledovat výsledky, postupujte podle těchto kroků.

1. V Visual Studio Code otevřete podokno **Průzkumník** . V levém dolním rohu vyhledejte **Azure IoT Hub**.
2. Rozbalte uzel **zařízení** .
3. Klikněte pravým tlačítkem na **lva-Sample-Device** a pak vyberte **Spustit monitorování integrovaného monitorování událostí**.

    ![Spustit monitorování událostí IoT Hub](./media/quickstarts/start-monitoring-iothub-events.png)
    
V okně **výstup** se zobrazí následující zpráva:

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

Všimněte si těchto podrobností:

* Zpráva obsahuje `body` oddíl a `applicationProperties` oddíl. Další informace najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).
* V `applicationProperties` , `subject` odkazuje na uzel v, `MediaGraph` ze kterého byla zpráva generována. V tomto případě zpráva pochází z procesoru detekce pohybu.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je analytická událost.
* `eventTime`Hodnota je čas, kdy došlo k události.
* `body`Oddíl obsahuje data o události analýzy. V tomto případě událost je odvozená událost, takže tělo obsahuje `timestamp` `inferences` data a.
* `inferences`Část označuje, že `type` je `motion` . Poskytuje další data o `motion` události.

Pokud necháte Media Graph běžet v průběhu chvilky, zobrazí se v okně **výstup** následující zpráva.

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

V této zprávě si všimněte následujících podrobností:

* V `applicationProperties` nástroji `subject` označuje, že zpráva byla vygenerována ze zdrojového uzlu RTSP v mediálním grafu.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je diagnostická.
* `body`Obsahuje data o diagnostické události. V tomto případě zpráva obsahuje tělo, protože událost je `MediaSessionEstablished` .

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Vyvolání dalších přímých metod k vyčištění

Vyvolejte přímé metody pro první deaktivaci instance grafu a pak ji odstraňte.

### <a name="invoke-graphinstancedeactivate"></a>Vyvolat GraphInstanceDeactivate

Volejte přímou metodu `GraphInstanceDeactivate` pomocí následující datové části.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Během několika sekund se v okně **výstup** zobrazí následující odpověď:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že instance grafu byla úspěšně dezaktivována.

Dále se pokuste vyvolat, `GraphInstanceGet` jak je uvedeno výše v tomto článku. Sledujte `state` hodnotu.

### <a name="invoke-graphinstancedelete"></a>Vyvolat GraphInstanceDelete

Volejte přímou metodu `GraphInstanceDelete` pomocí následující datové části.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Během několika sekund se v okně **výstup** zobrazí následující odpověď:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že se instance grafu úspěšně odstranila.

### <a name="invoke-graphtopologydelete"></a>Vyvolat GraphTopologyDelete

Volejte přímou metodu `GraphTopologyDelete` pomocí následující datové části.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Během několika sekund se v okně **výstup** zobrazí následující odpověď.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Stavový kód 200 označuje, že topologie grafu byla úspěšně odstraněna.

Vyzkoušejte následující další kroky:

1. Vyvolejte `GraphTopologyList` a sledujte, že modul neobsahuje žádné topologie grafu.
1. Vyvolat `GraphInstanceList` pomocí stejné datové části jako `GraphTopologyList` . Všimněte si, že nejsou vyčísleny žádné instance grafů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky, které jste vytvořili v rámci tohoto rychlého startu.

## <a name="next-steps"></a>Další kroky

* Naučte se [nahrávat video pomocí živé analýzy videí na IoT Edge](continuous-video-recording-tutorial.md).
* Přečtěte si další informace o [diagnostických zprávách](monitoring-logging.md).
