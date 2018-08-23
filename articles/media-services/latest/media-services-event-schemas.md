---
title: Azure Event Grid schémata událostí služby Media Services
description: Popisuje vlastnosti, které jsou k dispozici pro události služby Media Services pomocí služby Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054024"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Event Grid schémata událostí služby Media Services

Tento článek obsahuje schémata a vlastnosti pro události služby Media Services.

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj událostí Media Services](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Typy událostí k dispozici

Služba Media Services vydá následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Stav úlohy změní. |
| Microsoft.Media.LiveEventConnectionRejected | Pokus o připojení kodéru byl odmítnut. |
| Microsoft.Media.LiveEventEncoderConnected | Kodér naváže připojení se živá událost. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kodér odpojí. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Server mediálních datových zahodí datové dávky, protože je příliš pozdě nebo obsahuje překrývající se časové razítko (timestamp nové datové dávky je menší než koncový čas předchozího bloku dat). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Média server přijímá prvního bloku dat pro každou sledovat ve službě stream ani připojení. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Server mediálních datových zjistí zvuk a video streamy nejsou synchronizované. Použít jako varování, protože nemusí mít dopad na činnost koncového uživatele. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Server mediálních datových zjistí, že některý dvě videa datové proudy z externí kodér nejsou synchronizované. Použít jako varování, protože nemusí mít dopad na činnost koncového uživatele. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikuje každých 20 sekund pro každé stopu, když běží živá událost. Poskytuje ingestování shrnutí stavu. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Server mediálních datových detekuje diskontinuitu v příchozí směr. |

Existují dvě kategorie pro **Live** události: událostí na úrovni datového proudu a sledovat události. 

Stream úroveň události jsou vyvolány na datový proud nebo připojení. Každá událost `StreamId` parametr, který identifikuje připojení nebo datového proudu. Každý datový proud nebo připojení má jeden nebo více sleduje různé typy. Jedno připojení z kodéru může mít například jedna zvuková stopa a čtyři videa stopy. Typy datového proudu událostí jsou:

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Sledování události jsou vyvolány jednu stopu. Sledování událostí typy jsou:

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

Následující příklad ukazuje schématu **JobStateChange** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| previousState | řetězec | Stav úlohy před událostí. |
| state | řetězec | Nový stav úlohy se oznámení na tuto událost. Například "ve frontě: Úloha čeká na prostředky" nebo "naplánované: Úloha je připraven ke spuštění".|

Stav úlohy kde může být jedna z hodnot: *ve frontě*, *naplánované*, *zpracování*, *dokončeno*, *chyba*, *Zrušena*, *zrušení*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Následující příklad ukazuje schématu **LiveEventConnectionRejected** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| StreamId | řetězec | Identifikátor datového proudu nebo připojení. Přidat toto ID v adresu URL ingestování zodpovídá kodér nebo zákazníků. |  
| IngestUrl | řetězec | Ingestování adresy URL poskytnuté živé události. |  
| EncoderIp | řetězec | IP adresa z kodéru. |
| EncoderPort | řetězec | Port kodér, ve kterém je tento datový proud zapnout. |
| Kód výsledku | řetězec | Z důvodů, proč se požadavek na připojení odmítnut. V následující tabulce jsou uvedeny kódy výsledků. |

Kódy výsledků jsou:

| Kód výsledku | Popis |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Adresa URL ingestu nesprávné |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kodér IP není k dispozici v IP nakonfigurovat seznam povolených |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kodér neposlali metadata o datového proudu. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Kodek zadaný se nepodporuje. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Fragment obdrženo předtím, než přijímají a záhlaví pro tento datový proud. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Procento kvality zadaný překračuje maximální povolený limit. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Agregované s přenosovou rychlostí překračuje maximální povolený limit. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Časové razítko pro video nebo audio FLVTag není platné z RTMP kodér. |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

Následující příklad ukazuje schématu **LiveEventEncoderConnected** události: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| StreamId | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník zodpovídá za poskytování toto ID v adresu URL ingestování. |
| IngestUrl | řetězec | Ingestování adresy URL poskytnuté živé události. |
| EncoderIp | řetězec | IP adresa z kodéru. |
| EncoderPort | řetězec | Port kodér, ve kterém je tento datový proud zapnout. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Následující příklad ukazuje schématu **LiveEventEncoderDisconnected** události: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| StreamId | řetězec | Identifikátor datového proudu nebo připojení. Přidat toto ID v adresu URL ingestování zodpovídá kodér nebo zákazníků. |  
| IngestUrl | řetězec | Ingestování adresy URL poskytnuté živé události. |  
| EncoderIp | řetězec | IP adresa z kodéru. |
| EncoderPort | řetězec | Port kodér, ve kterém je tento datový proud zapnout. |
| Kód výsledku | řetězec | Důvod pro kodér odpojení. Může to být řádné odpojit nebo z chyby. V následující tabulce jsou uvedeny kódy výsledků. |

Kódy výsledků chyby jsou:

| Kód výsledku | Popis |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | Vypršel časový limit po povolený časový limit nečinnosti relace RTMP. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Časové razítko pro video nebo audio FLVTag není platné z RTMP kodér. |
| MPE_CAPACITY_LIMIT_REACHED | Kodér odesílat data příliš rychle. |
| Došlo k neznámé chybě kódy | Tyto kódy chyb může být v rozsahu od paměti chyba duplicitní položky v mapě hash. |

Kódy výsledků řádné odpojit jsou:

| Kód výsledku | Popis |
| ----------- | ----------- |
| S_OK | Kodér úspěšně odpojen. |
| MPE_CLIENT_TERMINATED_SESSION | Kodér odpojení (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodér odpojení (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Příkaz Obnovit kanál přijetí. |
| MPI_REST_API_CHANNEL_STOP | Kanál byl přijat příkaz stop. |
| MPI_REST_API_CHANNEL_STOP | Kanál probíhá údržba. |
| MPI_STREAM_HIT_EOF | Kodér odesílají streamu EOF. |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Následující příklad ukazuje schématu **LiveEventIncomingDataChunkDropped** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| TrackType | řetězec | Typ stopě (zvuk / Video). |
| TrackName | řetězec | Jeden směr určený název. |
| S přenosovou rychlostí | integer | S přenosovou rychlostí dráhy. |
| Časové razítko | řetězec | Časové razítko datové dávky vyřazen. |
| Časová osa | řetězec | Časové razítko. |
| Kód výsledku | řetězec | Z důvodu rozevírací bloku dat data. **FragmentDrop_OverlapTimestamp** nebo **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Následující příklad ukazuje schématu **LiveEventIncomingStreamReceived** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| TrackType | řetězec | Typ stopě (zvuk / Video). |
| TrackName | řetězec | Název stopě (buď pokud kodér nebo v případě RTMP server vygeneruje v *TrackType_Bitrate* formátu). |
| S přenosovou rychlostí | integer | S přenosovou rychlostí dráhy. |
| IngestUrl | řetězec | Ingestování adresy URL poskytnuté živé události. |
| EncoderIp | řetězec  | IP adresa z kodéru. |
| EncoderPort | řetězec | Port kodér, ve kterém je tento datový proud zapnout. |
| Časové razítko | řetězec | První časové razítko přijetí datové dávky. |
| Časová osa | řetězec | Časový rámec, ve kterém je reprezentován časové razítko. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Následující příklad ukazuje schématu **LiveEventIncomingStreamsOutOfSync** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| MinLastTimestamp | řetězec | Minimální poslední časové razítko mezi všechny stopy (zvuku nebo videa). |
| TypeOfTrackWithMinLastTimestamp | řetězec | Typ sledování (zvuku nebo videa) s minimální poslední časové razítko. |
| MaxLastTimestamp | řetězec | Maximální počet časová razítka mezi všechny stopy (zvuku nebo videa). |
| TypeOfTrackWithMaxLastTimestamp | řetězec | Typ sledování (zvuku nebo videa) s maximální poslední časové razítko. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Následující příklad ukazuje schématu **LiveEventIncomingVideoStreamsOutOfSync** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| FirstTimestamp | řetězec | Časové razítko přijetí pro jednu z úrovní sleduje/kvality typu videa. |
| FirstDuration | řetězec | Doba trvání bloků dat s první časové razítko. |
| SecondTimestamp | řetězec  | Časové razítko přijetí pro některé další úroveň sledování/kvality typu videa. |
| SecondDuration | řetězec | Doba trvání bloků dat s druhé časové razítko. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Následující příklad ukazuje schématu **LiveEventIngestHeartbeat** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| TrackType | řetězec | Typ stopě (zvuk / Video). |
| TrackName | řetězec | Název stopě (buď pokud kodér nebo v případě RTMP server vygeneruje v *TrackType_Bitrate* formátu). |
| S přenosovou rychlostí | integer | S přenosovou rychlostí dráhy. |
| IncomingBitrate | integer | Vypočtený s přenosovou rychlostí podle bloků dat z kodéru. |
| LastTimestamp | řetězec | Poslední časové razítko přijetí pro sledování v posledních 20 sekund. |
| Časová osa | řetězec | Časový rámec, ve kterém jsou vyjádřeny časová razítka. |
| OverlapCount | integer | Počet bloků dat měli překrytých časová razítka v posledních 20 sekund. |
| DiscontinuityCount | integer | Počet nespojitosti zjištěnými v posledních 20 sekund. |
| NonIncreasingCount | integer | Počet bloků dat s časovými razítky v minulosti byly přijaty v posledních 20 sekund. |
| UnexpectedBitrate | BOOL | Pokud očekávaných a aktuálních přenosových rychlostí lišit o více než povolený limit v posledních 20 sekund. Je hodnota true v případě a pouze tehdy, pokud IncomingBitrate > = 2 * s přenosovou rychlostí nebo IncomingBitrate < = nebo IncomingBitrate s přenosovou rychlostí/2 = 0. |
| Stav | řetězec | Stav živé události. |
| V pořádku | BOOL | Označuje, zda ingestování je v pořádku na základě počtu a příznaky. V pořádku má hodnotu true Pokud OverlapCount = 0 & & DiscontinuityCount = 0 & & NonIncreasingCount = 0 & & UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

Následující příklad ukazuje schématu **LiveEventTrackDiscontinuityDetected** události: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| TrackType | řetězec | Typ stopě (zvuk / Video). |
| TrackName | řetězec | Název stopě (buď pokud kodér nebo v případě RTMP server vygeneruje v *TrackType_Bitrate* formátu). |
| S přenosovou rychlostí | integer | S přenosovou rychlostí dráhy. |
| PreviousTimestamp | řetězec | Časové razítko předchozí fragment. |
| NewTimestamp | řetězec | Časové razítko aktuální fragmentu. |
| DiscontinuityGap | řetězec | Mezera mezi nad dva časová razítka. |
| Časová osa | řetězec | Jsou reprezentovány časový rámec, ve které časovým razítkem a diskontinuitu mezera. |

## <a name="common-event-properties"></a>Společných vlastností události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | EventGrid tématu. Tato vlastnost má ID prostředku účtu Media Services. |
| Předmět | řetězec | Cesta prostředku pro kanál služby Media Services v rámci účtu Media Services. Zřetězení tématu a předmět uveďte jste prostředek ID pro úlohu. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. Například "Microsoft.Media.JobStateChange". |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data událostí Media Services. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

## <a name="next-steps"></a>Další postup

[Zaregistrujte se na události změny stavu úlohy](job-state-events-cli-how-to.md)