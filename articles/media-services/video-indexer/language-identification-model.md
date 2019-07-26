---
title: Automatické určení mluveného jazyka pomocí Video Indexer – Azure
titlesuffix: Azure Media Services
description: Tento článek popisuje, jak se Video Indexer model identifikace jazyka používá k automatické identifikaci mluveného jazyka ve videu.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/22/2019
ms.author: ellbe
ms.openlocfilehash: ea7d2c451127283f2af204de2c961747880e1bfe
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494898"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automaticky identifikovat mluvený jazyk pomocí modelu identifikace jazyka

Video Indexer podporuje automatickou identifikaci jazyka (víka), což je proces automatického určení mluveného obsahu z zvuku a odeslání mediálního souboru, který bude přepisu v dominantním jazyce. V současnosti se víka podporuje angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a portugalština (Brazílie). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Výběr automatické identifikace jazyka při indexování

Při indexování nebo [opětovném indexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) videa pomocí rozhraní API vyberte `auto detect` možnost v `sourceLanguage` parametru.

Když používáte portál, přejděte na **video o účtu** na domovské stránce [video indexer](https://www.videoindexer.ai/) a najeďte myší na název videa, které chcete znovu indexovat. V pravém dolním rohu klikněte na tlačítko znovu indexovat. V dialogovém okně **znovu indexovat video** vyberte v rozevíracím seznamu **jazyk zdrojové video** možnost *automaticky rozpoznat* .

![automaticky rozpoznat](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Výstup modelu

Pokud je `> 0.6`spolehlivost pro daný jazyk, video indexer video transcribes podle nejpravděpodobnějšího jazyka. Pokud jazyk nelze identifikovat s jistotou, předpokládá se, že mluvený jazyk je angličtina. 

Převládající jazyk modelu je k dispozici ve formátu JSON Insights `sourceLanguage` jako atribut (pod položkou root/video/Insights). Odpovídající hodnocení spolehlivosti je také k dispozici v `sourceLanguageConfidence` atributu.

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
* Pokud video indexer nemůže identifikovat jazyk s dostatečnou spolehlivostí (`>0.6`), je záložní jazyk angličtina.
* Neexistuje žádná aktuální podpora pro soubor se smíšenými jazyky. Pokud zvuk obsahuje smíšené jazyky, výsledek je neočekávaný. 
* Kvalitní zvukové Caustic může mít vliv na výsledky modelu.
* Model vyžaduje ve zvukovém zařízení alespoň jednu minutu řeči.
* Model je navržený pro rozpoznávání spontánních konverzací mluveného slova (ne hlasové příkazy, přihlásí atd.).

## <a name="next-steps"></a>Další postup

[Přehled](video-indexer-overview.md)
