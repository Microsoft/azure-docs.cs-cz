---
title: Upgrade rozhraní API Bingu pro vyhledávání zpráv V5 na v7
titleSuffix: Azure Cognitive Services
description: Určuje části aplikace Vyhledávání zpráv Bingu, které je třeba aktualizovat, aby používaly verzi 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: a114cb24d79189f9e370fae1962f60ca97241d90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351363"
---
# <a name="news-search-api-upgrade-guide"></a>Průvodce upgradem rozhraní Vyhledávání zpráv API

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Tento průvodce upgradem identifikuje změny mezi verzemi 5 a verze 7 rozhraní API Bingu pro vyhledávání zpráv. Tento průvodce vám pomůže identifikovat části aplikace, které potřebujete aktualizovat, aby používaly verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu bylo změněno z verze 5 na v7. Například, `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

### <a name="error-response-objects-and-error-codes"></a>Objekty a chybové kódy pro odpověď na chybu

- Všechny neúspěšné žádosti by nyní měly obsahovat `ErrorResponse` objekt v těle odpovědi.

- Do objektu byla přidána následující pole `Error` .  
  - `subCode`&mdash;Rozdělí kód chyby do diskrétních kontejnerů, pokud je to možné.
  - `moreDetails`&mdash;Další informace o chybě popsané v `message` poli

- Kódy chyb 5 nahradily následujícími možnými `code` hodnotami a `subCode` .

|Kód|Podřízeného kódu|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing vrátí ServerError vždy, když dojde ke kterékoli z podmínek dílčího kódu. Odpověď zahrnuje tyto chyby, pokud je stavový kód HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest, pokud jakákoli část požadavku není platná. Například povinný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud je chyba HttpNotAllowed, kód stavu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. `Ocp-Apim-Subscription-Key`Hlavička například chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud byl klíč předplatného zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, kód stavu HTTP je 403.

- Následující kód namapuje předchozí chybové kódy na nové kódy. Pokud jste se seznámili s kódy chyb V5, aktualizujte odpovídající kód.

|Kód verze 5|Kód verze 7. Subcode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Zakázáno|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError. UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError. NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokované|InvalidRequest. Block

### <a name="object-changes"></a>Změny objektu

- Pole se přidalo `contractualRules` do objektu [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . `contractualRules`Pole obsahuje seznam pravidel, která je nutné dodržovat (například přidělení článku). Místo použití musíte použít zadané přidělení `contractualRules` `provider` . Článek obsahuje `contractualRules` jenom v případě, že odpověď [vyhledávání na webu API](../bing-web-search/overview.md) obsahuje odpověď na zprávy.

## <a name="non-breaking-changes"></a>Neprůlomové změny

### <a name="query-parameters"></a>Parametry dotazů

- Přidání produktů jako možné hodnoty, pro kterou můžete nastavit parametr dotazu [kategorie](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) . Podívejte se [na kategorie podle trhů](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Byl přidán parametr dotazu [sortby](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) , který vrátí témata pro trend seřazená podle data s nejnovějším prvním.

- Přidal se parametr [od](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) parametru Query, který vrátí témata trendů zjištěná bingem na nebo po zadaném časovém razítku epocha systému UNIX.

### <a name="object-changes"></a>Změny objektu

- Pole se přidalo `mentions` do objektu [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . `mentions`Pole obsahuje seznam entit (osob nebo míst), které byly nalezeny v článku.

- Pole se přidalo `video` do objektu [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . `video`Pole obsahuje video, které se vztahuje k článku příspěvky. Video je buď \<iframe\> , který můžete vložit, nebo miniaturu pohybu.

- Pole bylo přidáno `sort` do objektu [News](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) . V `sort` poli se zobrazí pořadí řazení článků. Například články jsou seřazené podle relevance (výchozí) nebo data.

- Byl přidán objekt [SortValue](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) , který definuje pořadí řazení. `isSelected`Pole označuje, zda odpověď použila pořadí řazení. Je-li **nastavena hodnota true**, odpověď použila pořadí řazení. Pokud `isSelected` je **hodnota false**, můžete použít adresu URL v `url` poli pro vyžádání jiného pořadí řazení.