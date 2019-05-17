---
title: Koncepty služby video Indexer
titlesuffix: Azure Media Services
description: Toto téma popisuje některé pojmy služby Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 156eceba856bf159d4821360639a0641d3ed02be
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799054"
---
# <a name="video-indexer-concepts"></a>Koncepty služby video Indexer
 
Tento článek popisuje některé pojmy služby Video Indexer.
    
## <a name="summarized-insights"></a>Souhrnný přehled

Souhrnný přehled obsahuje souhrnné zobrazení dat: tváří, témata a emocí. Například namísto prostřednictvím všech tisíce časových rozsahů a kontrola tváří, které jsou v ní, obsahuje souhrnný přehled všech ploch a pro každé z nich, časových rozsahů, které se zobrazí v a % doby se zobrazí.

## <a name="time-range-vs-adjusted-time-range"></a>časový rozsah a upravené časové rozmezí

TimeRange je časový rozsah v původního videa. AdjustedTimeRange je časový rozsah vzhledem k aktuálního seznamu testů. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, můžete trvat video 1 hodinu a používat jenom 1 řádek z něj, třeba 10:00-10:15. V takovém případě budete mít seznam testů s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
 
## <a name="blocks"></a>bloky

Bloky jsou určené k usnadnění procházení data. Blok může být rozdělený třeba podle toho, jak se mění přednášející, nebo když nastane dlouhá pauza.

## <a name="next-steps"></a>Další postup

Informace o tom, jak začít, najdete v části [postup registrace a nahrání prvního videa](video-indexer-get-started.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled Video Indexeru](video-indexer-overview.md)
