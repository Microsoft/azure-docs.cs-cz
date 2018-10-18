---
title: Co je rozhraní API pro rozpoznávání emocí?
titlesuffix: Azure Cognitive Services
description: Využijte cloudové algoritmy pro rozpoznávání emocí k vytváření lépe přizpůsobených aplikací.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: abf94e0ab6ebb3df649b1958503c086feb4fa19e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237122"
---
# <a name="what-is-the-emotion-api"></a>Co je rozhraní API pro rozpoznávání emocí?

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Vítá vás rozhraní API Microsoftu pro rozpoznávání emocí, které vám umožní vytvářet lépe přizpůsobené aplikace s využitím cloudových algoritmů Microsoftu pro rozpoznávání emocí.

### <a name="emotion-recognition"></a>Rozpoznávání emocí

Beta verze rozhraní API pro rozpoznávání emocí přijímá jako vstup obrázek a s využitím rozhraní API pro rozpoznávání tváře vrací pro každý obličej na obrázku míru pravděpodobnosti výskytu celé řady emocí a rámeček ohraničující konkrétní obličej. Rozpoznávanými emocemi jsou štěstí, smutek, překvapení, hněv, strach, opovržení, odpor a neutrální pocity. Tyto emoce jsou univerzální a v různých kulturách se vyznačují stejnými základními výrazy tváře, které rozhraní API pro rozpoznávání emocí identifikuje.

**Interpretace výsledků:**

Při interpretaci výsledků z rozhraní API pro rozpoznávání emocí by se rozpoznaná emoce měla interpretovat jako emoce s nejvyšším skóre, protože všechna skóre se normalizují, aby jejich celkový součet byl 1. Uživatelé můžou podle svých potřeb ve své aplikaci nastavit vyšší prahovou hodnotu spolehlivosti.

Další informace o rozpoznávání emocí najdete v referenčních informacích k rozhraní API:
  * Basic: Pokud uživatel již volal rozhraní API pro rozpoznávání tváře, může jako vstup odeslat obdélník tváře a použít úroveň Basic. [Referenční materiály k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Pokud uživatel neodešle obdélník tváře, měl by použít standardní režim.  [Referenční materiály k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Ukázku interpretace streamovaného videa pomocí rozhraní API pro rozpoznávání emocí najdete v tématu [Analýza videí v reálném čase](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
