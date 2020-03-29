---
title: Koncový bod Vlastního vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Vytvořte přizpůsobené vyhledávací prostředí pro témata, která vás zajímají. Uživatelům se zobrazují výsledky vyhledávání přizpůsobené obsahu, na který jim záleží.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072793"
---
# <a name="custom-search"></a>Vlastní vyhledávání
Vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobená vyhledávací prostředí pro témata, o která máte zájem. Uživatelům se budou zobrazovat výsledky hledání přizpůsobené obsahu, který je zajímá, a nebudou tak muset procházet výsledky hledání s irelevantním obsahem.

## <a name="custom-search-endpoint"></a>Vlastní koncový bod hledání
Chcete-li získat výsledky pomocí rozhraní `GET` API pro vlastní vyhledávání Bingu, odešlete požadavek na následující koncový bod. Pomocí záhlaví a parametrů adresy URL definujte další specifikace.

Koncový bod: Vrátí návrhy hledání jako výsledky JSON, které `?q=""`jsou relevantní pro vstup uživatele definované .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Příklady, které popisují, jak nastavit vlastní zdroje vyhledávání, naleznete v [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Podrobnosti o záhlavích, parametrech, kódech trhu, objektech odpovědí, chybách atd., naleznete v odkazu [rozhraní API pro vlastní vyhledávání Bingu v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)

## <a name="custom-search-response-json"></a>Vlastní odpověď hledání JSON
Vlastní požadavek na hledání vrátí výsledky jako objekty JSON, viz [Response objects](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Vlastní autonávrh
Rozhraní API vlastní autosuggest umožňuje odeslat dotaz na částečný dotaz na Bing a získat zpět seznam navrhovaných dotazů, které můžete nakonfigurovat. Pomocí vlastního automatického návrhu přidáte návrhy vrácené rozhraním API a volitelně určíte, zda mají být zahrnuty návrhy generované službou Bing.

## <a name="custom-autosuggest-endpoint"></a>Vlastní koncový bod automatického návrhu
Chcete-li požádat o vlastní návrhy dotazů, odešlete požadavek GET na:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Informace o definování vlastních návrhů naleznete v [tématu Definování vlastních návrhů hledání](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Vlastní vyhledávání obrázků
Rozhraní API pro vlastní vyhledávání obrázků umožňuje odeslat vyhledávací dotaz do bingu a získat zpět seznam relevantních obrázků z instance vlastního vyhledávání.

## <a name="custom-image-search-endpoint"></a>Koncový bod hledání vlastního obrázku
Chcete-li požádat o obrázky z instance vlastního vyhledávání, odešlete požadavek GET na následující adresu URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Informace o konfiguraci instance vlastního hledání naleznete v [tématu Konfigurace vlastního prostředí pro vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Další kroky
Bing **Bing** API podporují akce vyhledávání, které vracejí výsledky podle jejich typu.Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON. Všechny koncové body podporují dotazy, které vracejí určitý jazyk nebo umístění podle zeměpisné délky, šířky a poloměru hledání.

Úplné informace o parametrech podporovaných jednotlivými koncovými body naleznete v referenčních stránkách pro každý typ.
Příklady základních požadavků pomocí rozhraní API pro vlastní vyhledávání naleznete v [tématu Custom Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
