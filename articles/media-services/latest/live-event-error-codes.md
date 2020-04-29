---
title: Azure Media Services kódy chyb živé události | Microsoft Docs
description: V tomto článku jsou uvedeny kódy chyb živé události.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654575"
---
# <a name="media-services-live-event-error-codes"></a>Media Services kódy chyb živé události

Tabulky v této části uvádějí kódy chyb [živé události](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Když se přihlásíte k odběru události [Event Grid](https://docs.microsoft.com/azure/event-grid/) pro živou událost, může se zobrazit jedna z následujících chyb z události [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) .

| Kód výsledku | Popis |
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

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

V události [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) se může zobrazit jedna z následujících chyb.

|Kód výsledku|Popis|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Platnost relace RTMP vypršela po nečinnosti pro povolený časový limit.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Časové razítko pro video nebo audio FLVTag je od kodéru RTMP neplatné.|
|MPE_CAPACITY_LIMIT_REACHED|Kodér odesílá data příliš rychle.|
|Neznámé kódy chyb|Tyto kódy chyb můžou být v rozsahu od chyby paměti až po duplicitní položky v mapě algoritmu hash.|


## <a name="see-also"></a>Viz také

[Kódy chyb koncového bodu streamování (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Další kroky

[Kurz: živé streamování pomocí Media Services](stream-live-tutorial-with-api.md)
