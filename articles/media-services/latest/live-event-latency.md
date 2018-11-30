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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619808"
---
# <a name="liveevent-latency-in-media-services"></a>Latence Livestream ve službě Media Services

Tento článek popisuje, jak nastavit na nízkou latenci **Livestream**. Také popisuje výsledky, které se zobrazí při použití nastavení s nízkou latencí v různé přehrávače. Výsledky lišit v závislosti na latenci sítě CDN. 

Použití nového **LowLatency** funkci, můžete nastavit **StreamOptionsFlag** k **LowLatency** na **Livestream**. Po vytvoření a spuštění je datový proud, můžete [Azure Media Player](http://ampdemo.azureedge.net/) (AMP) ukázka stránku a nastavit možnosti přehrávání použít "S nízkou latencí heuristiky profil".

Následující příklad .NET ukazuje, jak nastavit **LowLatency** na **Livestream**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Podívejte se na úplný příklad: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Předávací LiveEvents latence

Následující tabulka ukazuje typické výsledky pro latenci (Pokud je zapnutý příznak LowLatency) ve službě Media Services, měřená od okamžiku informačního kanálu příspěvek dosáhne službě a když hráč může vyžádat přehrávání.

||2S GOP nízká latence povoleno|1s GOP nízká latence povoleno|
|---|---|---|
|POMLČKA v knihovně AMP|10s|8S|
|HLS v přehrávači nativní aplikace pro iOS|14s|10s|

> [!NOTE]
> Zpoždění začátku do konce se může lišit v závislosti na podmínkách místní sítě, nebo zavedením vrstvu ukládání do mezipaměti CDN. Měli byste vyzkoušet vaše přesné konfigurace.

## <a name="next-steps"></a>Další postup

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)

