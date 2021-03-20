---
title: Koncové body pro rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro Vyhledávání obrázků obsahuje tři koncové body. Koncový bod 1 vrací obrázky z webu. Koncový bod 2 vrátí ImageInsights. Koncový bod 3 vrátí obrázky pro vývoj.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94593514"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Koncové body pro rozhraní API Bingu pro vyhledávání obrázků

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Rozhraní API pro vyhledávání obrázků** obsahuje tři koncové body.  Koncový bod 1 vrátí obrázky z webu na základě dotazu. Koncový bod 2 vrátí [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Koncový bod 3 vrátí obrázky pro vývoj.

## <a name="endpoints"></a>Koncové body

Pokud chcete získat výsledky obrázků pomocí rozhraní API Bingu, odešlete žádost jednomu z následujících koncových bodů. K definování dalších specifikací použijte záhlaví a parametry URL.

**Koncový bod 1:** Vrátí obrázky, které jsou relevantní pro vyhledávací dotaz uživatele definovaný v `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Koncový bod 2:** Vrátí přehledy o obrázku pomocí `GET` nebo `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Požadavek GET vrátí přehled o obrázku, jako jsou například webové stránky, které obsahují obrázek. Zahrňte parametr [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) s `GET` požadavkem.

Nebo můžete do těla žádosti zahrnout binární obrázek `POST` a nastavit parametr [moduly](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na `RecognizedEntities` . Tím se vrátí [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) , který se použije jako parametr v následné `GET` žádosti, který vrátí informace o lidech v imagi.  Nastavte `modules` na `All` pro získání všech přehledů s výjimkou `RecognizedEntities` výsledků `POST` bez dalšího volání pomocí `insightsToken` .


**Koncový bod 3:** Vrátí obrázky, které jsou v trendech, na základě požadavků na hledání provedených ostatními. Obrázky jsou rozdělené do různých kategorií, například podle zajímavosti lidí nebo událostí.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Seznam trhů, které podporují obrázky v trendech, najdete v tématu věnovaném [trendům obrázků](./trending-images.md).

Podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. naleznete v tématu [rozhraní API Bingu pro vyhledávání obrázků v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) reference.
## <a name="response-json"></a>Odpověď JSON
Odpověď na požadavek hledání obrázku obsahuje výsledky jako objekty JSON. Příklady analýzy výsledků najdete v [kurzu](./tutorial-bing-image-search-single-page-app.md) a ve [zdrojovém kódu](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Další kroky
Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON.  Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro hledání obrázků najdete v tématu [vyhledávání obrázků rychlé zahájení](./overview.md)práce.