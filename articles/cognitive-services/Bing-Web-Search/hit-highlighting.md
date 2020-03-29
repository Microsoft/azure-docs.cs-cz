---
title: Jak zvýraznit text pomocí značek dekorace – rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Naučte se používat textové dekorace a zvýraznění přístupů ve výsledcích hledání pomocí rozhraní API pro vyhledávání na webu Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854011"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Zvýraznění textu pomocí značek dekorace

Bing podporuje zvýraznění přístupů, které označuje termíny dotazu (nebo jiné termíny, které Bing považuje za relevantní) v řetězce zobrazení některých odpovědí. Například pole výsledků webové `name`stránky `displayUrl`, `snippet` a mohou obsahovat označené termíny dotazu. 

Ve výchozím nastavení bing neobsahuje značky zvýraznění v řetězecch zobrazení. Chcete-li značky povolit, zahrňte do požadavku parametr dotazu `textDecorations` a nastavte jej na `true`.

## <a name="hit-highlighting-example"></a>Příklad zvýraznění přístupů

Následující příklad ukazuje výsledek `Sailing Dinghy`webu pro aplikaci . Bing označil začátek a konec termínu dotazu pomocí znaků Unicode E000 a E001.
  
![Zvýraznění zásahu](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Před zobrazením výsledku v uživatelském rozhraní nahraďte znaky Unicode znaky, které jsou vhodné pro formát zobrazení.

## <a name="marker-formatting"></a>Formátování značek

Bing poskytuje možnost použití znaků Unicode nebo značek HTML jako značek. Chcete-li určit, které značky se mají použít, zahrňte parametr dotazu [textFormat:](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 

| Hodnota             | Značku                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Znaky Unicode (výchozí) |
| `textFormat=HTML` | Znaky HTML              |

## <a name="additional-text-decorations"></a>Další textové dekorace

Bing může vrátit několik různých textových dekorací. Odpověď může například obsahovat značky dolního `log(2)` indexu `expression` pro termín dotazu v poli. `Computation`

![výpočetní značky](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Pokud požadavek nespecifikoval dekorace, `expression` pole `log10(2)`bude obsahovat . 

Pokud `textDecorations` `true`je , Bing může obsahovat následující značky v řetězci zobrazení odpovědí. Pokud neexistuje žádná ekvivalentní značka HTML, je buňka tabulky prázdná.

|Kódování Unicode|HTML|Popis
|-|-|-
|U+E000|\<b>|Označuje začátek termínu dotazu (zvýraznění přístupů)
|U+E001|\</b>|Označuje konec termínu dotazu.
|U+E002|\<i>|Označuje začátek obsahu kurzívou. 
|U+E003|\</i>|Označuje konec obsahu kurzívy.
|U+E004|\<br/>|Označuje zalomení řádku.
|U+E005||Označuje začátek telefonního čísla.
|U+E006||Označuje konec telefonního čísla.
|U+E007||Označuje začátek adresy.
|U+E008||Označuje konec adresy.
|U+E009|\&nbsp;|Označí nerozdělitý prostor.
|U+E00C|\<silná>|Označuje začátek tučného obsahu.
|U+E00D|\</silný>|Označuje konec tučného obsahu.
|U+E00E||Označuje začátek obsahu, jehož pozadí by mělo být světlejší než okolní pozadí.
|U+E00F||Označuje konec obsahu, jehož pozadí by mělo být světlejší než okolní pozadí.
|U+E010||Označuje začátek obsahu, jehož pozadí by mělo být tmavší než okolní pozadí.
|U+E011||Označuje konec obsahu, jehož pozadí by mělo být tmavší než okolní pozadí.
|U+E012|\<del>|Označuje začátek obsahu, který by měl být
|U+E013|\</del>|Označuje konec obsahu, který by měl být
|U+E016|\<dílčí>|Označuje začátek obsahu dolního indexu.
|U+E017|\</dílčí>|Označuje konec obsahu dolního indexu.
|U+E018|\<>|Označuje začátek obsahu horního indexu.
|U+E019|\</sup>|Označuje konec obsahu horního indexu.

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro vyhledávání na webu Bingu?](overview.md) 
* [Změna velikosti a oříznutí miniatur](resize-and-crop-thumbnails.md)