---
title: Použití Video Indexer k automatické identifikaci mluvených jazyků – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak se Video Indexer model identifikace jazyka používá k automatické identifikaci mluveného jazyka ve videu.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600633"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatická identifikace mluveného jazyka s využitím modelu identifikace jazyka

Video Indexer podporuje automatickou identifikaci jazyka (víka), což je proces automatického určení mluveného obsahu z zvuku a odeslání mediálního souboru, který bude přepisu v dominantním jazyce. 

Momentálně podporuje víko: angličtina, španělština, francouzština, němčina, italština, ČLR, japonština, ruština a portugalština (Brazílie). 

Nezapomeňte si přečíst část s [pokyny a omezeními](#guidelines-and-limitations) .

## <a name="choosing-auto-language-identification-on-indexing"></a>Výběr automatické identifikace jazyka při indexování

Při indexování nebo [opětovném indexování](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) videa pomocí rozhraní API vyberte `auto detect` možnost v `sourceLanguage` parametru.

Když používáte portál, přejděte na **video o účtu** na domovské stránce [video indexer](https://www.videoindexer.ai/) a najeďte myší na název videa, které chcete znovu indexovat. V pravém dolním rohu klikněte na tlačítko znovu indexovat. V dialogovém okně **znovu indexovat video** vyberte v rozevíracím seznamu **jazyk zdrojové video** možnost *automaticky rozpoznat* .

![automaticky rozpoznat](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Výstup modelu

Pokud je spolehlivost pro daný jazyk, Video Indexer video transcribes podle nejpravděpodobnějšího jazyka `> 0.6` . Pokud jazyk nelze identifikovat s jistotou, předpokládá se, že mluvený jazyk je angličtina. 

Převládající jazyk modelu je k dispozici ve formátu JSON Insights jako `sourceLanguage` atribut (pod položkou root/video/Insights). Odpovídající hodnocení spolehlivosti je také k dispozici v `sourceLanguageConfidence` atributu.

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

* Automatická identifikace jazyka (víka) podporuje následující jazyky: 

    Angličtina, španělština, francouzština, němčina, italština, Mandariná chines, japonština, ruština a portugalština (Brazílie).
* I když Video Indexer podporuje arabštinu (moderní Standard a Levantine), hindština a korejštinu, tyto jazyky se na víka nepodporují.
* Pokud zvuk obsahuje jiné jazyky než seznam podporované, výsledek je neočekávaný.
* Pokud Video Indexer nemůže identifikovat jazyk s dostatečnou spolehlivostí ( `>0.6` ), je záložní jazyk angličtina.
* Neexistuje žádná aktuální podpora pro soubor se smíšenými jazyky. Pokud zvuk obsahuje smíšené jazyky, výsledek je neočekávaný. 
* Zvuk nízké kvality může mít vliv na výsledky modelu.
* Model vyžaduje ve zvukovém zařízení alespoň jednu minutu řeči.
* Model je navržený pro rozpoznávání spontánních konverzací mluveného slova (ne hlasové příkazy, přihlásí atd.).

## <a name="next-steps"></a>Další kroky

* [Přehled](video-indexer-overview.md)
* [Automatická identifikace a přepis vícejazyčného obsahu](multi-language-identification-transcription.md)
