---
title: Kódy chyb živých událostí Služby Azure Media Services | Dokumenty společnosti Microsoft
description: V tomto článku jsou uvedeny kódy chyb živých událostí.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654575"
---
# <a name="media-services-live-event-error-codes"></a>Kódy chyb živých událostí služby Media Services

V tabulkách v této části jsou uvedeny kódy chyb [živé události.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>Připojení LiveEvent bylo odmítnuto.

Když se přihlásíte k odběru událostí [Event Grid](https://docs.microsoft.com/azure/event-grid/) pro živou událost, může se zobrazit jedna z následujících chyb z události [LiveEventConnectionRejected.](media-services-event-schemas.md#liveeventconnectionrejected)

| Kód výsledku | Popis |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Nesprávná adresa URL ingestování |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kód IP není k dispozici v seznamu povolených adres IP nakonfigurován |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kodér RTMP neodeslal příkaz setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Zadaný kodek není podporován. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Informace o popisu média nebyly přijaty před doručením skutečných mediálních dat.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Počet vlastností pro typ zvuku nebo videa překročil maximální povolený limit.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Celkový příchozí přenosový tok ve službě živé události nebo kanálu překročil maximální povolený limit.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Časové razítko pro video nebo audio FLVTag je neplatné z kodéru RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Příchozí kodér ingestované datové proudy s framerates překročil maximální povolenou 30fps pro kódování živých událostí / kanálů.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Příchozí kodéru pohlcované datové proudy překročilnásledující povolené rozlišení: 1920x1088 pro kódování živých událostí/kanálů a 4096 x 2160 pro průchozí živé události/kanály.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderOdpojen

Může se zobrazit jedna z následujících chyb z události [LiveEventEncoderDisconnected.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Kód výsledku|Popis|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Časový limit relace RTMP byl po nečinnosti pro povolený časový limit.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Časové razítko pro video nebo audio FLVTag je neplatné z kodéru RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Kodér odesílá data příliš rychle.|
|Neznámé kódy chyb|Tyto kódy chyb mohou být v rozsahu od chyby paměti až po duplicitní položky v mapě hash.|


## <a name="see-also"></a>Viz také

[Kódy chyb koncového bodu streamování (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Další kroky

[Kurz: Streamujte živě s mediálními službami](stream-live-tutorial-with-api.md)
