---
title: Koncové body pro rozhraní API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro vyhledávání obrázků obsahuje tři koncové body. Koncový bod 1 vrací obrázky z webu. Koncový bod 2 vrátí ImageInsights. Koncový bod 3 vrátí trendy obrázky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072628"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Koncové body pro rozhraní API pro vyhledávání obrázků Bingu

Rozhraní **API pro vyhledávání obrázků** obsahuje tři koncové body.  Koncový bod 1 vrátí obrázky z webu na základě dotazu. Koncový bod 2 vrátí [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Koncový bod 3 vrátí trendy obrázky.

## <a name="endpoints"></a>Koncové body

Chcete-li získat výsledky obrazu pomocí rozhraní API Bingu, odešlete požadavek na jeden z následujících koncových bodů. Pomocí záhlaví a parametrů adresy URL definujte další specifikace.

**Koncový bod 1:** Vrátí obrázky, které jsou relevantní pro `?q=""`vyhledávací dotaz uživatele definované .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Koncový bod 2:** Vrátí přehledy o obrázku pomocí jednoho `GET` nebo . `POST`
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Požadavek GET vrací přehledy o obrázku, jako jsou webové stránky, které obsahují obrázek. Zahrňte parametr `GET` [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) s požadavkem.

Nebo můžete zahrnout binární obrázek v `POST` těle požadavku a nastavit `RecognizedEntities`parametr [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na . Tím se vrátí [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) použít jako parametr `GET` v následné žádosti, která vrátí informace o lidech v bitové kopii.  Nastavte `modules` `All` pro získání všech `RecognizedEntities` přehledů, s `POST` výjimkou výsledků bez `insightsToken`dalšího volání pomocí .


**Koncový bod 3:** Vrátí obrázky, které jsou trendy na základě požadavků na vyhledávání jiných uživatelů. Obrázky jsou rozděleny do různých kategorií, například na základě pozoruhodných lidí nebo událostí.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Seznam trhů, které podporují obrázky trendů, naleznete [v tématu Trendy obrázky](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Podrobnosti o záhlavích, parametrech, kódech trhu, objektech odpovědí, chybách atd., naleznete v odkazu rozhraní [API pro vyhledávání obrázků Bingu v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Odpověď JSON
Odpověď na požadavek hledání obrázku zahrnuje výsledky jako objekty JSON. Příklady analýzy výsledků naleznete v [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Další kroky
Bing **Bing** API podporují akce vyhledávání, které vracejí výsledky podle jejich typu.Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON. Všechny koncové body podporují dotazy, které vracejí určitý jazyk nebo umístění podle zeměpisné délky, šířky a poloměru hledání.

Úplné informace o parametrech podporovaných jednotlivými koncovými body naleznete v referenčních stránkách pro každý typ.
Příklady základních požadavků pomocí rozhraní API pro vyhledávání obrázků naleznete v [tématu Rychlé spuštění hledání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
