---
title: Azure Media Services kódy chyb živé události | Microsoft Docs
description: V tomto článku jsou uvedeny kódy chyb živé události.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 13277c3d46d5b68aa5705699f45a2bf9e97a78d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291530"
---
# <a name="media-services-live-event-error-codes"></a>Media Services kódy chyb živé události

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tabulky v této části uvádějí kódy chyb [živé události](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Když se přihlásíte k odběru události [Event Grid](../../event-grid/index.yml) pro živou událost, může se zobrazit jedna z následujících chyb z události [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected)  .

| Kód výsledku | Description |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Nesprávná adresa URL pro příjem |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | V nakonfigurovaném seznamu povolených IP adres není kodér IP. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kodér RTMP neodeslal příkaz setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Zadaný kodek není podporován. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Předtím, než se doručí skutečná data médií, se informace o popisu média nepřijaly.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Počet kvality pro typ zvuku nebo videa překročil maximální povolený limit.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Celkový počet příchozích přenosů v živé události nebo službě kanálu překročil maximální povolený limit.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Časové razítko pro video nebo audio FLVTag je od kodéru RTMP neplatné. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Příchozí kodér ingestující proudy s framerates překročil maximální povolený 30fps pro kódování živých událostí nebo kanálů.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Příchozí kanály ingestující kodér překročily následující povolená řešení: 1920x1088 pro kódování živých událostí a kanálů a 4096 x 2160 pro předávací živé události a kanály.|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | Živá událost dostala velké množství zvukových dat najednou nebo velké množství dat videa bez jakýchkoli klíčových snímků. Kodér jsme odpojili, abychom mu pomohli opakovat se se správnými daty. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

V události [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) se může zobrazit jedna z následujících chyb.

|Kód výsledku|Description|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Platnost relace RTMP vypršela po nečinnosti pro povolený časový limit.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Časové razítko pro video nebo audio FLVTag je od kodéru RTMP neplatné.|
|MPE_CAPACITY_LIMIT_REACHED|Kodér odesílá data příliš rychle.|
|Neznámé kódy chyb|Tyto kódy chyb můžou být v rozsahu od chyby paměti až po duplicitní položky v mapě algoritmu hash.|


## <a name="see-also"></a>Viz také

[Kódy chyb koncového bodu streamování (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Další kroky

[Kurz: živé streamování pomocí Media Services](stream-live-tutorial-with-api.md)
