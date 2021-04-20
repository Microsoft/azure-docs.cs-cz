---
title: Přehled záznamu volání služby Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled funkce záznamu volání a rozhraní API.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730564"
---
# <a name="calling-recording-overview"></a>Přehled volání záznamu

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Řada zemí a států má zákony a předpisy, které se vztahují na záznam telefonátů, hlasových a obrazových hovorů, což často vyžaduje, aby uživatelé měli souhlas se záznamem jejich komunikace. Vaše zodpovědnost za použití možností záznamu volání je v souladu se zákonem. Musíte získat souhlas od stran zaznamenané komunikace způsobem, který je v souladu se zákony platnými pro každého účastníka.

> [!NOTE]
> Předpisy týkající se údržby osobních údajů vyžadují možnost exportovat uživatelská data. Aby bylo možné podporovat tyto požadavky, zaznamenání souborů metadat zahrnuje participantId pro každého účastníka volání v `participants` poli. Pro identifikaci účastníků ve volání můžete křížově odkazovat na snímky magnetické rezonance v poli `participants` pomocí identit interního uživatele. Příklad souboru metadat záznamu je uveden níže pro referenci.

Záznam volání poskytuje sadu rozhraní API pro spuštění, zastavení, pozastavení a obnovení záznamu. K těmto rozhraním API je možné přistupovat z obchodní logiky na straně serveru nebo pomocí událostí aktivovaných akcemi uživatele. Zaznamenaný výstup média je ve `MP4 Audio+Video` formátu, který je stejný jako ten, který týmy používají k záznamu médií. Oznámení týkající se médií a metadat se generují prostřednictvím Event Grid. Nahrávky se ukládají po dobu 48 hodin na integrovaném dočasném úložišti pro účely načítání a přesunu do dlouhodobého řešení podle vlastního výběru. 

## <a name="run-time-control-apis"></a>Rozhraní API pro řízení běhu
Rozhraní API pro řízení za běhu lze použít ke správě záznamů prostřednictvím aktivačních procedur interní obchodní logiky, jako je například aplikace vytvářející volání skupiny a záznam konverzace, nebo z akce aktivované uživatelem, která říká aplikaci serveru, aby spustila záznam. V obou případech `<conversation-id>` je vyžadován záznam konkrétní schůzky nebo volání. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Získání ID konverzace ze serveru iniciované volání

Objekt `ConversationId` je vrácen prostřednictvím `Microsoft.Communication.CallLegStateChanged` události. Toto oznámení o události se vydává po navázání volání. Dá se najít v `data.ConversationId` poli. Tuto hodnotu lze použít přímo jako `{conversationId}` parametr v rozhraních API pro řízení běhu:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>Získání ID konverzace z události aktivované uživatelem v klientovi

Z knihovny jazyka JavaScript `@azure/communication-calling` po vytvoření volání volání `let result = call.info.getConversationUrl()` pro získání `conversationUrl` a poté **Base64Url kódování `conversationUrl` `{conversationId}` pro získání pro použití v rozhraních API pro řízení v době běhu**. Kódování lze provést buď na straně klienta, před odesláním události na server nebo na straně serveru.

Všimněte si, že `conversationUrl` *musí* být Base64Url zakódovaný a nesmí se zaměňovat s použitím pouze kódování Base64 (tj. BTOA).                                                            

### <a name="start-recording"></a>Spustit záznam

#### <a name="request"></a>Žádost

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Odpověď

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Získat stav záznamu volání

#### <a name="request"></a>Žádost

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Odpověď

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Zastavit záznam
#### <a name="request"></a>Žádost
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Odpověď
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Pozastavit záznam
Pozastavení a obnovení záznamu volání umožňuje přeskočit nahrávání části volání nebo schůzky a pokračovat v záznamu do jednoho souboru. 
#### <a name="request"></a>Žádost
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Odpověď
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Pokračovat v záznamu
#### <a name="request"></a>Žádost
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Odpověď
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Typy výstupů multimédií
Záznam volání aktuálně podporuje smíšený výstupní formát zvuku a videa. Výstupní médium odpovídá záznamům ze schůzky vytvořeným pomocí nahrávání Microsoft Teams.

| Typ kanálu | Formát obsahu | Video | Zvuk |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | MP4 | video 1080 8 FPS všech účastníků ve výchozím uspořádání dlaždic | 16kHz mp4a, smíšený zvuk všech účastníků |

## <a name="event-grid-notifications"></a>Oznámení Event Grid
Oznámení Event Grid `Microsoft.Communication.RecordingFileStatusUpdated` se publikuje, když je nahrávání připravené k načtení, obvykle 1-2 minut po dokončení procesu nahrávání (např. Schůzka skončila, záznam se zastavil). Záznam oznámení událostí zahrnuje ID dokumentu, které se dá použít k načtení nahraných médií a souboru metadat záznamu:

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Vzorový kód pro zpracování oznámení služby Event Grid a stahování záznamů a souborů metadat najdete [tady](../../quickstarts/voice-video-calling/download-recording-file-sample.md). 

### <a name="notification-schema"></a>Schéma oznámení
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Stažení souboru

> Komunikační služby Azure poskytují krátkodobé úložiště médií pro nahrávky. **Exportujte veškerý zaznamenaný obsah, který chcete uchovat během 48 hodin.** Po 48 hodinách již nebudou záznamy k dispozici.

### <a name="download-recording"></a>Stažení záznamu
#### <a name="request"></a>Žádost
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Odpověď
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Stáhnout metadata záznamu
#### <a name="request"></a>Žádost
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Odpověď
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
