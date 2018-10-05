---
title: Upgrade rozhraní API pro vyhledávání zpráv Bingu v5 pro v7
titlesuffix: Azure Cognitive Services
description: Identifikuje části aplikace, které je potřeba aktualizovat na použití verze 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: c6ecb7d4c1e5b648373fcaa3f44c6294329d33c2
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801161"
---
# <a name="news-search-api-upgrade-guide"></a>Průvodce upgradem rozhraní API pro vyhledávání zpráv

Tento průvodce upgradem identifikuje změny mezi verzí 5 a rozhraní API pro vyhledávání zpráv Bingu verze 7. Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncový bod se změní z v5 na v7. Například https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/news/search.

### <a name="error-response-objects-and-error-codes"></a>Objekty odpovědi chyby a chybové kódy

- Všechny neúspěšné žádosti by teď měl obsahovat `ErrorResponse` objektu v textu odpovědi.

- Přidat následující pole `Error` objektu.  
  - `subCode`&mdash;Oddíly kód chyby: do samostatných sad, tj. Pokud je to možné
  - `moreDetails`&mdash;Další informace o chybě popsaných v `message` pole
   

- Nahradí chybové kódy v5 následujícího `code` a `subCode` hodnoty.

|Kód|Podřízeného|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Bing vrátí ServerError pokaždé, když dojde k některé z podmínek dílčí kód. Odpověď obsahuje tyto chyby, pokud je stavový kód HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest pokaždé, když libovolnou část žádosti není platný. Například povinný parametr chybí nebo není platná hodnota parametru.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded pokaždé, když překročíte dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud se překročí QPS a 403 překročení QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization při Bingu se nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo není platný klíč předplatného.<br/><br/>Redundance nastane, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization volající nemá oprávnění k přístupu k prostředku. Tato situace může nastat, pokud klíč předplatného se zakázalo, nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

- Následující mapuje předchozích kódů chyb nové kódy. Pokud jste pořídili závislost na kódy chyb v5, aktualizujte svůj kód odpovídajícím způsobem.

|Kód verze 5|Verze 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Zakázáno|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Neimplementováno|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokováno|InvalidRequest.Blocked

### <a name="object-changes"></a>Změny objektu

- Přidá `contractualRules` pole [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektu. `contractualRules` Pole obsahuje seznam pravidel, které je třeba dodržovat (například attribution článku). Přidělení podle musíte použít `contractualRules` namísto použití `provider`. Tento článek obsahuje `contractualRules` pouze tehdy, když [webové rozhraní API pro vyhledávání](../bing-web-search/search-the-web.md) odpověď obsahuje odpovědi na zprávy.

## <a name="non-breaking-changes"></a>Nevýznamných změn

### <a name="query-parameters"></a>Parametry dotazu

- Přidat produkty jako možná hodnota, která můžete nastavit [kategorie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) parametr do dotazu. Zobrazit [kategorie trhy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).  
    
- Přidá [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) parametr dotazu, který vrátí seřazené podle data poslední prvních populárních témat.  
  
- Přidá [od](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) parametr dotazu, které vrací populárních tématech, které byly zjištěny bingem na nebo za zadané časové razítko epocha Unix.

### <a name="object-changes"></a>Změny objektu

- Přidá `mentions` pole [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektu. `mentions` Pole obsahuje seznam entit (osoby nebo místa), které nebyly nalezeny v následujícím článku.  
  
- Přidá `video` pole [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektu. `video` Pole obsahuje video, které souvisí s zpravodajskému článku. Video je buď \<iframe\> , které můžete vložit nebo miniaturu pohybu.   
  
- Přidá `sort` pole [zpráv](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) objektu. `sort` Pole zobrazuje pořadí řazení článků. Například články jsou seřazené podle závažnosti (výchozí) nebo data.  
  
- Přidá [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) objektu, který definuje pořadí řazení. `isSelected` Pole označuje, zda odpovědi používá pořadí řazení. Pokud **true**, odpověď používá pořadí řazení. Pokud `isSelected` je **false**, můžete použít na adresu URL v `url` pole, které chcete požádat o jiné pořadí řazení.
