---
title: Koncepty Azure Video Indexer | Dokumentace Microsoftu
description: Toto téma popisuje některé pojmy služby Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399491"
---
# <a name="video-indexer-concepts"></a>Koncepty služby video Indexer
 
Toto téma popisuje některé pojmy služby Video Indexer.
    
## <a name="summarized-insights"></a>Souhrnný přehled

Souhrnný přehled obsahuje souhrnné zobrazení dat: tváří, zabarvení a klíčová slova. Například namísto prostřednictvím všech tisíce časových rozsahů a kontrola tváří, které jsou v ní, obsahuje souhrnný přehled všech ploch a pro každé z nich, časových rozsahů, které se zobrazí v a % doby se zobrazí.

## <a name="topicskeywords"></a>Témata/klíčová slova

Témata/klíčová slova jsou v seznamu klíčové fráze, které z textu extrahuje Video Indexer. Scott Guthrie videa může obsahovat například následující témata/klíčová slova: zabezpečení, Azure, Microsoft Cloud, výnosy.

## <a name="sentiments"></a>zabarvení

Video Indexer analyzuje záznamy o studiu, zjistí také mínění. Například "to je velice zajímavé události" je pozitivní mínění.

## <a name="time-range-vs-adjusted-time-range"></a>časový rozsah a upravené časové rozmezí

TimeRange je časový rozsah v původního videa. AdjustedTimeRange je časový rozsah vzhledem k aktuálního seznamu testů. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, můžete trvat video 1 hodinu a používat jenom 1 řádek z něj, třeba 10:00-10:15. V takovém případě budete mít seznam testů s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
 
## <a name="blocks"></a>bloky

Bloky jsou určené k usnadnění procházení data. Například blok může rozdělit na základě při přednášející změnit nebo je dlouhý pozastavit.

## <a name="next-steps"></a>Další postup

Informace o tom, jak začít, najdete v části [postup registrace a nahrání prvního videa](video-indexer-get-started.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Video Indexer s přehledem](video-indexer-overview.md)
