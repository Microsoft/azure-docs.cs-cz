---
title: Použití značek dekorace k zvýraznění textu rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Naučte se používat dekorace textu a zvýrazňování přístupů ve výsledcích hledání pomocí rozhraní API Bingu pro vyhledávání na webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: 5c0bed04fccfba1ac06a54d5ddba9033ef05761b
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380704"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Zvýraznění textu pomocí značek dekorace

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Bing podporuje zvýrazňování přístupů, které označuje výrazy dotazu (nebo jiné výrazy, které Bing najde relevantní) v zobrazovaných řetězcích některých odpovědí. Například výsledek webové stránky `name` , `displayUrl` a `snippet` pole mohou obsahovat označené výrazy dotazu. 

Ve výchozím nastavení Bing neobsahuje značky zvýrazňování v řetězcích zobrazení. Chcete-li povolit značky, zahrňte do `textDecorations` žádosti parametr dotazu a nastavte jej na `true` .

## <a name="hit-highlighting-example"></a>Příklad zvýraznění přístupů

Následující příklad ukazuje webový výsledek pro `Sailing Dinghy` . Bing označil začátek a konec výrazu dotazu pomocí znaků Unicode E000 a E001.
  
![Zvýrazňování přístupů](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Před zobrazením výsledku v uživatelském rozhraní nahraďte znaky Unicode těmi, které jsou vhodné pro formát zobrazení.

## <a name="marker-formatting"></a>Formátování značky

Bing nabízí možnost použití znaků Unicode nebo značek HTML jako značek. Chcete-li určit, které značky se mají použít, zahrňte parametr dotazu [TextFormat](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) : 

| Hodnota             | Značky                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Znaky Unicode (výchozí) |
| `textFormat=HTML` | Znaky HTML              |

## <a name="additional-text-decorations"></a>Další dekorace textu

Bing může vracet několik různých dekorace textu. `Computation`Odpověď může například obsahovat značky dolního indexu pro termín dotazu `log(2)` v `expression` poli.

![značky výpočtů](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Pokud žádost neurčila dekorace, `expression` pole by obsahovalo `log10(2)` . 

Pokud `textDecorations` je `true` , Bing může do zobrazovaných řetězců odpovědí zahrnout následující značky. Pokud neexistuje odpovídající značka HTML, buňka tabulky je prázdná.

|Kódování Unicode|HTML|Popis
|-|-|-
|U + E000|\<b>|Označuje začátek termínu dotazu (zvýrazňování přístupů).
|U + E001|\</b>|Označuje konec termínu dotazu.
|U + E002|\<i>|Označí začátek obsahu s kurzívou. 
|U + E003|\</i>|Označuje konec obsahu v kurzívě.
|U + E004|\<br/>|Označí zalomení řádku.
|U + E005||Označí začátek telefonního čísla.
|U + E006||Označí konec telefonního čísla.
|U + E007||Označuje začátek adresy.
|U + E008||Označuje konec adresy.
|U + E009|\&nbsp;|Označí neukončující prostor.
|U + E00C|\<strong>|Označí začátek tučného obsahu.
|U + E00D|\</strong>|Označuje konec tučného obsahu.
|U + E00E||Označí začátek obsahu, jehož pozadí by mělo být světlejší než jeho okolní pozadí.
|U + E00F||Označí konec obsahu, jehož pozadí by mělo být světlejší než jeho okolní pozadí.
|U + E010||Označí začátek obsahu, jehož pozadí má být tmavší než jeho okolní pozadí.
|U + E011||Označí konec obsahu, jehož pozadí má být tmavší než jeho okolní pozadí.
|U + E012|\<del>|Označuje začátek obsahu, který by se měl prorazit.
|U + E013|\</del>|Označuje konec obsahu, který by se měl prorazit.
|U + E016|\<sub>|Označí začátek obsahu dolního indexu.
|U + E017|\</sub>|Označuje konec obsahu dolního indexu.
|U + E018|\<sup>|Označí začátek horního indexu obsahu.
|U + E019|\</sup>|Označuje konec obsahu horního indexu.

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API Bingu pro vyhledávání na webu?](overview.md) 
* [Změna velikosti a oříznutí miniatur](resize-and-crop-thumbnails.md)