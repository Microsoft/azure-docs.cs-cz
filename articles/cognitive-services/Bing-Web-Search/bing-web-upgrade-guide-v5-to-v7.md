---
title: Upgrade z rozhraní API V5 na v7-rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Určete, které části aplikace vyžadují aktualizace, aby bylo možné používat rozhraní API Vyhledávání na webu Bingu v7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 7ee8d05a542c6906d4ebe70f7e2a461752c6e3f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85609448"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade z rozhraní API Bingu pro vyhledávání na webu V5 na v7

Tento průvodce upgradem identifikuje změny mezi verzemi 5 a verze 7 rozhraní API Bingu pro vyhledávání na webu. Tento průvodce vám pomůže identifikovat části aplikace, které potřebujete aktualizovat, aby používaly verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu se změnilo z V5 na v7. Například https: \/ \/ API.Cognitive.Microsoft.com/Bing/**v 7.0**/Search.

### <a name="error-response-objects-and-error-codes"></a>Objekty a chybové kódy pro odpověď na chybu

- Všechny neúspěšné žádosti by nyní měly obsahovat `ErrorResponse` objekt v těle odpovědi.

- Do objektu byla přidána následující pole `Error` .  
  - `subCode`&mdash;Rozdělí kód chyby do diskrétních kontejnerů, pokud je to možné.
  - `moreDetails`&mdash;Další informace o chybě popsané v `message` poli


- Kódy chyb 5 nahradily následujícími možnými `code` hodnotami a `subCode` .

|Kód|Podřízeného kódu|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing vrátí ServerError vždy, když dojde ke kterékoli z podmínek subkódu. Odpověď bude obsahovat tyto chyby, pokud je stavový kód HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest, pokud jakákoli část požadavku není platná. Například povinný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud je chyba HttpNotAllowed, kód stavu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. `Ocp-Apim-Subscription-Key`Hlavička například chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění pro přístup k prostředku. K této chybě může dojít, pokud byl klíč předplatného zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, kód stavu HTTP je 403.

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


## <a name="non-breaking-changes"></a>Neprůlomové změny  

### <a name="headers"></a>Hlavičky

- Byla přidána volitelná Hlavička požadavku [pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) . Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste Bingu zabránili ve vrácení obsahu uloženého v mezipaměti, hlavičku Pragma nastavte na hodnotu no-cache (například Pragma: no-cache).

### <a name="query-parameters"></a>Parametry dotazů

- Byl přidán parametr dotazu [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) . Pomocí tohoto parametru můžete zadat počet odpovědí, které má odpověď zahrnovat. Odpovědi se volí na základě hodnocení. Například pokud nastavíte tento parametr na tři (3), odpověď obsahuje horní tři seřazené odpovědi.  

- Byl přidán parametr [povýšení](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) dotazu. Použijte tento parametr společně s `answerCount` pro explicitní zahrnutí jednoho nebo více typů odpovědí bez ohledu na jejich hodnocení. Například pro zvýšení úrovně videa a obrázků do odpovědi byste měli nastavit zvýšení úrovně na *videa, obrázky*. Seznam odpovědí, které chcete zvýšit, se nepočítá s `answerCount` limitem. Například pokud `answerCount` má hodnotu 2 a `promote` je nastavená na *videa, obrázky*, odpověď může obsahovat webové stránky, novinky, videa a obrázky.

### <a name="object-changes"></a>Změny objektu

- Pole bylo přidáno `someResultsRemoved` do objektu [webanswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) . Pole obsahuje logickou hodnotu, která označuje, zda odpověď vyloučila některé výsledky z webové odpovědi.  
