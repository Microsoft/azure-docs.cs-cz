---
title: Přehled rozhraní API pro rozpoznávání emocí | Dokumentace Microsoftu
description: Použijte algoritmus rozpoznávání Microsoft nejmodernější, založené na cloudu pro rozpoznávání emocí vytvářet víc personalizované aplikace s rozhraním API pro rozpoznávání Emocí ve službě Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 210990b0f436fd75cb36e71ea28928c457a5232e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573550"
---
# <a name="what-is-emotion-api"></a>Co je rozhraní API pro rozpoznávání emocí?

> [!IMPORTANT]
> 30. října 2017 se přestala nabízet rozhraní API pro rozpoznávání emocí. Funkce je teď součástí [API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/).

Vítá vás rozhraní API pro rozpoznávání Emocí Microsoft, které umožňuje vytvářet víc personalizované aplikace s algoritmem rozpoznávání emocí založené na cloudu od Microsoftu.

### <a name="emotion-recognition"></a>Rozpoznávání emocí

Beta verze rozhraní API pro rozpoznávání Emocí přijímá jako vstup image a vrací sadu pro každou tvář na obrázku, jakož i rámeček ohraničující konkrétní obličej z rozhraní API pro rozpoznávání tváře. Rozpoznávanými emocemi jsou štěstí, smutek, překvapení, hněv, strach, opovržení, znechucení nebo neutrální. Tyto pocity se předávají napříč kulturami a univerzálně prostřednictvím stejné základní výrazů tváře, kde se identifikují pomocí rozhraní API pro rozpoznávání Emocí. 

**Interpretace výsledků:** 

Při interpretaci výsledky z rozhraní API pro rozpoznávání Emocí, pro rozpoznávání emocí zjistil by měl být interpretován jako pro rozpoznávání emocí s nejvyšším skóre, jako jsou normalizovány skóre, které se mají sečíst do jednoho. Uživatelé se můžou rozhodnout nastavit prahovou hodnotu vyšší spolehlivosti do své aplikace podle jejich potřeb. 

Další informace o rozpoznávání emocí z výrazu najdete v referenci rozhraní API: 
  * Základní: Pokud uživatel již volal rozhraní API pro rozpoznávání tváře, mohou jako vstup obličejový obdélník odeslat a použít na úrovni basic. [Referenční materiály k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Pokud uživatel není odeslat obdélníku pro rozpoznávání tváře, musí používat standardní režim.  [Referenční materiály k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Příklad o tom, jak interpretovat streamování videa s rozhraním API pro rozpoznávání Emocí, naleznete v tématu [jak analyzovat videa v reálném čase](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
