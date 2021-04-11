---
title: Livestream nastavení nízké latence v Azure Media Services
description: Toto téma obsahuje přehled nastavení Livestream s nízkou latencí a ukazuje, jak nastavit nízkou latenci.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2b612201a5ce1c661c8d6549f2882f86cfaf0e32
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123357"
---
# <a name="live-event-low-latency-settings"></a>Nastavení nízké latence události za provozu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek ukazuje, jak nastavit nízkou latenci pro [živou událost](/rest/api/media/liveevents). Pojednává také o typických výsledcích, které vidíte při použití nastavení nízké latence v různých přehrávačích. Výsledky se liší v závislosti na síti CDN a latenci sítě.

Chcete-li použít novou funkci **LowLatency** , nastavte **StreamOptionsFlag** na **LowLatency** na **Livestream**. Při vytváření [LiveOutput](/rest/api/media/liveoutputs) pro přehrávání HLS nastavte [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) na hodnotu 1. Jakmile je datový proud v provozu, můžete použít [Azure Media Player](https://ampdemo.azureedge.net/) (Ukázková stránka amp) a nastavit možnosti přehrávání tak, aby používaly profil heuristiky s nízkou latencí.

> [!NOTE]
> V současné době je LowLatency HeuristicProfile v Azure Media Player navržena pro přehrávání back-streamů v protokolu MPEG-SPOJOVNÍKu s formátem CSF nebo CMAF (například `format=mdp-time-csf` nebo `format=mdp-time-cmaf` ). 

Následující příklad rozhraní .NET ukazuje, jak nastavit **LowLatency** na **Livestream**:

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#NewLiveEvent)]

Podívejte se na úplný příklad: [živá událost se systémem DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs).

## <a name="live-events-latency"></a>Latence živých událostí

V následujících tabulkách jsou uvedeny typické výsledky pro latenci (když je povolený příznak LowLatency) v Media Services, měřeno od doby, kdy kanál příspěvků dosáhne toho, kdy je v přehrávači vidět přehrávání. Chcete-li použít nízkou latenci, měli byste nastavení kodéru vyladit až na 1 sekundu "délku" skupiny obrázků (skupinu GOP). Při použití větší skupinu GOP délky minimalizujete využití šířky pásma a omezíte přenosovou rychlost při stejné frekvenci snímků. Je to obzvláště užitečné u videí s menším pohybem.

### <a name="pass-through"></a>Průchod 

||2S – skupinu GOP s povolenou nízkou latencí|1 skupinu GOP nízká latence povolena|
|---|---|---|
|**POMLČKa v AMP**|desítkách|8s|
|**HLS v nativním přehrávači pro iOS**|14s|desítkách|

### <a name="live-encoding"></a>Kódování v reálném čase

||2S – skupinu GOP s povolenou nízkou latencí|1 skupinu GOP nízká latence povolena|
|---|---|---|
|**POMLČKa v AMP**|14s|desítkách|
|**HLS v nativním přehrávači pro iOS**|18s|13s|

> [!NOTE]
> Koncová latence se může lišit v závislosti na podmínkách místní sítě nebo při zavedení vrstvy mezipaměti CDN. Měli byste testovat přesnou konfiguraci.

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](stream-live-streaming-concept.md)
- [Kurz živého streamování](stream-live-tutorial-with-api.md)
