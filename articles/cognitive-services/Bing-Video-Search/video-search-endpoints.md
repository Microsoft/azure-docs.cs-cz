---
title: Koncové body video vyhledávání | Microsoft Docs
description: Shrnutí koncový bod rozhraní API vyhledávání Video.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342403"
---
# <a name="video-search-endpoints"></a>Koncové body video vyhledávání
**Rozhraní API pro vyhledávání Video** zahrnuje tři koncové body.  Koncový bod 1 vrátí videa z webu na základě dotazu. Koncový bod 2 vrátí přehledy o video na základě `modules` parametr URL.  Koncový bod 3 vrátí trendů bitové kopie.

## <a name="endpoints"></a>Koncové body
K získání video výsledků pomocí rozhraní API služby Bing, odeslání `GET` požadavek na jednu z následujících koncových bodů. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

**Koncovém bodě 1:** vrátí videa, která jsou relevantní pro uživatele vyhledávací dotaz definované `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Koncový bod 2:** vrátí přehledy o video, například související videa. Zahrnout `modules` [parametr dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), což je čárkami oddělený seznam přehledy o.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Koncový bod 3:** vrátí videa, která jsou trendů podle požadavky search provedené jinými uživateli. Videím jsou rozdělené do různých kategorií, například na základě pozoruhodné osoby nebo události.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Podrobnosti o hlavičky, parametry, trhu kódy, objekty odpovědi, chyb atd., najdete [rozhraní API služby Bing Video Search v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) odkaz.
## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání videa obsahuje výsledky jako objekty JSON. Příklady Analýza výsledků najdete v tématu [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Další postup
**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady základní požadavků pomocí vyhledávání Video rozhraní API najdete v tématu [Video vyhledávání rychlé spustí](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).