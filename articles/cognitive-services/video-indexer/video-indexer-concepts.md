---
title: Koncepty služby video Indexer
titlesuffix: Azure Cognitive Services
description: Toto téma popisuje některé pojmy služby Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 740f13e90397650ed9274937b16254e46c6deced
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984115"
---
# <a name="video-indexer-concepts"></a>Koncepty služby video Indexer
 
Tento článek popisuje některé pojmy služby Video Indexer.
    
## <a name="summarized-insights"></a>Souhrnný přehled

Souhrnný přehled obsahuje souhrnné zobrazení dat: tváří, témata a emocí. Například namísto prostřednictvím všech tisíce časových rozsahů a kontrola tváří, které jsou v ní, obsahuje souhrnný přehled všech ploch a pro každé z nich, časových rozsahů, které se zobrazí v a % doby se zobrazí.

## <a name="time-range-vs-adjusted-time-range"></a>časový rozsah a upravené časové rozmezí

TimeRange je časový rozsah v původního videa. AdjustedTimeRange je časový rozsah vzhledem k aktuálního seznamu testů. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, můžete trvat video 1 hodinu a používat jenom 1 řádek z něj, třeba 10:00-10:15. V takovém případě budete mít seznam testů s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
 
## <a name="blocks"></a>bloky

Bloky jsou určené k usnadnění procházení data. Například blok může rozdělit na základě při přednášející změnit nebo je dlouhý pozastavit.

## <a name="next-steps"></a>Další postup

Informace o tom, jak začít, najdete v části [postup registrace a nahrání prvního videa](video-indexer-get-started.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Video Indexer s přehledem](video-indexer-overview.md)
