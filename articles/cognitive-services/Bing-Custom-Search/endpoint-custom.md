---
title: Vlastní koncový bod hledání | Microsoft Docs
description: Souhrn rozhraní API pro vyhledávání vlastní koncového bodu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 8d9851f3687a783f52a80a8dffcf2580d4710551
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342862"
---
# <a name="custom-search"></a>Vlastní vyhledávání
Bing vlastní vyhledávání můžete vytvořit prostředí šité na míru vyhledávání pro témata, která se zajímáte o. Výsledky hledání podle obsahu zajímají místo nutnosti se uživatelům zobrazí stránku prostřednictvím výsledky hledání, které mají důležité obsah.

## <a name="custom-search-endpoint"></a>Koncový bod vlastní vyhledávání
Chcete-li získat výsledky pomocí rozhraní API služby Bing vlastní Search, odešlete `GET` požadavek na následující koncový bod. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

Koncový bod: Vrátí vyhledávání návrhy jako výsledky JSON, které jsou relevantní pro vstup uživatele, které jsou definované `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Příklady, které popisují, jak nastavit vlastní vyhledávání zdroje najdete v tématu [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Podrobnosti o hlavičky, parametry, trhu kódy, objekty odpovědi, chyb atd., najdete [rozhraní API služby Bing vlastní Search v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) odkaz.

## <a name="custom-search-response-json"></a>Vlastní hledání odpovědi JSON
Žádost o vlastní vyhledávání vrátí výsledky jako objekty JSON najdete v tématu [odpovědi objekty](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Vlastní pro automatické návrhy v
Rozhraní API pro automatické návrhy vlastní umožňuje odesílat do služby Bing termín dotazu částečné vyhledávání a vrátit seznam navrhované dotazy, které můžete nakonfigurovat. S vlastní pro automatické návrhy přidejte návrhy vrácený rozhraní API a volitelně určete, jestli obsahují návrhy generované Bing.

## <a name="custom-autosuggest-endpoint"></a>Vlastní pro automatické návrhy koncový bod
O vlastních dotazů odeslat požadavek GET na:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Informace o definování vlastní návrhy najdete v tématu [definovat vlastní vyhledávání návrhy](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Vlastní Image vyhledávání
Rozhraní API služby vlastní Image Search umožňuje odesílat vyhledávací dotaz do služby Bing a vrátit seznam příslušné bitové kopie z vaší instance vlastní vyhledávání.

## <a name="custom-image-search-endpoint"></a>Koncový bod hledání vlastní Image
Požádat o bitové kopie z vaší instance vlastní vyhledávání, poslat požadavek GET na následující adresu URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Informace o konfiguraci vlastních hledání instance najdete v tématu [konfigurovat možnosti vlastní vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Další postup
**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady základní požadavků pomocí rozhraní API vlastní Search najdete v tématu [vlastní vyhledávání rychlé – spuštění](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)