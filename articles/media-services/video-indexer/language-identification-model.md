---
title: Automatické identifikaci mluvených jazyků pomocí videoindexeru – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak se model identifikace jazyka Video Indexer používá k automatické identifikaci mluveného jazyka ve videu.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: ba1521581316d559eb4e67bafba0061c31cc666b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272946"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatická identifikace mluveného jazyka pomocí modelu identifikace jazyka

Video Indexer podporuje automatickou identifikaci jazyka (LID), což je proces automatické identifikace mluveného jazyka ze zvuku a odeslání mediálního souboru, který má být přepsán v dominantním identifikovaném jazyce. 

V současné době LID podporuje: angličtina, španělština, francouzština, němčina, italština, mandarinské chines, japonština, ruština a portugalština (brazilská). 

Přečtěte si níže uvedenou část [Pokyny a omezení.](#guidelines-and-limitations)

## <a name="choosing-auto-language-identification-on-indexing"></a>Volba automatické identifikace jazyka při indexování

Při indexování nebo [přeindexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) videa pomocí `auto detect` rozhraní API `sourceLanguage` zvolte možnost v parametru.

Při používání portálu přejděte na domovskou stránku [videoindexeru](https://www.videoindexer.ai/) na **videa svého účtu** a najeďte na název videa, které chcete přeindexovat. V pravém dolním rohu klikněte na tlačítko re-index. V dialogovém okně **Přeindexovat video** zvolte *Automaticky rozpoznat* z rozevíracího pole Zdrojový **jazyk videa.**

![automatické rozpoznání](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Výstup modelu

Video Indexer přepisuje video podle nejpravděpodobnějšího jazyka, pokud `> 0.6`je důvěra pro tento jazyk . Pokud jazyk nelze identifikovat s důvěrou, předpokládá, že mluvený jazyk je angličtina. 

Model dominantní jazyk je k dispozici v `sourceLanguage` přehledech JSON jako atribut (pod root/videos/insights). Odpovídající skóre spolehlivosti je `sourceLanguageConfidence` také k dispozici pod atributem.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Pokyny a omezení

* Automatická identifikace jazyka (LID) podporuje následující jazyky: 

    Angličtina, španělština, francouzština, němčina, italština, mandarinka, japonština, ruština a portugalština (brazilská).
* I když Video Indexer podporuje arabštinu (Moderní standard a Levantine), Hindština a Korejština, tyto jazyky nejsou podporovány v lid.
* Pokud zvuk obsahuje jiné jazyky než výše podporovaný seznam, výsledek je neočekávaný.
* Pokud Video Indexer nelze identifikovat jazyk s`>0.6`dostatečně vysokou spolehlivostí ( ), záložní jazyk je angličtina.
* Neexistuje žádná aktuální podpora pro soubor se smíšenými jazyky zvuku. Pokud zvuk obsahuje smíšené jazyky, výsledek je neočekávaný. 
* Na výsledky modelu může mít vliv zvuk nízké kvality.
* Model vyžaduje alespoň jednu minutu řeči ve zvuku.
* Model je navržen tak, aby rozpoznal spontánní konverzační řeč (ne hlasové příkazy, zpěv atd.).

## <a name="next-steps"></a>Další kroky

* [Přehled](video-indexer-overview.md)
* [Automatická identifikace a přepis obsahu ve více jazycích](multi-language-identification-transcription.md)
