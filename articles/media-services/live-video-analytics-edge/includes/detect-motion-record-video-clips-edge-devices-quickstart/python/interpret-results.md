---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682175"
---
Když spustíte graf médií, výsledky z uzlu procesoru snímače pohybu procházejí uzlem IoT Hub jímky do služby IoT Hub. Zprávy, které vidíte v okně **výstup** Visual Studio Code obsahují `body` oddíl a `applicationProperties` oddíl. Další informace najdete v tématu [Vytvoření a čtení zpráv IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

V následujících zprávách modul Live video Analytics definuje vlastnosti aplikace a obsah těla.

### <a name="mediasessionestablished-event"></a>Událost MediaSessionEstablished

Po vytvoření instance mediálního grafu se zdrojový uzel RTSP pokusí připojit k serveru RTSP, který běží na kontejneru rtspsim-live555. Pokud je připojení úspěšné, bude vytištěna následující událost.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

V předchozím výstupu: 

* Zpráva je událost diagnostiky, `MediaSessionEstablished` . Indikuje, že zdrojový uzel RTSP (předmět) navázal připojení k simulátoru RTSP a začal přijímat (simulované) živé kanály.
* V `applicationProperties` aplikaci `subject` odkazuje na uzel v topologii grafu, ze které byla zpráva generována. V tomto případě zpráva pochází ze zdrojového uzlu RTSP.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je událost diagnostiky.
* `eventTime`Hodnota je čas, kdy došlo k události.
* `body`Oddíl obsahuje data o diagnostickou událost. V tomto případě data obsahují podrobnosti [protokolu SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="recordingstarted-event"></a>Událost RecordingStarted

Když se zjistí pohyb, aktivuje se uzel procesoru signálu signálu a v mediálním grafu se zahájí zápis souboru MP4. Uzel jímky souborů odesílá provozní událost. `type`Je nastavena na hodnotu `motion` , která označuje, že se jedná o výsledek procesoru detekce pohybu. `eventTime`Hodnota je čas UTC, kdy došlo k pohybu. Další informace o tomto procesu najdete v části [Přehled](#overview) v tomto rychlém startu.

Tady je příklad této zprávy:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

V předchozí zprávě: 

* V `applicationProperties` aplikaci `subject` odkazuje na uzel v grafu médií, ze kterého byla zpráva generována. V tomto případě zpráva pochází z uzlu jímky souborů.
* V `applicationProperties` nástroji `eventType` označuje, že tato událost je funkční.
* `eventTime`Hodnota je čas, kdy došlo k události. Tato doba je 5 až 6 sekund po `MediaSessionEstablished` a po spuštění videa se spustí Flow. Tato doba odpovídá označení 5 až 6 sekund, když se [auto začalo přesunout](#review-the-sample-video) do parkovací dávky.
* `body`Oddíl obsahuje data o provozní události. V tomto případě data obsahují `outputType` a `outputLocation` .
* `outputType`Proměnná označuje, že tyto informace se týkají cesty k souboru.
* `outputLocation`Hodnota je umístění souboru MP4 v modulu Edge.

### <a name="recordingstopped-and-recordingavailable-events"></a>Události RecordingStopped a RecordingAvailable

Pokud provedete kontrolu vlastností uzlu procesoru brány signálu v [topologii grafu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), uvidíte, že časy aktivace jsou nastavené na 5 sekund. Přibližně 5 sekund po `RecordingStarted` přijetí události získáte:

* `RecordingStopped`Událost, která označuje, že záznam byl zastaven.
* `RecordingAvailable`Událost, která označuje, že soubor MP4 se teď dá použít k zobrazení.

Dvě události se obvykle generují během několika sekund.
