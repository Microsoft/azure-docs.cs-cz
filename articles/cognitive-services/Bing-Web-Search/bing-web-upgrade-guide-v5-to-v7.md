---
title: Upgrade ze služby Bing webové v5 vyhledávání rozhraní API pro v7 | Microsoft Docs
description: Identifikuje části aplikace, které je potřeba aktualizovat na použití verze 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 155297f230c0ee02d6fa49d6d35eb24d9941f29b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343414"
---
# <a name="web-search-api-upgrade-guide"></a>Průvodce upgradem webové rozhraní API pro hledání

Tento průvodce upgradem identifikuje změny mezi 5 a verze 7 rozhraní API pro vyhledávání webové služby Bing. Pomocí Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze pro koncový bod změněn z v5 na v7. Například https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /vyhledávání.

### <a name="error-response-objects-and-error-codes"></a>Chyba odpovědi objekty a kódy chyb

- Teď by měla obsahovat všechny neúspěšné žádosti `ErrorResponse` objektu v textu odpovědi.

- Přidat následující pole do `Error` objektu.  
  - `subCode`&mdash;Oddíly. kód chyby do diskrétní sad, pokud je to možné
  - `moreDetails`&mdash;Další informace o této chybě podrobněji popsaná `message` pole
   

- Kódy chyb v5 nahradí následující možné `code` a `subCode` hodnoty.

|Kód|Dílčí|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Bing vrátí ServerError vždy, když nastane některá z podmínek dílčí kódu. Odpověď bude obsahovat tyto chyby, pokud je stavový kód protokolu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest vždy, když libovolná součást žádosti není platný. Například chybí povinný parametr nebo hodnota parametru není platný.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód protokolu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročí dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud se překročí QPS a 403 překračování QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, pokud Bing nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` chybí hlavička nebo klíč předplatného není platný.<br/><br/>Pokud zadáte více než jednu metodu ověřování, dojde k redundance.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud má volající nemá oprávnění pro přístup k prostředku. Této chybě může dojít, pokud klíč předplatného je zakázané nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

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


## <a name="non-breaking-changes"></a>Bez nejnovější změny  

### <a name="headers"></a>Záhlaví

- Přidat volitelné [– direktiva Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) hlavičky žádosti. Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Chcete-li Bing nedošlo k vrácení obsahu v mezipaměti, nastavte na hodnotu no cache hlavičku – direktiva Pragma (například – direktiva Pragma: Ne mezipaměti).

### <a name="query-parameters"></a>Parametry dotazu

- Přidat [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) parametr dotazu. Tento parametr použijte k určení počtu odpovědi, které chcete odpověď na zahrnout. Odpovědi jsou zvolen v závislosti na hodnocení. Například, pokud tento parametr nastavte na tří (3), odpověď obsahuje tři hlavní seřazený odpovědi.  
  
- Přidat [povýšit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) parametr dotazu. Pomocí tohoto parametru spolu s `answerCount` explicitně zahrnout jeden nebo více typů odpovědí, bez ohledu na jejich pořadí. Například pro podporu videa a bitové kopie do odpovědi, by nastavení povýšení na *videa, bitové kopie*. Seznam odpovědí, které chcete zvýšit úroveň nepočítá proti `answerCount` limit. Například pokud `answerCount` 2 a `promote` je nastaven na *videa, bitové kopie*, odpověď může zahrnovat webové stránky, zprávy, videa a obrázků.

### <a name="object-changes"></a>Změny objektu

- Přidat `someResultsRemoved` do [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) objektu. Pole obsahuje logickou hodnotu, která určuje, zda odpovědi z webové odpověď vyloučit některé výsledky.  

