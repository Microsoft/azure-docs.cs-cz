---
title: Monitorování a protokolování – Azure
description: Tento článek poskytuje přehled živé analýzy videí na IoT Edge monitorování a protokolování.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: ef00517fc61ac532bdd99c1e887dfd93d56a8c4f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567550"
---
# <a name="monitoring-and-logging"></a>Monitorování a protokolování

V tomto článku se dozvíte, jak můžete přijímat události z nástroje Live video Analytics v modulu IoT Edge pro vzdálené monitorování. 

Naučíte se také, jak můžete řídit protokoly, které modul generuje.

## <a name="taxonomy-of-events"></a>Taxonomie událostí

Live video Analytics na IoT Edge generuje události nebo data telemetrie podle následující taxonomie.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Taxonomie událostí":::

* Provozní: události, které jsou generovány jako součást akcí prováděných uživatelem nebo během provádění [mediálního grafu](media-graph-concept.md).
   
   * Volume: očekávalo se, že bude nízká (několikrát za minutu nebo i nižší).
   * Příklady:

      Záznam je spuštěný (níže), nahrávání se zastavilo.
      
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
* Diagnostika: události, které vám pomůžou diagnostikovat problémy nebo problémy s výkonem.

   * Svazek: může být vysoký (několikrát za minutu).
   * Příklady:
   
      Informace [o](https://en.wikipedia.org/wiki/Session_Description_Protocol) datovém kanálu RTSP (níže) nebo mezery v příchozím videu.

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
* Analytics: události, které jsou generovány jako součást analýzy videa.

   * Svazek: může být vysoký (několikrát za minutu nebo častěji).
   * Příklady:
      
      Byl zjištěn pohyb (níže), odvozený výsledek.

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

Události generované modulem jsou odesílány do [centra IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)a v takovém případě je lze směrovat do jiných cílů. 

### <a name="timestamps-in-analytic-events"></a>Časová razítka v analytických událostech

Jak je uvedeno výše, události vygenerované jako součást analýzy videa mají přidružené časové razítko. Pokud jste jako součást vaší topologie grafu [nahráli živé video](video-recording-concept.md) , toto časové razítko vám pomůže najít, kde v zaznamenaném videu došlo k určité události. V následující části najdete pokyny k mapování časového razítka v analytické události na časovou osu videa zaznamenaného v [prostředku Azure Media Service](terminology.md#asset).

Nejprve rozbalte `eventTime` hodnotu. Tuto hodnotu použijte v rámci [filtru časového rozsahu](playback-recordings-how-to.md#time-range-filters) k načtení vhodné části záznamu. Například můžete chtít načíst video, které spustí 30 sekund před `eventTime` a končí 30 sekund. U výše uvedeného příkladu, kde `eventTime` je 2020-05-12T23:33:09.381 z, požadavek na manifest HLS pro okno +/-30 s by vypadal takto:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Výše uvedená adresa URL by vrátila [hlavní seznam](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)testů s názvem, který obsahuje adresy URL pro seznamy skladeb multimédií. Seznam stop média obsahuje následující položky:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Ve výše uvedeném záznamu se zobrazí zpráva, že je k dispozici fragment videa, který začíná hodnotou časového razítka `143039375031270` . `timestamp`Hodnota v analytické události používá stejnou časovou osu jako seznam stop média a lze ji použít k identifikaci relevantního fragmentu videa a k hledání správného rámce.

Další informace najdete v tématu o tom, jak se dá v HLS přečíst jeden z mnoha [článků](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) o snímku přesně.

## <a name="controlling-events"></a>Řízení událostí

Pro řízení provozních a diagnostických událostí, které jsou publikovány ve službě Live video Analytics v modulu IoT Edge, můžete použít následující funkční vlastnosti modulu, jak je popsáno v [modulu s dvojitým](module-twin-configuration-schema.md)zápisem do schématu JSON.

`diagnosticsEventsOutputName` – Zahrňte a poskytněte (any) hodnotu pro tuto vlastnost, aby bylo možné z modulu získat diagnostické události. Vynechejte nebo ponechte prázdné, aby modul zastavil publikování diagnostických událostí.
   
`operationalEventsOutputName` – Zahrňte a poskytněte (any) hodnotu pro tuto vlastnost, aby bylo možné z modulu získat provozní události. Vynechejte, nebo ponechte prázdné, aby se modul zastavil od publikování provozních událostí.
   
Události analýzy jsou generovány uzly, jako je například procesor detekce pohybu nebo procesor rozšíření HTTP, a jímka centra IoT slouží k jejich posílání do centra IoT Edge. 

[Směrování všech výše uvedených událostí](../../iot-edge/module-composition.md#declare-routes) můžete řídit prostřednictvím požadované vlastnosti vlákna modulu $edgeHub v manifestu (v manifestu nasazení):

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

Ve výše uvedeném příkladu je lvaEdge název pro Live video Analytics v modulu IoT Edge a pravidlo směrování následuje po schématu definovaném v části [deklarace tras](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Aby se zajistilo, že události analýzy dosáhnou centra IoT Edge, musí se jednat o uzel jímky služby IoT Hub za provozu libovolného uzlu procesoru pro detekci pohybu nebo libovolného uzlu procesoru rozšíření HTTP.

## <a name="event-schema"></a>Schéma událostí

Události pocházejí z hraničního zařízení a můžou se spotřebovat na hranici nebo v cloudu. Události generované živou analýzou videa na IoT Edge odpovídají [vzoru zasílání zpráv streamování](../../iot-hub/iot-hub-devguide-messages-construct.md) , který vytvořila služba Azure IoT Hub, s vlastnostmi systému, vlastnostmi aplikace a textem.

### <a name="summary"></a>Shrnutí

Každá událost, pokud je pozorována prostřednictvím IoT Hub, bude mít sadu společných vlastností, jak je popsáno níže.

|Vlastnost   |Typ vlastnosti| Typ dat   |Popis|
|---|---|---|---|
|ID zprávy |systém |guid|  Jedinečné ID události|
|téma| applicationProperty |řetězec|    Azure Resource Manager cesta pro Media Services účet.|
|závislosti|   applicationProperty |řetězec|    Dílčí cesta k entitě, která vysílá událost|
|eventTime| applicationProperty|    řetězec| Čas, kdy byla událost vygenerována.|
|eventType| applicationProperty |řetězec|    Identifikátor typu události (viz níže).|
|text|text  |object|    Konkrétní data události.|
|dataVersion    |applicationProperty|   řetězec  |{Hlavní}. Moll|

### <a name="properties"></a>Vlastnosti

#### <a name="message-id"></a>ID zprávy

Globálně jedinečný identifikátor události (GUID)

#### <a name="topic"></a>téma

Představuje účet Azure Media Service přidružený ke grafu.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>závislosti

Entita, která vysílá událost:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Vlastnost Subject umožňuje mapovat Obecné události na modul generování. Například v případě neplatného uživatelského jména nebo hesla pro RTSP bude vygenerovaná událost `Microsoft.Media.Graph.Diagnostics.ProtocolError` na `/graphInstances/myGraph/sources/myRtspSource` uzlu.

#### <a name="event-types"></a>Typy událostí

Typy událostí jsou přiřazeny k oboru názvů podle následujícího schématu:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Třídy událostí

|Název třídy|Popis|
|---|---|
|Analýzy  |Události generované jako součást analýzy obsahu|
|Diagnostika    |Události, které pomáhají diagnostikovat problémy a výkon.|
|Provoz    |Události generované jako součást operace prostředků|

Typy událostí jsou specifické pro každou třídu Event.

Příklady:

* Microsoft. Media. Graph. Analytics. odvození
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Čas události

Čas události je popsán v ISO8601 String a v čase, kdy k události došlo.

## <a name="logging"></a>protokolování

Stejně jako u jiných IoT Edgech modulů můžete také [prozkoumávat protokoly kontejnerů](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) na hraničním zařízení. Informace, které jsou zapsány v protokolech, mohou být řízeny [následujícími dvojitými vlastnostmi modulu](module-twin-configuration-schema.md) :

* logLevel

   * Povolené hodnoty jsou verbose, Information, Warning, Error, none.
   * Výchozí hodnota jsou informace – protokoly budou obsahovat chyby, varování a informace. zprávy.
   * Pokud nastavíte hodnotu upozornění, protokoly budou obsahovat chybové zprávy a upozornění.
   * Pokud nastavíte hodnotu chyba, protokoly budou obsahovat pouze chybové zprávy.
   * Pokud nastavíte hodnotu žádné, nebudou vygenerovány žádné protokoly (nedoporučuje se).
   * Verbose byste měli použít jenom v případě, že potřebujete sdílet protokoly s podporou Azure pro diagnostiku problému.
* logCategories

   * Čárkami oddělený seznam jednoho nebo více z následujících hodnot: aplikace, události, MediaPipeline.
   * Výchozí: aplikace, události.
   * Aplikace – jedná se o informace vysoké úrovně z modulu, například zprávy o spuštění modulu, chyby prostředí a přímá volání metody.
   * Události – jedná se o všechny události popsané dříve v tomto článku.
   * MediaPipeline – jedná se o některé protokoly nízké úrovně, které mohou nabídnout přehled při řešení problémů, například problémy s navázáním spojení s fotoaparátem podporujícím RTSP.
   
### <a name="generating-debug-logs"></a>Generování protokolů ladění

V některých případech možná budete muset vygenerovat podrobnější protokoly než ty, které jsou popsané výše, a pomoci tak podpoře Azure vyřešit problém. Tuto akci lze provést dvěma kroky.

Nejdřív [propojíte úložiště modulu s úložištěm zařízení](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) přes createOptions. Pokud prohlížíte [šablonu manifestu nasazení](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) z rychlého startu, zobrazí se:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Výše vám umožní, aby modul Edge zapisoval protokoly do cesty úložiště (zařízení) "/var/local/MediaServices/". Pokud do modulu přidáte následující požadovanou vlastnost:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Modul pak bude zapisovat protokoly ladění v binárním formátu do cesty úložiště (zařízení)/var/local/MediaServices/debuglogs/, kterou můžete sdílet s podporou Azure.

## <a name="faq"></a>Nejčastější dotazy

[Nejčastější dotazy](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Další kroky

[Nepřetržité nahrávání videa](continuous-video-recording-tutorial.md)
