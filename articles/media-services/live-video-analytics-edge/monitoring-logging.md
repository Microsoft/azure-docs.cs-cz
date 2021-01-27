---
title: Monitorování a protokolování – Azure
description: Tento článek poskytuje přehled monitorování a protokolování v živé analýze videí v IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 6dc0a6d499d06c95bdccbc9e386d7f9288971ee8
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878100"
---
# <a name="monitoring-and-logging"></a>Monitorování a protokolování

V tomto článku se dozvíte, jak přijímat události pro vzdálené monitorování ze služby Live video Analytics v modulu IoT Edge. 

Naučíte se také, jak řídit protokoly generované modulem.

## <a name="taxonomy-of-events"></a>Taxonomie událostí

Live video Analytics na IoT Edge generuje události nebo data telemetrie v závislosti na následující taxonomii:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagram znázorňující taxonomii událostí.":::

* Provozní: události vygenerované akcemi uživatele nebo při spuštění [mediálního grafu](media-graph-concept.md)
   
   * Volume: očekávalo se, že bude nízká (za pár minut nebo ještě méně).
   * Příklady:

      - Záznam byl zahájen (znázorněno v následujícím příkladu)
      - Záznam se zastavil.
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostika: události, které vám pomůžou diagnostikovat problémy s výkonem

   * Svazek: může být vysoký (několikrát za minutu).
   * Příklady:
   
      - Informace o službě RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) (viz následující příklad) 
      - Mezery v kanálu příchozího videa

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analýzy: události vygenerované jako součást analýzy videí

   * Svazek: může být vysoký (několikrát za minutu nebo více).
   * Příklady:
      
      - Byl zjištěn pohyb (zobrazený v následujícím příkladu) 
      - Výsledek odvození

   ```      
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
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Události emitované modulem se odesílají do [centra IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). Je možné je směrovat z umístění do jiných cílů. 

### <a name="timestamps-in-analytic-events"></a>Časová razítka v analytických událostech

Jak bylo uvedeno dříve, události vygenerované jako součást analýzy videa mají přidružena časová razítka. Pokud jste jako součást topologie grafu [nahráli živé video](video-recording-concept.md) , tato časová razítka vám pomůžou najít, kde v zaznamenaném videu došlo k určité události. Níže jsou uvedeny pokyny pro mapování časového razítka v analytické události na časovou osu videa zaznamenaného v [Azure Media Servicesm prostředku](terminology.md#asset).

Nejprve rozbalte `eventTime` hodnotu. Tuto hodnotu použijte v rámci [filtru časového rozsahu](playback-recordings-how-to.md#time-range-filters) k načtení vhodné části záznamu. Můžete například chtít načíst video, které začíná 30 sekund před `eventTime` a končí 30 sekundami. V předchozím příkladu, kde `eventTime` je 2020-05-12T23:33:09.381 z, požadavek na manifest HLS po dobu 30 sekund před a potom `eventTime` by vypadal jako tento požadavek:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Předchozí adresa URL vrátí [hlavní seznam](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) testů, který obsahuje adresy URL pro seznamy skladeb multimédií. Seznam stop média by obsahoval položky podobné tomuto:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Předchozí položka hlásí, že je k dispozici fragment videa, který začíná `timestamp` hodnotou `143039375031270` . `timestamp`Hodnota v analytické události používá stejnou časovou osu jako seznam stop média. Dá se použít k identifikaci relevantního fragmentu videa a k hledání správného rámce.

Další informace najdete v těchto [článcích o přesném hledání](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) v HLS v rámci rámce.

## <a name="controlling-events"></a>Řízení událostí

Pomocí následujících vlastností modulu můžete řídit provozní a diagnostické události publikované v rámci analýzy živých videí v modulu IoT Edge. Tyto vlastnosti jsou popsány v [modulu s dvojitým výsledkem ve schématu JSON](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName`: Chcete-li získat diagnostické události z modulu, zahrňte tuto vlastnost a zadejte pro ni libovolnou hodnotu. Vynechejte nebo ponechte prázdné, aby modul zastavil publikování diagnostických událostí.
   
- `operationalEventsOutputName`: Chcete-li získat provozní události z modulu, zahrňte tuto vlastnost a zadejte pro ni libovolnou hodnotu. Vynechejte nebo ponechte prázdné, aby se modul zastavil od publikování provozních událostí.
   
Události analýzy jsou generovány uzly, jako je procesor detekce pohybu nebo procesor rozšíření HTTP. Jímka centra IoT slouží k jejich posílání do centra IoT Edge. 

