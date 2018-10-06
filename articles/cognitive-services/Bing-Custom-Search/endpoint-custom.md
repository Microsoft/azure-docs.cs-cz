---
title: Koncový bod Vlastního vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Přehled rozhraní API pro vyhledávání Bingu vlastní koncový bod.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: da448cdeaf6fcbe10cba8e5e2613214f8e0cee18
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815184"
---
# <a name="custom-search"></a>Vlastní vyhledávání
Vlastní vyhledávání Bingu umožňuje vytvořit míru vyhledávací prostředí pro témata, které vás zajímají. Výsledky hledání přizpůsobená pro obsah, o kterou vám jde namísto toho, aby se uživatelům zobrazí na stránce přes výsledky hledání, které mají irelevantní obsah.

## <a name="custom-search-endpoint"></a>Koncový bod pro vlastní vyhledávání
Chcete-li získat výsledky pomocí rozhraní API pro vlastní vyhledávání Bingu, odeslat `GET` požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

Koncový bod: Vrátí návrhy hledání jako výsledky JSON, které se týkají vstupu uživatele určené `?q=""`.
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
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro vlastní vyhledávání, najdete v článku [rychlé zprovoznění vlastního vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)