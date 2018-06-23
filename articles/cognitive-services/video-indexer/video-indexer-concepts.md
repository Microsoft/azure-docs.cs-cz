---
title: Koncepty Azure Video Indexer | Microsoft Docs
description: Toto téma popisuje některé pojmy služby Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/17/2017
ms.author: juliako
ms.openlocfilehash: 01d92a6b55d2fb2c09cee333f482d79d2cdf763c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343428"
---
# <a name="video-indexer-concepts"></a>Video Indexer koncepty
 
Toto téma popisuje některé pojmy služby Video Indexer.
    
## <a name="summarized-insights"></a>Souhrnná statistika

Souhrnná statistika obsahovat agregovaná zobrazení pro data: řezy, klíčová slova, chráněny. Například místo nad každý tisíce časových rozsahů a kontroly, které řezy jsou v ní, Souhrnná statistika obsahuje všechny řezy a pro každé z nich, časových rozsahů, které se zobrazí v a % doby je zobrazen.

## <a name="topicskeywords"></a>Témata nebo klíčová slova

Témata nebo klíčová slova nejsou v seznamu klíčů frází, které Video Indexer extrahuje z textu. Scott Guthrie video může například obsahovat následující témata nebo klíčová slova: zabezpečení, Azure, Microsoft Cloud, tržby.

## <a name="sentiments"></a>Chráněny

Když Video Indexer analyzuje přepisy, zjistí také chráněny. Například "Toto je velmi zajímavé událostí" je kladné postojích.

## <a name="time-range-vs-adjusted-time-range"></a>časové rozmezí oproti upravenou časový rozsah

Časové rozmezí v původní video je TimeRange. AdjustedTimeRange je časový rozsah, relativně k aktuální seznam stop. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, můžete proveďte video 1 hodinu a používat pouze 1 řádek z něj, například 10:00 – 10:15. V takovém případě bude mít seznam stop 1 řádek, kde časový rozsah je 10:00 – 10:15, ale adjustedTimeRange je 00:00 – 00:15.
 
## <a name="blocks"></a>Bloky

Bloky mají usnadňují projít data. Například bloku může rozdělit na základě při mluvčí změnit nebo je dlouhá pozastavení.

## <a name="next-steps"></a>Další postup

Informace o tom, jak začít, najdete v článku [postup registrace a nahrajte první videa](video-indexer-get-started.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Video s přehledem indexeru](video-indexer-overview.md)