[Směrování všech předchozích událostí](../../iot-edge/module-composition.md#declare-routes) můžete řídit pomocí `desired` vlastnosti `$edgeHub` modulu v manifestu nasazení:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

V předchozím formátu JSON `lvaEdge` je název živé analýzy videí v modulu IoT Edge. Pravidlo směrování následuje po schématu definované v části [Declare Routes](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Aby se zajistilo, že události analýzy dosáhnou centra IoT Edge, je nutné mít uzel jímky služby IoT Hub v jednom uzlu procesoru pro detekci pohybů nebo libovolného uzlu procesoru rozšíření HTTP.

## <a name="event-schema"></a>Schéma událostí

Události pocházejí z hraničního zařízení a dají se spotřebovat na hranici nebo v cloudu. Události generované živou analýzou videa na IoT Edge odpovídají [vzoru zasílání zpráv streamování](../../iot-hub/iot-hub-devguide-messages-construct.md) , který vytváří Azure IoT Hub. Vzor se skládá z vlastností systému, vlastností aplikace a textu.

### <a name="summary"></a>Souhrn

Každá událost, pokud je pozorována prostřednictvím IoT Hub, má sadu společných vlastností:

|Vlastnost   |Typ vlastnosti| Datový typ   |Popis|
|---|---|---|---|
|`message-id`   |systém |guid|  Jedinečné ID události|
|`topic`|   applicationProperty |řetězec|    Azure Resource Manager cesta pro Azure Media Services účet.|
|`subject`| applicationProperty |řetězec|    Dílčí cesta entity, která vysílá událost|
|`eventTime`|   applicationProperty|    řetězec| Čas, kdy byla událost vygenerována.|
|`eventType`|   applicationProperty |řetězec|    Identifikátor typu události (Další informace najdete v následující části.)|
|`body`|text    |object|    Konkrétní data události.|
|`dataVersion`  |applicationProperty|   řetězec  |{Hlavní}. Moll|

### <a name="properties"></a>Vlastnosti

#### <a name="message-id"></a>ID zprávy

Globálně jedinečný identifikátor (GUID) pro událost.

#### <a name="topic"></a>téma

Představuje účet Azure Media Services přidružený ke grafu.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Entita, která vyvolala událost:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject`Vlastnost umožňuje mapovat Obecné události na modul generování. Například pro neplatné uživatelské jméno nebo heslo protokolu RTSP bude vygenerovaná událost `Microsoft.Media.Graph.Diagnostics.ProtocolError` na `/graphInstances/myGraph/sources/myRtspSource` uzlu.

#### <a name="event-types"></a>Typy událostí

Typy událostí jsou přiřazeny k oboru názvů podle tohoto schématu:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Třídy událostí

|Název třídy|Popis|
|---|---|
|Analýzy  |Události generované jako součást analýzy obsahu|
|Diagnostika    |Události, které vám pomůžou diagnostikovat problémy a výkon.|
|Provoz    |Události generované jako součást operace prostředků|

Typy událostí jsou specifické pro každou třídu Event.

Příklady:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Čas události

Čas události je naformátován v řetězci ISO 8601. Představuje čas, kdy došlo k události.

### <a name="azure-monitor-collection-via-telegraf"></a>Kolekce Azure Monitor přes telegraf

Tyto metriky se budou nahlásit z videa Live video Analytics v modulu IoT Edge:  

|Název metriky|Typ|Popisek|Popis|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Měřidlo|iothub, edge_device module_name, graph_topology|Celkový počet aktivních grafů na topologii.|
|lva_received_bytes_total|Čítač|iothub, edge_device, module_name, graph_topology, graph_instance graph_node|Celkový počet bajtů přijatých uzlem Podporováno pouze pro zdroje RTSP.|
|lva_data_dropped_total|Čítač|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node data_kind|Čítač všech vyřazených dat (události, média atd.).|

> [!NOTE]
> [Koncový bod Prometheus](https://prometheus.io/docs/practices/naming/) se zveřejňuje na portu 9600 kontejneru. Pokud zadáte své Live video Analytics na IoT Edge Module "lvaEdge", budou moci získat přístup k metrikám odesláním žádosti o získání do http://lvaEdge:9600/metrics .   

Pomocí těchto kroků povolíte shromažďování metrik z živé analýzy videí v modulu IoT Edge:

1. Ve vývojovém počítači vytvořte složku a otevřete ji do této složky.

1. Ve složce vytvořte `telegraf.toml` soubor, který obsahuje následující konfigurace:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Nezapomeňte nahradit proměnné v souboru. toml. Proměnné jsou označeny složenými závorkami ( `{}` ).

1. Ve stejné složce vytvořte `.dockerfile` , který obsahuje následující příkazy:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Pomocí příkazů Docker CLI Sestavte soubor Docker a publikujte image do služby Azure Container Registry.
    
   Další informace o použití rozhraní příkazového řádku Docker pro vložení do registru kontejneru najdete v tématu [Image Docker push a pull](../../container-registry/container-registry-get-started-docker-cli.md). Další informace o Azure Container Registry najdete v [dokumentaci](../../container-registry/index.yml).


1. Po dokončení operace Push na Azure Container Registry přidejte do souboru manifestu nasazení následující uzel:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Nezapomeňte nahradit proměnné v souboru manifestu. Proměnné jsou označeny složenými závorkami ( `{}` ).


   Azure Monitor lze [ověřit prostřednictvím instančního objektu](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Modul plug-in Azure Monitor telegraf zpřístupňuje [několik metod ověřování](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Chcete-li použít ověřování instančního objektu, nastavte tyto proměnné prostředí:  
     `AZURE_TENANT_ID`: Určuje tenanta, pro který se má ověřit.  
     `AZURE_CLIENT_ID`: Určuje ID klienta aplikace, které se má použít.  
     `AZURE_CLIENT_SECRET`: Určuje tajný klíč aplikace, který se má použít.  
     
     >[!TIP]
     > Instančnímu objektu můžete přidělit roli **vydavatele metrik monitorování** .

1. Po nasazení modulů se metriky zobrazí v Azure Monitor pod jediným oborem názvů. Názvy metrik budou odpovídat těm, které emituje Prometheus. 

   V takovém případě v Azure Portal v levém podokně vyberte **metriky a vyberte metriky** . Měla by se zobrazit Metrika.

## <a name="logging"></a>protokolování

Stejně jako u jiných IoT Edgech modulů můžete také [prozkoumávat protokoly kontejnerů](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) na hraničním zařízení. Informace, které se zapisují do protokolů, můžete nakonfigurovat pomocí [následujících dvojitých vlastností modulu](module-twin-configuration-schema.md) :

* `logLevel`

   * Povolené hodnoty jsou `Verbose` , `Information` ,, a `Warning` `Error` `None` .
   * Výchozí hodnota je `Information`. Protokoly budou obsahovat chybové zprávy, upozornění a informační zprávy.
   * Pokud nastavíte hodnotu na `Warning` , protokoly budou obsahovat chybové zprávy a upozornění.
   * Pokud nastavíte hodnotu na `Error` , protokoly budou obsahovat pouze chybové zprávy.
   * Pokud nastavíte hodnotu na `None` , nebudou vygenerovány žádné protokoly. (Tuto konfiguraci nedoporučujeme.)
   * Použijte `Verbose` pouze v případě, že potřebujete sdílet protokoly s podporou Azure pro diagnostiku problému.

* `logCategories`

   * Čárkami oddělený seznam jedné nebo více z těchto hodnot: `Application` , `Events` , `MediaPipeline` .
   * Výchozí hodnota je `Application, Events`.
   * `Application`: Informace vysoké úrovně z modulu, například zprávy o spuštění modulu, chyby prostředí a přímá volání metody.
   * `Events`: Všechny události, které byly popsány dříve v tomto článku.
   * `MediaPipeline`: Protokoly nízké úrovně, které můžou nabízet přehledy při řešení problémů, jako jsou potíže s navázáním spojení s fotoaparátem podporujícím RTSP.
   
### <a name="generating-debug-logs"></a>Generování protokolů ladění

V některých případech může být potřeba vygenerovat podrobnější protokoly, než jaké jste popsali v tomto tématu, aby mohla podpora Azure vyřešit problém. Generování těchto protokolů:

1. [Propojte úložiště modulu s úložištěm zařízení](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) prostřednictvím `createOptions` . Pokud se podíváte na [šablonu manifestu nasazení](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) z rychlých startů, uvidíte tento kód:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Tento kód umožňuje, aby modul Edge zapisoval protokoly do cesty úložiště zařízení `/var/local/mediaservices/` . 

 1. Přidejte `desired` do modulu následující vlastnost:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Modul nyní bude zapisovat protokoly ladění v binárním formátu do cesty úložiště zařízení `/var/local/mediaservices/debuglogs/` . Tyto protokoly můžete sdílet s podporou Azure.

## <a name="faq"></a>Nejčastější dotazy

Pokud máte nějaké dotazy, přečtěte si [Nejčastější dotazy týkající se monitorování a metrik](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Další kroky

[Nepřetržité nahrávání videa](continuous-video-recording-tutorial.md)