---
title: Azure Media Services jako zdroj Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro Media Services události s Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b5772a2332e1864d0b8df0d4e102006b29b6a61e
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120108"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Azure Media Services jako zdroj Event Grid

Tento článek poskytuje schémata a vlastnosti pro Media Services události.

## <a name="job-related-event-types"></a>Typy událostí související s úlohou

Media Services emituje typy událostí **související s úlohou**  popsaných níže. Pro události **související s úlohou** jsou k dispozici dvě kategorie: "monitorování změn stavu úlohy" a "změny stavu výstupu úlohy monitorování". 

Pro všechny události se můžete zaregistrovat přihlášením k odběru události JobStateChange. Nebo se můžete přihlásit k odběru pouze pro konkrétní události (například poslední stavy, jako je JobErrored, JobFinished a JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitorování změn stavu úlohy

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Media. JobStateChange| Získá událost pro všechny změny stavu úlohy. |
| Microsoft. Media. JobScheduled| Při přechodu úlohy do plánovaného stavu získat událost. |
| Microsoft. Media. JobProcessing| Získá událost při přechodu úlohy do stavu zpracování. |
| Microsoft. Media. JobCanceling| Získá událost, když se přechody úlohy na stav zrušení. |
| Microsoft. Media. JobCanceled| Získá událost při přechodu úlohy do stavu zrušeno. Toto je konečný stav, který obsahuje výstupy úlohy.|
| Microsoft. Media. JobErrored | Získá událost při přechodu úlohy do chybového stavu. Toto je konečný stav, který obsahuje výstupy úlohy.|

Podívejte se na [Příklady schématu](#event-schema-examples) , které následují.

### <a name="monitoring-job-output-state-changes"></a>Sledování změn stavu výstupu úlohy

Úloha může obsahovat více výstupů úlohy (Pokud jste nakonfigurovali transformaci na více výstupů úloh). Pokud chcete sledovat podrobnosti o výstupu jednotlivých úloh, naslouchat události změny výstupu úlohy.

Každá **úloha** bude na vyšší úrovni než **JobOutput**, takže se události výstupu úlohy vystaví uvnitř odpovídající úlohy. 

Chybové zprávy v nástroji vypočítávají `JobFinished` `JobCanceled` `JobError` agregované výsledky pro každý výstup úlohy – až budou všechny dokončeny. Zatímco výstupní události úlohy se aktivují při dokončení každé úlohy. Například pokud máte výstup kódování následovaný výstupem video Analytics, získáte dvě události, které se aktivují jako události výstupu úlohy před tím, než se konečná událost JobFinished aktivuje s agregovanými daty.

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Media. JobOutputStateChange| Získá událost pro všechny změny stavu výstupu úlohy. |
| Microsoft. Media. JobOutputScheduled| Získá událost, když se výstupy úlohy do plánovaného stavu. |
| Microsoft. Media. JobOutputProcessing| Získá událost při přechodu výstupu úlohy do stavu zpracování. |
| Microsoft. Media. JobOutputCanceling| Získat událost při přechodu výstupů úlohy do stavu zrušení.|
| Microsoft. Media. JobOutputFinished| Získá událost, když se výstupy úlohy změní do stavu dokončeno.|
| Microsoft. Media. JobOutputCanceled| Získá událost, když se výstup úlohy změní do stavu zrušeno.|
| Microsoft. Media. JobOutputErrored| Získá událost, když výstup úlohy přejde do chybového stavu.|

Podívejte se na [Příklady schématu](#event-schema-examples) , které následují.

### <a name="monitoring-job-output-progress"></a>Průběh monitorování výstupu úlohy

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Media. JobOutputProgress| Tato událost odráží průběh zpracování úlohy, od 0 do 100%. Služba se pokusí odeslat událost, pokud došlo k 5% nebo většímu nárůstu hodnoty průběhu nebo je více než 30 sekund od poslední události (prezenční signál). Hodnota průběh není zaručena spuštění na 0% nebo pro dosažení 100%, ani během času není zaručeno zvýšení konstantní frekvence. Tato událost by se neměla používat k určení, zda bylo zpracování dokončeno – místo toho byste měli použít události změny stavu.|

Podívejte se na [Příklady schématu](#event-schema-examples) , které následují.

## <a name="live-event-types"></a>Typy živých událostí

Media Services také emituje typy **živých** událostí popsané níže. Existují dvě kategorie pro **živé** události: události na úrovni datového proudu a události na úrovni sledování. 

### <a name="stream-level-events"></a>Události na úrovni streamu

Události na úrovni proudu se vyvolávají na datový proud nebo připojení. Každá událost má `StreamId` parametr, který identifikuje připojení nebo datový proud. Každý datový proud nebo připojení má jednu nebo více skladeb různých typů. Například jedno připojení z kodéru může mít jednu zvukovou stopu a čtyři videozáznamy. Typy událostí streamu jsou:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Media. LiveEventConnectionRejected | Pokus o připojení kodéru byl odmítnut. |
| Microsoft. Media. LiveEventEncoderConnected | Kodér vytvoří připojení s živou událostí. |
| Microsoft. Media. LiveEventEncoderDisconnected | Kodér se odpojí. |

Podívejte se na [Příklady schématu](#event-schema-examples) , které následují.

### <a name="track-level-events"></a>Události na úrovni sledování

Události na úrovni sledování jsou vyvolány na základě stopy. 

> [!NOTE]
> Po připojení živého kodéru jsou vyvolány všechny události na úrovni sledování.

Typy událostí na úrovni sledování:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Media. LiveEventIncomingDataChunkDropped | Media Server vynechá datový blok dat, protože je moc pozdě nebo má překrývající se časové razítko (časové razítko nového bloku dat je menší než koncový čas předchozího bloku dat). |
| Microsoft. Media. LiveEventIncomingStreamReceived | Media Server obdrží první datový blok pro každou stopu v datovém proudu nebo připojení. |
| Microsoft. Media. LiveEventIncomingStreamsOutOfSync | Media Server detekuje audio a video streamy nejsou synchronizované. Používejte jako upozornění, protože uživatelské prostředí nemusí být ovlivněno. |
| Microsoft. Media. LiveEventIncomingVideoStreamsOutOfSync | Media Server detekuje, že některé ze dvou datových proudů videa přicházejících z externího kodéru nejsou synchronizované. Používejte jako upozornění, protože uživatelské prostředí nemusí být ovlivněno. |
| Microsoft. Media. LiveEventIngestHeartbeat | Publikováno každé 20 sekund pro každou stopu, když je spuštěná živá událost. Poskytuje souhrn stavu ingestování.<br/><br/>Po prvním připojení kodéru bude událost prezenčního signálu nadále vygenerována každých 20 sekund, zda je kodér stále připojen nebo ne. |
| Microsoft. Media. LiveEventTrackDiscontinuityDetected | Mediální server detekuje v příchozím stopě nekontinuitu. |

Podívejte se na [Příklady schématu](#event-schema-examples) , které následují.

## <a name="event-schema-examples"></a>Příklady schématu událostí

### <a name="jobstatechange"></a>JobStateChange

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **JobStateChange** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **JobStateChange** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "type": "Microsoft.Media.JobStateChange",
    "time": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `previousState` | řetězec | Stav úlohy před událostí. |
| `state` | řetězec | Nový stav úlohy, která se v této události oznamuje. Například "naplánované: úloha je připravena k zahájení" nebo "dokončeno: úloha je dokončena".|

Kde může být stav úlohy jedna z hodnot: *Queued*, *Scheduled*, *Processing*, *Finished*, *Error*, *Canceled*, *Canceled*

> [!NOTE]
> *Ve frontě* se bude vyskytovat pouze ve vlastnosti **previousState** , ale ne ve vlastnosti **State** .

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Pro každou nekonečnou změnu stavu úlohy (například JobScheduled, JobProcessing, JobCanceling) vypadá příklad schématu podobně jako v následujícím příkladu:

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

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Ukázkové schéma pro každou poslední změnu stavu úlohy (například JobFinished, JobCanceled, JobErrored) vypadá podobně jako v následujícím příkladu:

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Pro každou nekonečnou změnu stavu úlohy (například JobScheduled, JobProcessing, JobCanceling) vypadá příklad schématu podobně jako v následujícím příkladu:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobProcessing",
  "time": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Ukázkové schéma pro každou poslední změnu stavu úlohy (například JobFinished, JobCanceled, JobErrored) vypadá podobně jako v následujícím příkladu:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobFinished",
  "time": "2018-10-12T16:25:56.4115495",
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
  "specversion": "1.0"
}]
```

---


Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `outputs` | Pole | Získá výstupy úlohy.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **JobOutputStateChange** :

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

Pro každou změnu stavu JobOutput vypadá ukázkové schéma podobně jako v následujícím příkladu:

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

Příklad schématu vypadá nějak takto:

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

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Následující příklad ukazuje schéma události **LiveEventConnectionRejected** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **JobOutputStateChange** :

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobOutputStateChange",
  "time": "2018-10-12T16:25:56.0242854",
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
  "specversion": "1.0"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Pro každou změnu stavu JobOutput vypadá ukázkové schéma podobně jako v následujícím příkladu:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobOutputProcessing",
  "time": "2018-10-12T16:12:18.0061141",
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
  "specversion": "1.0"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Příklad schématu vypadá nějak takto:

 ```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "type": "Microsoft.Media.JobOutputProgress",
  "time": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "specversion": "1.0"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Následující příklad ukazuje schéma události **LiveEventConnectionRejected** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventConnectionRejected",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "specversion": "1.0"
  }
]
```

---


Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `streamId` | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník je zodpovědný za přidání tohoto ID do adresy URL ingestování. |  
| `ingestUrl` | řetězec | Adresa URL příjmu poskytovaná živou událostí |  
| `encoderIp` | řetězec | IP adresa kodéru. |
| `encoderPort` | řetězec | Port kodéru, ze kterého přichází tento datový proud. |
| `resultCode` | řetězec | Důvod zamítnutí připojení. Kódy výsledku jsou uvedeny v následující tabulce. |

Kódy výsledku chyby můžete najít v části [kódy chyb živé události](../media-services/latest/live-event-error-codes-reference.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventEncoderConnected** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventEncoderConnected** : 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderConnected",
    "time": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `streamId` | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník zodpovídá za poskytnutí tohoto ID v adrese URL ingestování. |
| `ingestUrl` | řetězec | Adresa URL příjmu poskytovaná živou událostí |
| `encoderIp` | řetězec | IP adresa kodéru. |
| `encoderPort` | řetězec | Port kodéru, ze kterého přichází tento datový proud. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventEncoderDisconnected** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventEncoderDisconnected** : 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderDisconnected",
    "time": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `streamId` | řetězec | Identifikátor datového proudu nebo připojení. Kodér nebo zákazník je zodpovědný za přidání tohoto ID do adresy URL ingestování. |  
| `ingestUrl` | řetězec | Adresa URL příjmu poskytovaná živou událostí |  
| `encoderIp` | řetězec | IP adresa kodéru. |
| `encoderPort` | řetězec | Port kodéru, ze kterého přichází tento datový proud. |
| `resultCode` | řetězec | Důvod odpojení kodéru Může být bezproblémové odpojení nebo chyba. Kódy výsledku jsou uvedeny v následující tabulce. |

Kódy výsledku chyby můžete najít v části [kódy chyb živé události](../media-services/latest/live-event-error-codes-reference.md).

Kódy výsledků řádného odpojení:

| Kód výsledku | Description |
| ----------- | ----------- |
| S_OK | Kodér byl úspěšně odpojen. |
| MPE_CLIENT_TERMINATED_SESSION | Kodér byl odpojen (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodér byl odpojen (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Byl přijat příkaz pro obnovení kanálu. |
| MPI_REST_API_CHANNEL_STOP | Byl přijat příkaz k zastavení kanálu. |
| MPI_REST_API_CHANNEL_STOP | Probíhá údržba kanálu. |
| MPI_STREAM_HIT_EOF | Kodér posílá datový proud EOF. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingDataChunkDropped** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingDataChunkDropped** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `trackType` | řetězec | Typ stopy (zvuk/video) |
| `trackName` | řetězec | Název stopy |
| `bitrate` | integer | Přenosová rychlost stopy |
| `timestamp` | řetězec | Časové razítko přehozených bloků dat |
| `timescale` | řetězec | Časová osa časového razítka. |
| `resultCode` | řetězec | Důvod přetažení datového bloku **FragmentDrop_OverlapTimestamp** nebo **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingStreamReceived** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingStreamReceived** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "time": "2018-08-07T23:08:10.5069288Z",
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
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `trackType` | řetězec | Typ stopy (zvuk/video) |
| `trackName` | řetězec | Název stopy (poskytnutý kodérem nebo v případě RTMP vygeneruje Server ve formátu *TrackType_Bitrate* ). |
| `bitrate` | integer | Přenosová rychlost stopy |
| `ingestUrl` | řetězec | Adresa URL příjmu poskytovaná živou událostí |
| `encoderIp` | řetězec  | IP adresa kodéru. |
| `encoderPort` | řetězec | Port kodéru, ze kterého přichází tento datový proud. |
| `timestamp` | řetězec | První časové razítko přijatého datového bloku |
| `timescale` | řetězec | Časový rozvrh, ve kterém je znázorněno časové razítko |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingStreamsOutOfSync** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingStreamsOutOfSync** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "time": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `minLastTimestamp` | řetězec | Minimální počet posledních časových razítek mezi všemi skladbami (zvuk nebo video). |
| `typeOfTrackWithMinLastTimestamp` | řetězec | Typ stopy (zvuk nebo video) s minimálním posledním časovým razítkem. |
| `maxLastTimestamp` | řetězec | Maximum všech časových razítek mezi všemi skladbami (zvuk nebo video). |
| `typeOfTrackWithMaxLastTimestamp` | řetězec | Typ stopy (zvuk nebo video) s maximálním posledním časovým razítkem |
| `timescaleOfMinLastTimestamp`| řetězec | Získá časovou osu, ve které je reprezentace "MinLastTimestamp".|
| `timescaleOfMaxLastTimestamp`| řetězec | Získá časovou osu, ve které je reprezentace "MaxLastTimestamp".|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingVideoStreamsOutOfSync** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventIncomingVideoStreamsOutOfSync** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `firstTimestamp` | řetězec | Bylo přijato časové razítko pro jednu z úrovní sledování a kvality typu video. |
| `firstDuration` | řetězec | Doba trvání datového bloku s prvním časovým razítkem. |
| `secondTimestamp` | řetězec  | Bylo přijato časové razítko pro určitou další úroveň sledování a kvality typu video. |
| `secondDuration` | řetězec | Doba trvání datového bloku s druhým časovým razítkem. |
| `timescale` | řetězec | Časová osa časových razítek a trvání|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventIngestHeartbeat** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)


Následující příklad ukazuje schéma události **LiveEventIngestHeartbeat** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIngestHeartbeat",
    "time": "2018-08-07T23:17:57.4610506",
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
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `trackType` | řetězec | Typ stopy (zvuk/video) |
| `trackName` | řetězec | Název stopy (poskytnutý kodérem nebo v případě RTMP vygeneruje Server ve formátu *TrackType_Bitrate* ). |
| `bitrate` | integer | Přenosová rychlost stopy |
| `incomingBitrate` | integer | Vypočtená přenosová rychlost založená na datových blocích přicházejících z kodéru. |
| `lastTimestamp` | řetězec | Poslední přijaté časové razítko pro stopu za posledních 20 sekund. |
| `timescale` | řetězec | Časové měřítko, ve kterém jsou vyjádřena časová razítka. |
| `overlapCount` | integer | Počet bloků dat má překrývající se časová razítka za posledních 20 sekund. |
| `discontinuityCount` | integer | Počet nekontinuity zjištěných za posledních 20 sekund. |
| `nonIncreasingCount` | integer | Počet datových bloků s časovými razítky v minulosti byl přijat za posledních 20 sekund. |
| `unexpectedBitrate` | bool | Pokud se očekává a Skutečná přenosová rychlost se v posledních 20 sekundách liší od více než povoleného limitu. Je true pouze v případě, že incomingBitrate >= 2 * přenosová rychlost nebo incomingBitrate <= přenosová rychlost/2 nebo IncomingBitrate = 0. |
| `state` | řetězec | Stav živé události. |
| `healthy` | bool | Uvádí, zda je příjem dat v pořádku v závislosti na počtu a příznacích. V pořádku má hodnotu true, pokud overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Následující příklad ukazuje schéma události **LiveEventTrackDiscontinuityDetected** : 

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události **LiveEventTrackDiscontinuityDetected** : 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "time": "2018-08-07T23:18:06.1270405Z",
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
    "specversion": "1.0"
  }
]
```

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `trackType` | řetězec | Typ stopy (zvuk/video) |
| `trackName` | řetězec | Název stopy (poskytnutý kodérem nebo v případě RTMP vygeneruje Server ve formátu *TrackType_Bitrate* ). |
| `bitrate` | integer | Přenosová rychlost stopy |
| `previousTimestamp` | řetězec | Časové razítko předchozího fragmentu |
| `newTimestamp` | řetězec | Časové razítko aktuálního fragmentu |
| `discontinuityGap` | řetězec | Mezera mezi více než dvěma časovými razítky. |
| `timescale` | řetězec | Časová osa, v níž jsou reprezentovány mezery mezi časovou osou a diskontinuita |

### <a name="common-event-properties"></a>Vlastnosti běžných událostí

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | řetězec | Téma Event gridu. Tato vlastnost má ID prostředku pro účet Media Services. |
| `subject` | řetězec | Cesta prostředku pro kanál Media Services pod účtem Media Services Zřetězením tématu a předmětu získáte ID prostředku pro úlohu. |
| `eventType` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. Například "Microsoft. Media. JobStateChange". |
| `eventTime` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Media Services data události. |
| `dataVersion` | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| `metadataVersion` | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `source` | řetězec | Téma Event gridu. Tato vlastnost má ID prostředku pro účet Media Services. |
| `subject` | řetězec | Cesta prostředku pro kanál Media Services pod účtem Media Services Zřetězením tématu a předmětu získáte ID prostředku pro úlohu. |
| `type` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. Například "Microsoft. Media. JobStateChange". |
| `time` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Media Services data události. |
| `specversion` | řetězec | Verze specifikace schématu CloudEvents |


---

## <a name="next-steps"></a>Další kroky

[Zaregistrujte se na události změny stavu úlohy.](../media-services/latest/monitoring/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Viz také

- [EventGrid .NET SDK zahrnující události Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definice událostí Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Živé kódy chyb událostí](../media-services/latest/live-event-error-codes-reference.md)
