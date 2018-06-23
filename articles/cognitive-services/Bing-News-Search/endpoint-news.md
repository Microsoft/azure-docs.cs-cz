---
title: Koncové body vyhledávání zprávy Bing | Microsoft Docs
description: Shrnutí koncový bod rozhraní API vyhledávání zprávy.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342407"
---
# <a name="news-search-endpoints"></a>Koncové body vyhledávání zprávy
**Rozhraní API pro vyhledávání zprávy** vrátí zprávy články, webové stránky, obrázky, videa, a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahovat souhrnné informace o osoby, místní nebo téma. 
## <a name="endpoints"></a>Koncové body
Chcete-li získat výsledky hledání zprávy pomocí rozhraní API služby Bing, odešlete `GET` požadavek na jednu z následujících koncových bodů. Specifikace definovat další hlaviček a parametrů adresy URL.

**Koncový bod 1:** vrátí položky zpráv podle uživatele vyhledávací dotaz. Pokud vyhledávací dotaz je prázdný, vrátí volání nejvyšší články. Dotaz `?q=""` možnost lze použít také s `/news` adresy URL. Dostupnost, najdete v části [podporovaných zemích a trhů](supported-countries-markets.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**Koncový bod 2:** vrátí nejnovější položky podle kategorie. Můžete konkrétně vyžádat hlavní obchodní, sportu nebo Zábava článků pomocí `category=business`, `category=sports`, nebo `category=entertainment`.  `category` Parametr lze použít pouze s `/news` adresy URL. Existují některé formální požadavky pro zadání kategorií; odkazovat na `category` v [parametr dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentaci. Dostupnost, najdete v části [podporovaných zemích a trhů](supported-countries-markets.md#supported-markets-for-news-endpoint). 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Koncový bod 3:** témata vrátí zprávy, které jsou aktuálně trendů v sociálních sítích. Když `/trendingtopics` je zadán parametr, Bing vyhledávání ignoruje několik dalších parametrů, jako například `freshness` a `?q=""`. Dostupnost, najdete v části [podporovaných zemích a trhů](supported-countries-markets.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

Podrobnosti o hlavičky, parametry, trhu kódy, objekty odpovědi, chyb atd., najdete [vyhledávání zprávy Bing v7 rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) odkaz.
## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání zprávy zahrne výsledky jako objekty JSON. Analýza výsledků vyžaduje postupy, které zpracovávají elementy každého typu. Najdete v článku [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) příklady.

## <a name="next-steps"></a>Další postup
**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady základní požadavků pomocí vyhledávání zprávy rozhraní API najdete v tématu [vyhledávání zprávy Bing rychlé spustí](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).