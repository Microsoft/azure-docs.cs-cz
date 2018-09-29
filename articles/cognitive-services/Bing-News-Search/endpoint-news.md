---
title: Hledat koncové body zprávy Bing | Dokumentace Microsoftu
description: Přehled koncového bodu rozhraní API hledání zpráv.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: ab892e947566adf025499382b213a52ed3e96e35
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433680"
---
# <a name="news-search-endpoints"></a>Koncové body vyhledávání zpráv
**Rozhraní API pro vyhledávání zpráv** vrátí články, webových stránek, obrázků, videa, zprávy a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo téma.
## <a name="endpoints"></a>Koncové body
Chcete-li získat výsledky hledání zpráv pomocí rozhraní API Bingu pro odesílání `GET` požadavek na jednu z následujících koncových bodů. Specifikace definovat další hlaviček a parametrů adresy URL.

**Koncový bod 1:** příspěvků podle uživatele vyhledávací dotaz vrátí. Pokud vyhledávacímu dotazu je prázdný, vrátí volání hlavní vybrané články. Dotaz `?q=""` možnost můžete použít také s `/news` adresy URL. Dostupnost, naleznete v tématu [podporované země a trhy](language-support.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**Koncový bod 2:** vrátí hlavní příspěvky podle kategorie. Konkrétně žádosti špičková obchodní, sportu nebo Zábava článků pomocí `category=business`, `category=sports`, nebo `category=entertainment`.  `category` Parametr jde použít jenom s `/news` adresy URL. Existují některé formální požadavky pro určení kategorií; odkazovat na `category` v [parametr dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentaci. Dostupnost, naleznete v tématu [podporované země a trhy](language-support.md#supported-markets-for-news-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Koncový bod 3:** vrátí témata zpráv, které jsou aktuálně trendů v sociálních sítích. Když `/trendingtopics` je zadán parametr, vyhledávání Bingu ignoruje několik dalších parametrů, jako například `freshness` a `?q=""`. Dostupnost, naleznete v tématu [podporované země a trhy](language-support.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [vyhledávání zpráv Bingu, rozhraní API v7 nabízí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) odkaz.
## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání zpráv obsahuje výsledky jako objekty JSON. Parsování výsledků vyžaduje postupy, které zpracovávají prvky každého typu. Zobrazit [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) příklady.

## <a name="next-steps"></a>Další postup
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro vyhledávání zpráv, najdete v článku [rychlé zprovoznění Bingu pro vyhledávání zpráv](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
