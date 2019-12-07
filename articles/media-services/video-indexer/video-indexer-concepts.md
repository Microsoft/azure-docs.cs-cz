---
title: Video Indexer koncepty
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
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900676"
---
# <a name="video-indexer-concepts"></a>Video Indexer koncepty
 
Tento článek popisuje některé koncepty služby Video Indexer.
    
## <a name="summarized-insights"></a>Shrnuté přehledy

Shrnuté přehledy obsahují agregované zobrazení dat: plošky, témata a emoce. Například místo toho, aby se načetly jednotlivé tisíce časových rozsahů a kontrolovaly, které plošky jsou v něm, shrnuté přehledy obsahují všechny plošky a pro každý z nich, časové rozsahy, ve kterých se zobrazuje, a% času, ve kterém se zobrazí.

## <a name="time-range-vs-adjusted-time-range"></a>časový rozsah vs. upravený časový rozsah

TimeRange je časovým rozsahem v původním videu. AdjustedTimeRange je časový rozsah relativní k aktuálnímu seznamu testů. Vzhledem k tomu, že můžete vytvořit seznam stop z různých řádků různých videí, můžete použít 1 hodiny videa a použít na něj pouze 1 čáru, například 10:00-10:15. V takovém případě budete mít seznam stop s 1 řádkem, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
 
## <a name="blocks"></a>Bloky

Jsou určeny k tomu, aby bylo snazší projít data. Blok může být rozdělený třeba podle toho, jak se mění přednášející, nebo když nastane dlouhá pauza.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v tématu [jak se zaregistrovat a nahrát své první video](video-indexer-get-started.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled Video Indexeru](video-indexer-overview.md)
