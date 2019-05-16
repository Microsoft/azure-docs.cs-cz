---
title: Koncové body pro vyhledávání obrázků Bingu, rozhraní API
titleSuffix: Azure Cognitive Services
description: Seznam dostupných koncových bodů pro rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 9b3edd10d2928a512b94e9273000439f80cb8f33
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65777087"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Koncové body pro vyhledávání obrázků Bingu, rozhraní API

**API pro vyhledávání obrázků** zahrnuje tři koncových bodů.  Koncový bod 1 vrátí imagí z webu na základě dotazu. Vrátí koncový bod 2 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Koncový bod 3 vrátí populárních obrázků.

## <a name="endpoints"></a>Koncové body

Chcete-li získat výsledky hledání obrázků pomocí rozhraní API Bingu odeslat požadavek na jednu z následujících koncových bodů. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

**Koncový bod 1:** Vrací bitové kopie, které jsou relevantní pro daného uživatele vyhledávací dotaz definovaný sadou `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Koncový bod 2:** Vrátí informace o obrázku, buď pomocí `GET` nebo `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Požadavek GET vrací informace o bitové kopie, jako jsou například webové stránky, které obsahují image. Zahrnout [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr s `GET` požadavku.

Nebo můžete zahrnout binární bitové kopie v těle `POST` žádosti a nastavit [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr `RecognizedEntities`. Vrátí [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) použít jako parametr v následné `GET` požadavek, který vrací informace o lidech na obrázku.  Nastavte `modules` k `All` proniknutí, s výjimkou `RecognizedEntities` ve výsledcích `POST` přitom jiné volání pomocí `insightsToken`.


**Koncový bod 3:** Vrátí obrázky, které jsou trendů založené na ostatních žádostí o hledání. Bitové kopie jsou rozděleny do různých kategorií, například na základě zajímavosti osoby nebo události.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Seznam trhy, které podporují populárních obrázků najdete v tématu [populárních obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [API Bingu pro vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) odkaz.
## <a name="response-json"></a>Odpověď JSON
Odpovědi na požadavek hledání image obsahuje výsledky jako objekty JSON. Viz příklady parsování výsledků [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Další postup
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro vyhledávání obrázků, najdete v článku [rychlé zprovoznění pro vyhledávání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
