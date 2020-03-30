---
title: Nastavení nízké latence LiveEvent ve službě Azure Media Services | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled nastavení nízké latence LiveEvent a ukazuje, jak nastavit nízkou latenci.
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
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199644"
---
# <a name="live-event-low-latency-settings"></a>Nastavení nízké latence živé události

Tento článek ukazuje, jak nastavit nízkou latenci na [živé události](https://docs.microsoft.com/rest/api/media/liveevents). Popisuje také typické výsledky, které se zobrazí při použití nastavení s nízkou latencí v různých přehrávačích. Výsledky se liší v závislosti na CDN a latenci sítě.

Chcete-li použít novou funkci **LowLatency,** nastavte **StreamOptionsFlag** na **LowLatency** na **LiveEvent**. Při vytváření [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) pro přehrávání HLS nastavte [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) na 1. Jakmile je datový proud v provozu, můžete použít [Azure Media Player](https://ampdemo.azureedge.net/) (ukázková stránka AMP) a nastavit možnosti přehrávání pro použití "Profil heuristiky s nízkou latencí".

> [!NOTE]
> V současné době LowLatency HeuristicProfile v Programu Azure Media Player je určen pro přehrávání datových proudů v `format=mdp-time-csf` protokolu `format=mdp-time-cmaf`MPEG-DASH, s formátem CSF nebo CMAF (například nebo ). 

Následující příklad rozhraní .NET ukazuje, jak nastavit **LowLatency** na **LiveEvent**:

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

Podívejte se na celý příklad: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latence živých událostí

Následující tabulky zobrazují typické výsledky latence (pokud je povolen příznak LowLatency) ve službě Media Services, měřeno od okamžiku, kdy kanál příspěvku dosáhne služby, až do okamžiku, kdy divák uvidí přehrávání v přehrávači. Chcete-li optimálně používat nízkou latenci, měli byste nastavení kodéru naladit na 1 sekundu délky "Skupina obrázků" (GOP). Při použití vyšší délky přídě minimalizujete spotřebu šířky pásma a snižujete datový tok při stejném kmitočetu snímků. To je výhodné zejména ve videích s menším pohybem.

### <a name="pass-through"></a>Průchod 

||2s GOP s nízkou latencí povoleno|1s GOP s nízkou latencí povoleno|
|---|---|---|
|DASH v AMP|10.|8s|
|HLS na nativním přehrávači iOS|14s|10.|

### <a name="live-encoding"></a>Kódování v reálném čase

||2s GOP s nízkou latencí povoleno|1s GOP s nízkou latencí povoleno|
|---|---|---|
|DASH v AMP|14s|10.|
|HLS na nativním přehrávači iOS|18s|13s|

> [!NOTE]
> Latence od konce se může lišit v závislosti na podmínkách místní sítě nebo zavedením vrstvy mezipaměti CDN. Měli byste otestovat přesné konfigurace.

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování tutorial](stream-live-tutorial-with-api.md)

