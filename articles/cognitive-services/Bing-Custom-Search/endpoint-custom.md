---
title: Koncový bod Vlastního vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Přehled rozhraní API pro vyhledávání Bingu vlastní koncový bod.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: b997db319677f6904db7e2fdee4ef0c6bf76a180
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859364"
---
# <a name="custom-search"></a>Vlastní vyhledávání
Vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobená vyhledávací prostředí pro témata, o která máte zájem. Uživatelům se budou zobrazovat výsledky hledání přizpůsobené obsahu, který je zajímá, a nebudou tak muset procházet výsledky hledání s irelevantním obsahem.

## <a name="custom-search-endpoint"></a>Koncový bod pro vlastní vyhledávání
Chcete-li získat výsledky pomocí rozhraní API pro vlastní vyhledávání Bingu, odeslat `GET` požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

Koncový bod: Návrhy hledání vrátí jako výsledky JSON, které se týkají vstupu uživatele určené `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Příklady, které popisují, jak vytvořit vlastní prohledávání zdrojů, najdete v článku [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [API pro vlastní vyhledávání Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) odkaz.

## <a name="custom-search-response-json"></a>Vlastní vyhledávání odpověď JSON
Žádost o vlastní hledání vrátí výsledky jako objekty JSON, naleznete v tématu [objekty odpovědi](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Vlastních automatických návrhů
Rozhraní API pro automatické návrhy vlastní vám umožní odesílat hledaný termín dotazu do Bingu a seznam navrhovaných dotazů, jejichž konfigurací můžete získat zpět. Pomocí vlastních automatických návrhů přidejte návrhy vrácená rozhraním API a volitelně můžete zadat, jestli se má zahrnout návrhy generovaných Bingu.

## <a name="custom-autosuggest-endpoint"></a>Vlastní koncový bod pro automatické návrhy
Požádat o vlastních dotazů, odeslat požadavek GET na:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Informace o definování vlastního návrhy najdete v tématu [definovat vlastní vyhledávání návrhy](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Vlastní vyhledávání obrázků
Rozhraní API pro vyhledávání obrázků vlastní umožňuje poslat dotaz vyhledávání Bingu a získat seznam určit prioritu relevantních obrázků z vaší instance vlastního vyhledávání.

## <a name="custom-image-search-endpoint"></a>Koncový bod vyhledávací vlastní Image
K vyžádání imagí z vaší instance vlastního vyhledávání, odešlete požadavek GET na následující adresu URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Informace o konfiguraci instanci vlastního vyhledávání najdete v tématu [nakonfigurovat prostředí pro vlastní vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Další postup
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro vlastní vyhledávání, najdete v článku [rychlé zprovoznění vlastního vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
