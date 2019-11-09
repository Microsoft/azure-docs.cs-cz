---
title: Automatické určení mluveného jazyka pomocí Video Indexer – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak se Video Indexer model identifikace jazyka používá k automatické identifikaci mluveného jazyka ve videu.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: ce3e488a6387f9a823d7c1b514b52af24944776b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838997"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automaticky identifikovat mluvený jazyk pomocí modelu identifikace jazyka

Video Indexer podporuje automatickou identifikaci jazyka (víka), což je proces automatického určení mluveného obsahu z zvuku a odeslání mediálního souboru, který bude přepisu v dominantním jazyce. V současnosti se víka podporuje angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a portugalština (Brazílie). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Výběr automatické identifikace jazyka při indexování

Při indexování nebo [opětovném indexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) videa pomocí rozhraní API vyberte v parametrech `sourceLanguage` možnost `auto detect`.

Když používáte portál, přejděte na **video o účtu** na domovské stránce [video indexer](https://www.videoindexer.ai/) a najeďte myší na název videa, které chcete znovu indexovat. V pravém dolním rohu klikněte na tlačítko znovu indexovat. V dialogovém okně **znovu indexovat video** vyberte v rozevíracím seznamu **jazyk zdrojové video** možnost *automaticky rozpoznat* .

![automaticky rozpoznat](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Výstup modelu

Pokud je spolehlivost tohoto jazyka `> 0.6`, Video Indexer video transcribes podle nejpravděpodobnějšího jazyka. Pokud jazyk nelze identifikovat s jistotou, předpokládá se, že mluvený jazyk je angličtina. 

Jazyk dominantního modelu je k dispozici ve formátu JSON Insights jako atribut `sourceLanguage` (pod položkou root/video/Insights). Odpovídající hodnocení spolehlivosti je také k dispozici v atributu `sourceLanguageConfidence`.

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

* Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a brazilská portugalština.
* Pokud zvuk obsahuje jiné jazyky než seznam podporované, výsledek je neočekávaný.
* Pokud Video Indexer nemůže určit jazyk s dostatečnou spolehlivostí (`>0.6`), je záložní jazyk angličtina.
* Neexistuje žádná aktuální podpora pro soubor se smíšenými jazyky. Pokud zvuk obsahuje smíšené jazyky, výsledek je neočekávaný. 
* Zvuk nízké kvality může mít vliv na výsledky modelu.
* Model vyžaduje ve zvukovém zařízení alespoň jednu minutu řeči.
* Model je navržený pro rozpoznávání spontánních konverzací mluveného slova (ne hlasové příkazy, přihlásí atd.).

## <a name="next-steps"></a>Další kroky

* [Přehled](video-indexer-overview.md)
* [Automatické určení a přepisovat obsahu s více jazyky](multi-language-identification-transcription.md)
