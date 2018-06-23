---
title: Upgrade Bing v5 grafické rozhraní API pro v7 | Microsoft Docs
description: Identifikuje části aplikace, které je potřeba aktualizovat na použití verze 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342450"
---
# <a name="video-search-api-upgrade-guide"></a>Průvodce upgradem grafické rozhraní API pro hledání

Tento průvodce upgradem identifikuje změny verze 5 až 7 verzi rozhraní API služby Bing Video Search. Pomocí Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze pro koncový bod změněn z v5 na v7. Například https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/videos/search.

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

### <a name="query-parameters"></a>Parametry dotazu

- Přejmenován `modulesRequested` parametr k dotazu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Změny objektu

- Přejmenován `nextOffsetAddCount` pole z [videa](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) k `nextOffset`. Změnil také způsobu, jakým používáte posun. Dříve, byste měli nastavit [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametr k dotazu `nextOffset` hodnotu a předchozí hodnotu posunutí plus počet videa ve výsledku. Nyní jednoduše nastavte `offset` parametr k dotazu `nextOffset` hodnotu.  
  
- Změnit datový typ `relatedVideos` pole z `Video[]` k [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (viz [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

