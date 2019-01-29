---
title: Latence Livestream ve službě Azure Media Services | Dokumentace Microsoftu
description: Toto téma s přehledem Livestream latence a ukazuje, jak nastavit s nízkou latencí.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: db6646c2066be940b2c058653fe8f2ceb9bff3a2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169696"
---
# <a name="live-event-latency-in-media-services"></a>Živé události latence ve službě Media Services

Tento článek popisuje, jak nastavit na nízkou latenci [živá událost](https://docs.microsoft.com/rest/api/media/liveevents). Také popisuje výsledky, které se zobrazí při použití nastavení s nízkou latencí v různé přehrávače. Výsledky lišit v závislosti na latenci sítě CDN.

Použití nového **LowLatency** funkci, můžete nastavit **StreamOptionsFlag** k **LowLatency** na **Livestream**. Při vytváření [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) přehrávání HLS, nastavte [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) na hodnotu 1. Po vytvoření a spuštění je datový proud, můžete [Azure Media Player](http://ampdemo.azureedge.net/) (AMP ukázkové stránky) a nastavte možnosti přehrávání použít "S nízkou latencí heuristiky profil".

> [!NOTE]
> V současné době LowLatency HeuristicProfile v Azure Media Player je navržen pro přehrávání datových proudů v protokolu DASH nebo HLS s CMAF. Pokud cílíte na zařízení s MacOS nebo iOS přes HLS pomocí Terminálové služby (například `format=m3u8-aapl` nebo `format=m3u8-aapl-v3`), neměli byste používat toto nastavení, protože AMP přímo používá nativní přehrávač, v tomto případě poskytované operačním systémem.

Následující příklad .NET ukazuje, jak nastavit **LowLatency** na **Livestream**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Podívejte se na úplný příklad: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latence živé události

Následující tabulky popisují typický výsledky pro latenci (Pokud je zapnutý příznak LowLatency) ve službě Media Services, měřená od okamžiku informačního kanálu příspěvek dosáhne službě a když prohlížeč uvidí přehrávání na přehrávači. Použití optimálně s nízkou latencí, by měla optimalizovat nastavení kodéru na 1 sekundu délka "Skupiny z obrázků" (GOP). Při použití s vyšší délkou GOP minimalizaci využití šířky pásma a omezení přenosové rychlosti v rámci stejné Snímková frekvence. Je obzvláště užitečné ve videu s méně pohybu.

### <a name="pass-through"></a>Průchod 

||2S GOP nízká latence povoleno|1s GOP nízká latence povoleno|
|---|---|---|
|POMLČKA v knihovně AMP|10s|8S|
|HLS v přehrávači nativní aplikace pro iOS|14s|10s|

### <a name="live-encoding"></a>Kódování v reálném čase

||2S GOP nízká latence povoleno|1s GOP nízká latence povoleno|
|---|---|---|
|POMLČKA v knihovně AMP|14s|10s|
|HLS v přehrávači nativní aplikace pro iOS|18s|13s|

> [!NOTE]
> Zpoždění začátku do konce se může lišit v závislosti na podmínkách místní sítě, nebo zavedením vrstvu ukládání do mezipaměti CDN. Měli byste vyzkoušet vaše přesné konfigurace.

## <a name="next-steps"></a>Další postup

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)

