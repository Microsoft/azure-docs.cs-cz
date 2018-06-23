---
title: Obrázek hledání koncové body | Microsoft Docs
description: Shrnutí koncový bod rozhraní API vyhledávání bitové kopie.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342370"
---
# <a name="image-search-endpoints"></a>Obrázek hledání koncové body
**Rozhraní API pro vyhledávání Image** zahrnuje tři koncové body.  Koncový bod 1 vrátí obrázky z webu na základě dotazu. Vrátí koncový bod 2 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Koncový bod 3 vrátí trendů bitové kopie.
## <a name="endpoints"></a>Koncové body
Chcete-li získat výsledky bitovou kopii pomocí rozhraní API služby Bing, odeslat požadavek na jednu z následujících koncových bodů. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

**Koncový bod 1:** vrátí bitové kopie, které jsou relevantní pro uživatele vyhledávací dotaz definované `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Koncový bod 2:** vrátí přehledy o bitovou kopii, buď pomocí `GET` nebo `POST`.
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Požadavek GET vrátí přehledy o image, jako jsou webové stránky, které zahrnují bitovou kopii. Zahrnout [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr s `GET` požadavku.

Nebo binární image můžete zahrnout do těla `POST` žádosti a nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametru `RecognizedEntities`. Tato možnost vrátí [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) použít jako parametr v následné `GET` požadavek, který vrací informace o osoby v bitové kopii.  Nastavit `modules` k `All` získáte všechny přehledy, s výjimkou `RecognizedEntities` ve výsledcích `POST` bez provedení další volání pomocí `insightsToken`. 


**Koncový bod 3:** vrátí bitové kopie, které jsou trendů podle požadavky search provedené jinými uživateli. Bitové kopie jsou rozdělené do různých kategorií, například na základě pozoruhodné osoby nebo události.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Seznam trhy, které podporují trendů obrázky, naleznete v části [trendů image](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Podrobnosti o hlavičky, parametry, trhu kódy, objekty odpovědi, chyb atd., najdete [rozhraní API Search bitové kopie Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) odkaz.
## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání bitové kopie obsahuje výsledky jako objekty JSON. Příklady Analýza výsledků najdete v tématu [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Další postup
**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady základní požadavků pomocí bitové kopie vyhledávání rozhraní API najdete v tématu [obrázek hledání rychlé spustí](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).