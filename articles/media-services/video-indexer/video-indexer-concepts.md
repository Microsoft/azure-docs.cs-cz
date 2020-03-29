---
title: Koncepty videoindexeru
titleSuffix: Azure Media Services
description: Tento článek popisuje některé koncepty služby Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900676"
---
# <a name="video-indexer-concepts"></a>Koncepty videoindexeru
 
Tento článek popisuje některé koncepty služby Video Indexer.
    
## <a name="summarized-insights"></a>Souhrnné přehledy

Souhrnné přehledy obsahují agregované zobrazení dat: tváře, témata, emoce. Například namísto procházet každý z tisíců časových rozsahů a kontrolu, které plochy jsou v něm, souhrnné přehledy obsahuje všechny plochy a pro každý z nich, časové rozsahy se zobrazí v a % času je zobrazen.

## <a name="time-range-vs-adjusted-time-range"></a>časový rozsah vs. upravený časový rozsah

TimeRange je časový rozsah v původním videu. AdjustedTimeRange je časový rozsah vzhledem k aktuálníplaylist. Vzhledem k tomu, že můžete vytvořit seznam videí z různých řádků různých videí, můžete si vzít 1hodinové video a použít z něj pouze 1 řádek, například 10:00-10:15. V takovém případě budete mít seznam stop s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
 
## <a name="blocks"></a>Bloky

Bloky jsou určeny k usnadnění procházení dat. Blok může být rozdělený třeba podle toho, jak se mění přednášející, nebo když nastane dlouhá pauza.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v tématu [Jak se zaregistrovat a nahrát první video](video-indexer-get-started.md).

## <a name="see-also"></a>Viz také

[Přehled Video Indexeru](video-indexer-overview.md)
