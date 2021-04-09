---
title: Automatické určení a přepisovat obsahu s více jazyky pomocí Video Indexer
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak automaticky identifikovat a přepisovat vícejazyčného obsahu pomocí Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015551"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Automatická identifikace a přepis vícejazyčného obsahu

Video Indexer podporuje automatickou identifikaci jazyka a přepis v obsahu s více jazyky. Tento proces zahrnuje automatickou identifikaci mluveného jazyka v různých segmentech ze zvukového přenosu, posílání jednotlivých segmentů mediálního souboru, které se přepisu, a jejich kombinování zpátky do jednoho sjednoceného přepisu. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Výběr vícejazyčné identifikace při indexování pomocí portálu

Můžete zvolit **rozpoznávání více jazyků** při nahrávání a indexování videa. Případně můžete zvolit **rozpoznávání více jazyků**  při opakovaném indexování videa. Následující postup popisuje, jak znovu indexovat:

1. Přejděte na web [Video Indexer](https://vi.microsoft.com/) a přihlaste se.
1. Přejděte na stránku **Knihovna** a najeďte myší na název videa, které chcete znovu indexovat. 
1. V pravém dolním rohu klikněte na tlačítko **znovu indexovat video** . 
1. V dialogovém okně **znovu index videa** v rozevíracím seznamu **jazyk zdrojové video** vyberte možnost **detekce více jazyků** .

    * Pokud je video indexováno jako vícejazyčná, bude stránka přehled zahrnovat tuto možnost. zobrazí se další typ Insight, který uživateli umožní zobrazit, který segment je přepisu, ve kterém jazyce "mluvený jazyk".
    * Překlad do všech jazyků je plně dostupný z přepisu pro více jazyků.
    * Všechny ostatní přehledy se zobrazí v zjištěném hlavním jazyce – což je jazyk, který se ve zvukovém zařízení objevil nejčastěji.
    * Skryté titulky jsou v přehrávači dostupné i ve více jazycích.

![Prostředí portálu](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Výběr vícejazyčné identifikace při indexování pomocí rozhraní API

Při indexování nebo [přeindexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) videa pomocí rozhraní API vyberte `multi-language detection` možnost v `sourceLanguage` parametru.

### <a name="model-output"></a>Výstup modelu

Model získá všechny jazyky zjištěné ve videu v jednom seznamu.

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Kromě toho všechny instance v oddílu přepisu budou zahrnovat jazyk, ve kterém byl přepisu.

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
* Podpora vícejazyčného obsahu s až třemi podporovanými jazyky.
* Pokud zvuk obsahuje jiné jazyky než seznam podporované, výsledek je neočekávaný.
* Minimální délka segmentu, která se má detekovat pro každý jazyk – 15 sekund
* Hodnota posunu detekce jazyka je v průměru 3 sekundy.
* Očekává se, že řeč bude průběžné. Časté střídající se mezi jazyky mohou ovlivnit výkon modelů.
* Rozpoznávání řeči nenativních mluvčích může ovlivnit výkon modelu (například když reproduktory používají svůj nativní jazyk a přepínají se na jiný jazyk).
* Model je navržený pro rozpoznávání spontánního mluveného hlasu s přiměřenými akustickými zvukovými nahrávkami (ne hlasové příkazy, přihlásí atd.).
* Vytváření a úpravy projektů nejsou aktuálně k dispozici pro videa s více jazyky.
* Vlastní jazykové modely nejsou k dispozici při použití rozpoznávání více jazyků.
* Přidávání klíčových slov se nepodporuje.
* Při exportování souborů titulků se nezobrazí označení jazyka.
* Rozhraní API pro aktualizaci přepisu nepodporuje soubory s více jazyky.

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)
