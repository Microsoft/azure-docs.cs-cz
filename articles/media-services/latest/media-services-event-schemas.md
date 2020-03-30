---
title: Události schémat azure event grid pro mediální služby
description: Popisuje vlastnosti, které jsou k dispozici pro události Media Services s Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: d4a206bbddedfe9f23a943df27c6ac4b5fe17e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251346"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Události schémat azure event grid pro mediální služby

Tento článek obsahuje schémata a vlastnosti událostí služby Media Services.

Seznam ukázkových skriptů a kurzů naleznete v tématu [Media Services event source](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Typy událostí souvisejících s úlohou

Media Services vyzařuje níže popsané typy událostí související s **úlohou.** Existují dvě kategorie pro události související s **úlohou:** "Sledování změn stavu úlohy" a "Sledování změn stavu výstupu úlohy". 

Můžete se zaregistrovat pro všechny události přihlášením k odběru události JobStateChange. Nebo se můžete přihlásit pouze pro určité události (například konečné stavy jako JobErrored, JobFinished a JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Sledování změn stavu úlohy

| Typ události | Popis |
| ---------- | ----------- |
| Změna stavu Microsoft.Media.JobState| Získejte událost pro všechny změny stavu úlohy. |
| Microsoft.Media.JobNascheduled| Získejte událost, když úloha přejde do naplánovaného stavu. |
| Microsoft.Media.JobProcessing| Získejte událost při úlohy přechody do stavu zpracování. |
| Microsoft.Media.JobZrušení| Získejte událost, když úloha přejde do stavu zrušení. |
| Microsoft.Media.JobDokončeno| Získejte událost, když úloha přejde do stavu dokončení. Toto je konečný stav, který zahrnuje výstupy úlohy.|
| Microsoft.Media.JobZrušeno| Získejte událost, když úloha přejde do zrušeného stavu. Toto je konečný stav, který zahrnuje výstupy úlohy.|
| Microsoft.Media.JobErrored| Získejte událost, když úloha přejde do chybového stavu. Toto je konečný stav, který zahrnuje výstupy úlohy.|

Viz [Příklady schématu,](#event-schema-examples) které následují.

### <a name="monitoring-job-output-state-changes"></a>Sledování změn stavu výstupu úlohy

Úloha může obsahovat více výstupů úlohy (pokud jste nakonfigurovali transformaci tak, aby měla více výstupů úlohy.) Pokud chcete sledovat podrobnosti o výstupu jednotlivé úlohy, poslouchejte událost změny výstupu úlohy.

Každá **úloha** bude na vyšší úrovni než **JobOutput**, takže události výstupu úlohy jsou aktivovány uvnitř odpovídající úlohy. 

Chybové zprávy `JobFinished` `JobCanceled`v `JobError` , , výstup agregované výsledky pro každý výstup úlohy – po dokončení všech. Vzhledem k tomu, výstup úlohy události požáru jako každý úkol dokončí. Například pokud máte výstup kódování, následovaný výstupem Video Analytics, by se dvě události spouštění jako události výstupu úlohy před konečné JobFinished události požáry s agregovaná data.

| Typ události | Popis |
| ---------- | ----------- |
| Změna stavu Microsoft.Media.JobOutputState| Získejte událost pro všechny změny stavu výstupu úlohy. |
| Microsoft.Media.JobOutputNascheduled| Získejte událost, když výstup úlohy přejde do naplánovaného stavu. |
| Microsoft.Media.JobOutputProcessing| Získejte událost, když výstup úlohy přejde do stavu zpracování. |
| Microsoft.Media.JobOutputZrušení| Získejte událost, když výstup úlohy přejde do stavu zrušení.|
| Microsoft.Media.JobOutputDokončeno| Získejte událost, když výstup úlohy přejde do stavu dokončení.|
| Microsoft.Media.JobOutputZrušeno| Získejte událost, když výstup úlohy přejde do zrušeného stavu.|
| Microsoft.Media.JobOutputChyba| Získejte událost, když výstup úlohy přejde do chybového stavu.|

Viz [Příklady schématu,](#event-schema-examples) které následují.

### <a name="monitoring-job-output-progress"></a>Sledování průběhu výstupu úlohy

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Media.JobOutputPrůběh| Tato událost odráží průběh zpracování úlohy z 0 % na 100 %. Služba se pokusí odeslat událost, pokud došlo k 5 % nebo větší zvýšení hodnoty průběhu nebo bylo více než 30 sekund od poslední události (prezenční signál). Hodnota průběhu není zaručeno, že začínat na 0 % nebo dosáhnout 100 %, ani není zaručeno, že zvýšení konstantní rychlostí v průběhu času. Tato událost by neměla být použita k určení, že zpracování bylo dokončeno – místo toho byste měli použít události změny stavu.|

Viz [Příklady schématu,](#event-schema-examples) které následují.

## <a name="live-event-types"></a>Typy živých událostí

Media Services také vyzařuje typy **živých** událostí popsané níže. Existují dvě kategorie pro **živé** události: události na úrovni datového proudu a události na úrovni sledování. 

### <a name="stream-level-events"></a>Události na úrovni datového proudu

Události na úrovni datového proudu jsou vyvolány na datový proud nebo připojení. Každá událost `StreamId` má parametr, který identifikuje připojení nebo datový proud. Každý datový proud nebo připojení má jednu nebo více stop různých typů. Například jedno připojení z kodéru může mít jednu zvukovou stopu a čtyři stopy videa. Typy událostí datového proudu jsou:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionOdmítnuto | Pokus o připojení kodéru je odmítnut. |
| Microsoft.Media.LiveEventEncoderPřipojeno | Kodér naváže spojení s živou událostí. |
| Microsoft.Media.LiveEventEncoderOdpojen | Kodér se odpojí. |

Viz [Příklady schématu,](#event-schema-examples) které následují.

### <a name="track-level-events"></a>Události na úrovni sledování

Události na úrovni stopy jsou vyvolány na stopu. 

> [!NOTE]
> Všechny události na úrovni trati jsou vyvolány po připojení živého kodéru.

Typy událostí na úrovni sledování jsou:

| Typ události | Popis |
| ---------- | ----------- |
| Soubor Microsoft.Media.LiveEventIncomingDataChunkDropped | Mediální server přeruší datový blok, protože je příliš pozdě nebo má překrývající se časové razítko (časové razítko nového datového bloku je menší než koncový čas předchozího datového bloku). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Mediální server přijímá první datový blok pro každou stopu v datovém proudu nebo připojení. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Mediální server zjistí, že datové proudy zvuku a videa nejsou synchronizovány. Použijte jako upozornění, protože uživatelské prostředí nemusí být ovlivněno. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Mediální server zjistí, že některý ze dvou datových proudů videa přicházejících z externího kodéru není synchronizován. Použijte jako upozornění, protože uživatelské prostředí nemusí být ovlivněno. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikováno každých 20 sekund pro každou stopu, když běží živá událost. Poskytuje souhrn stavu ingestování.<br/><br/>Po počátečním připojení kodéru událost srdečního tepu pokračuje v yemit každých 20 sekund bez ohledu na to, zda je kodér stále připojen či nikoli. |
| Zjištěna zjištěna informace o microsoft.media.LiveEventDiscontinuity | Mediální server detekuje nespojitost v příchozí stopě. |

Viz [Příklady schématu,](#event-schema-examples) které následují.

## <a name="event-schema-examples"></a>Příklady schématu událostí

### <a name="jobstatechange"></a>Změna stavu úlohy

Následující příklad ukazuje schéma události **JobStateChange:** 

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
| předchozístát | řetězec | Stav úlohy před událostí. |
| state | řetězec | Nový stav úlohy, která je v tomto případě oznámena. Například "Naplánováno: Úloha je připravena ke spuštění" nebo "Dokončeno: Úloha je dokončena" .|

Kde může být stav úlohy jednou z hodnot: *Zařazeno do fronty*, *Naplánováno*, *Zpracování*, *Dokončeno*, *Chyba*, *Zrušeno*, *Zrušení*

> [!NOTE]
> *Zařazeno* do fronty bude k dispozici pouze ve vlastnosti **previousState,** ale ne ve vlastnosti **state.**

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Pro každou změnu stavu nefinal úlohy (například JobScheduled, JobProcessing, JobCanceling) vypadá ukázkové schéma podobně jako následující:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>Dokončena úloha, ÚlohaByla zrušena, Úloha Byla chybová

Pro každou konečnou změnu stavu úlohy (například JobFinished, JobCanceled, JobErrored) vypadá ukázkové schéma podobně jako následující:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Výstupy | Pole | Získá výstupy úlohy.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

Následující příklad ukazuje schéma události **JobOutputStateChange:**

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Pro každou změnu stavu JobOutput vypadá ukázkové schéma podobně jako následující:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Příklad schématu vypadá podobně jako následující:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>Připojení LiveEvent bylo odmítnuto.

Následující příklad ukazuje schéma události **LiveEventConnectionRejected:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| streamId | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník je zodpovědný za přidání tohoto ID do adresy URL ingestování. |  
| ingesturl | řetězec | Ingestovat adresu URL poskytovanou živou událostí. |  
| encoderIp | řetězec | IP kód. |
| kodérPort | řetězec | Přístav kodéru, odkud tento proud přichází. |
| resultCode | řetězec | Důvod, proč bylo připojení odmítnuto. Kódy výsledků jsou uvedeny v následující tabulce. |

Kódy výsledků chyb najdete v [kódech chyb živých událostí](live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderPřipojeno

Následující příklad ukazuje schéma události **LiveEventEncoderConnected:** 

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
| streamId | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník je zodpovědný za poskytnutí tohoto ID v adrese URL ingestování. |
| ingesturl | řetězec | Ingestovat adresu URL poskytovanou živou událostí. |
| encoderIp | řetězec | IP kód. |
| kodérPort | řetězec | Přístav kodéru, odkud tento proud přichází. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderOdpojen

Následující příklad ukazuje schéma události **LiveEventEncoderDisconnected:** 

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
| streamId | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník je zodpovědný za přidání tohoto ID do adresy URL ingestování. |  
| ingesturl | řetězec | Ingestovat adresu URL poskytovanou živou událostí. |  
| encoderIp | řetězec | IP kód. |
| kodérPort | řetězec | Přístav kodéru, odkud tento proud přichází. |
| resultCode | řetězec | Důvod odpojení kodéru. Může to být řádné odpojení nebo od chyby. Kódy výsledků jsou uvedeny v následující tabulce. |

Kódy výsledků chyb najdete v [kódech chyb živých událostí](live-event-error-codes.md).

Řádné odpojit výsledek kódy jsou:

| Kód výsledku | Popis |
| ----------- | ----------- |
| S_OK | Kodér byl úspěšně odpojen. |
| MPE_CLIENT_TERMINATED_SESSION | Kodér odpojen (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodér odpojen (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Byl přijat příkaz pro obnovení kanálu. |
| MPI_REST_API_CHANNEL_STOP | Byl přijat příkaz zastavení kanálu. |
| MPI_REST_API_CHANNEL_STOP | Kanál prochází údržbou. |
| MPI_STREAM_HIT_EOF | Datový proud EOF je odeslán kodérem. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Následující příklad ukazuje schéma události **LiveEventIncomingDataChunkDropped:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| trackType | řetězec | Typ stopy (Audio / Video). |
| trackName | řetězec | Název stopy. |
| Datový tok | celé číslo | Malý tok stopy. |
| časové razítko | řetězec | Časové razítko datového bloku kleslo. |
| Časové osy | řetězec | Časová osa časového razítka. |
| resultCode | řetězec | Důvod poklesu datového bloku. **FragmentDrop_OverlapTimestamp** nebo **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Následující příklad ukazuje schéma události **LiveEventIncomingStreamReceived:** 

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
| trackType | řetězec | Typ stopy (Audio / Video). |
| trackName | řetězec | Název stopy (buď dodaný kodérem, nebo, v případě RTMP, server generuje v *TrackType_Bitrate* formátu). |
| Datový tok | celé číslo | Malý tok stopy. |
| ingesturl | řetězec | Ingestovat adresu URL poskytovanou živou událostí. |
| encoderIp | řetězec  | IP kód. |
| kodérPort | řetězec | Přístav kodéru, odkud tento proud přichází. |
| časové razítko | řetězec | První časové razítko přijatého datového bloku. |
| Časové osy | řetězec | Časová osa, ve které je znázorněno časové razítko. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Následující příklad ukazuje schéma události **LiveEventIncomingStreamsOutOfSync:** 

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
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| minLastTimerazítk | řetězec | Minimální poslední časová razítka mezi všemi stopami (audio nebo video). |
| typeOfTrackWithMinLastTimestamp | řetězec | Typ stopy (zvuk nebo video) s minimálním posledním časovým razítkem. |
| maxLastTimerazítko | řetězec | Maximálně všechna časová razítka mezi všemi stopami (audio nebo video). |
| typeOfTrackWithMaxLastTimestamp | řetězec | Typ stopy (zvuk nebo video) s maximálním posledním časovým razítkem. |
| timescaleOfMinLastTimerazítk| řetězec | Získá časové osy, ve kterém je reprezentován "MinLastTimestamp" .|
| timescaleOfMaxLastTimerazítko| řetězec | Získá časové osy, ve kterém je reprezentován "MaxLastTimestamp" .|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Následující příklad ukazuje schéma události **LiveEventIncomingVideoStreamsOutOfSync:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
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
| firstTimestamp | řetězec | Časové razítko přijaté pro jednu ze skladeb / úrovně kvality typu videa. |
| firstDuration | řetězec | Doba trvání datového bloku s prvním časovým razítkem. |
| secondTimestamp | řetězec  | Časové razítko přijaté pro některé další skladby / úrovně kvality typu videa. |
| secondDuration | řetězec | Doba trvání datového bloku s druhým časovým razítkem. |
| Časové osy | řetězec | Časová osa časových razítek a doba trvání.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Následující příklad ukazuje schéma události **LiveEventIngestHeartbeat:** 

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
| trackType | řetězec | Typ stopy (Audio / Video). |
| trackName | řetězec | Název stopy (buď dodaný kodérem, nebo, v případě RTMP, server generuje v *TrackType_Bitrate* formátu). |
| Datový tok | celé číslo | Malý tok stopy. |
| incomingBitrate | celé číslo | Vypočtený datový tok založený na datových blocích pocházejících z kodéru. |
| lastTimestamp | řetězec | Poslední časové razítko přijaté pro stopu za posledních 20 sekund. |
| Časové osy | řetězec | Časová osa, ve které jsou vyjádřena časová razítka. |
| overlapCountCount | celé číslo | Počet datových bloků překrýval časová razítka za posledních 20 sekund. |
| počet discontinuityCount | celé číslo | Počet nespojitostí pozorovaných za posledních 20 sekund. |
| nonIncreasingCount | celé číslo | Počet datových bloků s časovými razítky v minulosti byl přijat za posledních 20 sekund. |
| neočekávanábitová rychlost | bool | Pokud se očekává a skutečné přenosové rychlosti se liší o více než povolený limit za posledních 20 sekund. Je to pravda, pokud a pouze pokud, incomingBitrate >= 2 * přenosová rychlost NEBO incomingBitrate <= přenosová rychlost/2 NEBO PříchozíBitrate = 0. |
| state | řetězec | Stav živé události. |
| Zdravé | bool | Označuje, zda ingestování je v pořádku na základě počty a příznaky. V pořádku je true, pokud overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>Detekce přenosu EventTrackDiscontinuity

Následující příklad ukazuje schéma události **LiveEventTrackDiscontinuityDetected:** 

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
| trackType | řetězec | Typ stopy (Audio / Video). |
| trackName | řetězec | Název stopy (buď dodaný kodérem, nebo, v případě RTMP, server generuje v *TrackType_Bitrate* formátu). |
| Datový tok | celé číslo | Malý tok stopy. |
| předchozíčasové razítko | řetězec | Časové razítko předchozífragment. |
| nové časové razítko | řetězec | Časové razítko aktuálního fragmentu. |
| discontinuityGap | řetězec | Mezera mezi nad dvěma časovými razítky. |
| Časové osy | řetězec | Časová osa, ve které jsou zastoupeny mezery časového razítka a diskontinuity. |

### <a name="common-event-properties"></a>Běžné vlastnosti událostí

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Téma EventGrid. Tato vlastnost má ID prostředku pro účet Mediální služby. |
| Předmět | řetězec | Cesta k prostředkům pro kanál Mediální služby v rámci účtu Mediální služby. Zřetězení tématu a předmětu vám poskytne ID prostředku pro úlohu. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. Například "Microsoft.Media.JobStateChange". |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí služby Media Services. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

## <a name="next-steps"></a>Další kroky

[Registrovat se pro události změny stavu úlohy](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Viz také

- [Sada EventGrid .NET SDK, která zahrnuje události služby Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definice událostí mediálních služeb](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Kódy chyb živých událostí](live-event-error-codes.md)
