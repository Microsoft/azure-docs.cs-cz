---
title: Koncové body pro vyhledávání videí – Video Bingu pro vyhledávání
titlesuffix: Azure Cognitive Services
description: Přehled koncového bodu API Bingu pro vyhledávání videí.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: db65f0ec857b2d69a2d36a22a1d9e3676af91683
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190620"
---
# <a name="video-search-endpoints"></a>Video hledat koncové body
**API pro vyhledávání videí** zahrnuje tři koncových bodů.  Koncový bod 1 vrátí videa z webu na základě dotazu. Koncový bod 2 vrátí informace o videu na základě `modules` parametr adresy URL.  Koncový bod 3 vrátí populárních obrázků.

## <a name="endpoints"></a>Koncové body
Chcete-li získat výsledky videí pomocí rozhraní API Bingu pro odesílání `GET` požadavek na jednu z následujících koncových bodů. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

**Endpoint1:** Videa, která jsou relevantní pro určené uživatele vyhledávací dotaz vrátí `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Koncový bod 2:** Vrátí informace o videa, například související videa. Zahrnout `modules` [parametr dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), což je čárkami oddělený seznam přehledů o.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Koncový bod 3:** Vrátí videa, která jsou trendů založené na ostatních žádostí o hledání. Videa, která jsou rozděleny do různých kategorií, například na základě zajímavosti osoby nebo události.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [API Bingu pro vyhledávání videí v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) odkaz.
## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání videí obsahuje výsledky jako objekty JSON. Viz příklady parsování výsledků [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Další postup
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro vyhledávání Video najdete v tématu [rychlé zprovoznění hledání videí](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).
