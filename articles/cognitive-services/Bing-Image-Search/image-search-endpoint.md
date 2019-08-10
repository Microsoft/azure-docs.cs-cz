---
title: Koncové body pro rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Seznam dostupných koncových bodů pro rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: fc1a0670767b134ad6e330fb41fc5564c754d91c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883405"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Koncové body pro rozhraní API Bingu pro vyhledávání obrázků

**Rozhraní API pro vyhledávání obrázků** obsahuje tři koncové body.  Koncový bod 1 vrátí obrázky z webu na základě dotazu. Koncový bod 2 vrátí [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Koncový bod 3 vrátí obrázky pro vývoj.

## <a name="endpoints"></a>Koncové body

Pokud chcete získat výsledky obrázků pomocí rozhraní API Bingu, odešlete žádost jednomu z následujících koncových bodů. K definování dalších specifikací použijte záhlaví a parametry URL.

**Koncový bod 1:** Vrátí obrázky, které jsou relevantní pro vyhledávací dotaz uživatele definovaný `?q=""`v.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Koncový bod 2:** Vrátí přehledy o obrázku pomocí `GET` nebo. `POST`
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Požadavek GET vrátí přehled o obrázku, jako jsou například webové stránky, které obsahují obrázek. Zahrňte parametr [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) s `GET` požadavkem.

Nebo můžete do těla `POST` žádosti zahrnout binární obrázek a nastavit parametr [moduly](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na. `RecognizedEntities` Tím se vrátí [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) , který se použije jako parametr v následné `GET` žádosti, který vrátí informace o lidech v imagi.  Nastavte `modules` `RecognizedEntities` na `All` pro získání všech přehledů s výjimkou výsledků `POST` bez dalšího volání pomocí. `insightsToken`


**Koncový bod 3:** Vrátí obrázky, které jsou v trendech, na základě požadavků na hledání provedených ostatními. Obrázky jsou rozdělené do různých kategorií, například podle zajímavosti lidí nebo událostí.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Seznam trhů, které podporují obrázky v trendech, najdete v tématu věnovaném [trendům obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. naleznete v tématu [rozhraní API Bingu pro vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) reference.
## <a name="response-json"></a>Odpověď JSON
Odpověď na požadavek hledání obrázku obsahuje výsledky jako objekty JSON. Příklady analýzy výsledků najdete v [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) a ve zdrojovém [kódu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Další kroky
Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON.  Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro hledání obrázků najdete v tématu [vyhledávání obrázků rychlé zahájení](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)práce.
