---
title: Automatická identifikace a přepis vícejazyčné hospo-
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak automaticky identifikovat a přepsat vícejazyčný obsah pomocí video indexeru.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968741"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Automatická identifikace a přepis vícejazyčné položky (náhled)

Video Indexer podporuje automatickou identifikaci jazyka a přepis ve vícejazyčném obsahu. Tento proces zahrnuje automatickou identifikaci mluveného jazyka v různých segmentech od zvuku, odeslání každého segmentu mediálního souboru, který má být přepisován, a zkombinování přepisu zpět do jednoho jednotného přepisu. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Výběr vícejazyčné identifikace při indexování pomocí portálu

Při nahrávání a indexování videa můžete zvolit **detekci více jazyků.** Případně můžete zvolit **detekci více jazyků** při přeindexování videa. Následující kroky popisují, jak přeindexovat:

1. Přejděte na web [Video Indexer](https://vi.microsoft.com/) a přihlaste se.
1. Přejděte na stránku **Knihovna** a najeďte na název videa, které chcete přeindexovat. 
1. V pravém dolním rohu klikněte na tlačítko **Přeindexovat video.** 
1. V dialogovém okně **Přeindexovat video** zvolte **vícejazyčné zjišťování** z rozevíracího pole **Zdrojový jazyk videa.**

    * Pokud je video indexováno jako vícejazyčné, stránka přehledu tuto možnost zahrne a zobrazí se další typ přehledu, který uživateli umožní zobrazit segment, ve kterém je přepsán jazyk "Mluvený jazyk".
    * Překlad do všech jazyků je plně k dispozici z vícejazyčné hospo-
    * Všechny ostatní poznatky se zobrazí v dektovovaném hlavním jazyce – to je jazyk, který se ve zvuku objevil nejvíce.
    * Skryté titulky na přehrávači je k dispozici ve více jazycích stejně.

![Prostředí portálu](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Výběr vícejazyčné identifikace při indexování pomocí rozhraní API

Při indexování nebo [přeindexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) videa pomocí `multi-language detection` rozhraní `sourceLanguage` API zvolte možnost v parametru.

### <a name="model-output"></a>Výstup modelu

Model načte všechny jazyky zjištěné ve videu v jednom seznamu

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Kromě toho každá instance v části přepisu bude obsahovat jazyk, ve kterém byla přepsána

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Pokyny a omezení

* Sada podporovaných jazyků: angličtina, francouzština, němčina, španělština.
* Podpora vícejazyčového obsahu až se třemi podporovanými jazyky.
* Pokud zvuk obsahuje jiné jazyky než výše podporovaný seznam, výsledek je neočekávaný.
* Minimální délka segmentu pro detekci pro každý jazyk – 15 sekund.
* Posun detekce jazyka je v průměru 3 sekundy.
* Očekává se, že řeč bude nepřetržitá. Časté střídání mezi jazyky může ovlivnit výkon modelů.
* Řeč nerodilých mluvčích může ovlivnit výkon modelu (například když mluvčí používají svůj rodný jazyk a přecházejí do jiného jazyka).
* Model je navržen tak, aby rozpoznal spontánní konverzační řeč s rozumnou zvukovou akustikou (ne hlasové příkazy, zpěv atd.).
* Vytváření a úpravy projektu nejsou v současné době k dispozici pro vícejazyčná videa.
* Vlastní jazykové modely nejsou k dispozici při použití detekce více jazyků.
* Přidání klíčových slov není podporováno.
* Při exportu souborů skrytých titulků se nezobrazí jazyková indikace.
* Rozhraní API pro přepis aktualizace nepodporuje soubory více jazyků.

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)
