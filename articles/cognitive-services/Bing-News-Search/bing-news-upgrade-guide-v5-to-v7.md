---
title: Upgrade vyhledávání zprávy Bing rozhraní API verze 5 k v7 | Microsoft Docs
description: Identifikuje části aplikace, které je potřeba aktualizovat na použití verze 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 5334C475-4841-4736-A66E-DC1E07CBCEC9
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: baed6f0091ddad40b4802c0fb52dc2ca1818cd03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342491"
---
# <a name="news-search-api-upgrade-guide"></a>Průvodce upgradováním rozhraní API pro vyhledávání zprávy

Tento průvodce upgradem identifikuje změny mezi 5 a verze 7 rozhraní API pro vyhledávání zprávy Bing. Pomocí Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze pro koncový bod změněn z v5 na v7. Například https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/news/search.

### <a name="error-response-objects-and-error-codes"></a>Chyba odpovědi objekty a kódy chyb

- Teď by měla obsahovat všechny neúspěšné žádosti `ErrorResponse` objektu v textu odpovědi.

- Přidat následující pole do `Error` objektu.  
  - `subCode`&mdash;Oddíly. kód chyby do diskrétní sad, pokud je to možné
  - `moreDetails`&mdash;Další informace o této chybě podrobněji popsaná `message` pole
   

- Kódy chyb v5 nahradí následující možné `code` a `subCode` hodnoty.

|Kód|Dílčí|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Bing vrátí ServerError vždy, když nastane některá z podmínek dílčí kódu. Odpověď obsahuje tyto chyby, pokud je stavový kód protokolu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest vždy, když libovolná součást žádosti není platný. Například chybí povinný parametr nebo hodnota parametru není platný.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód protokolu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročí dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud se překročí QPS a 403 překračování QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, pokud Bing nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` chybí hlavička nebo klíč předplatného není platný.<br/><br/>Pokud zadáte více než jednu metodu ověřování, dojde k redundance.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud má volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud klíč předplatného je zakázané nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

- Následující mapuje předchozí kódy chyb nové kódy. Pokud jste prováděné závislost na kódy chyb v5, aktualizujte kód odpovídajícím způsobem.

|Verze 5 kódu|Verze 7 code.subCode
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

- Přidat `contractualRules` do [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektu. `contractualRules` Pole obsahuje seznam pravidel, které je třeba provést (například porušení článku). Musíte použít uvedení součástí `contractualRules` místo použití `provider`. Článek obsahuje `contractualRules` pouze tehdy, když [vyhledávání webového rozhraní API](../bing-web-search/search-the-web.md) odpovědi obsahuje odpovědi na zprávy.

## <a name="non-breaking-changes"></a>Pevné změny

### <a name="query-parameters"></a>Parametry dotazu

- Přidat produkty jako možná hodnota, kterou může nastavit [kategorie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) parametr do dotazu. V tématu [kategorie trhy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).  
    
- Přidat [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) parametr dotazu, který vrátí trendů témata řazená podle data poslední první.  
  
- Přidat [od](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) parametr dotazu, který vrátí trendů témata, které byly zjištěny nástrojem Bing na nebo za zadané časové razítko epoch Unix.

### <a name="object-changes"></a>Změny objektu

- Přidat `mentions` do [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektu. `mentions` Pole obsahuje seznam sad entit (osob nebo míst), které nebyly nalezeny v článku.  
  
- Přidat `video` do [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektu. `video` Pole obsahuje video, které souvisí s článku zprávy. Je buď \<iframe\> , vložením nebo miniaturu pohybu.   
  
- Přidat `sort` do [zprávy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) objektu. `sort` Poli se zobrazí pořadí řazení článků. Například v článcích jsou seřazené podle závažnosti (výchozí) nebo datum.  
  
- Přidat [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) objekt, který definuje pořadí řazení. `isSelected` Pole určuje, zda použít odpovědi řazení. Pokud **true**, používá odpovědi řazení. Pokud `isSelected` je **false**, můžete použít adresu URL v `url` pole požádat o jiné pořadí řazení.
