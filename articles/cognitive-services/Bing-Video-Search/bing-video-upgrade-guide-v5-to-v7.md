---
title: Upgrade Bingu v5 rozhraní Video API k v7 | Dokumentace Microsoftu
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
ms.openlocfilehash: db1bc07c06f3d073b6cc0f206620e50a5f048e2a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "41987613"
---
# <a name="video-search-api-upgrade-guide"></a>Průvodce upgradem video Search API

Tento průvodce upgradem identifikuje změny mezi verzí 5 a API pro vyhledávání videí Bingu verze 7. Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncový bod se změní z v5 na v7. Například, `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

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

### <a name="query-parameters"></a>Parametry dotazu

- Přejmenovat `modulesRequested` parametr do dotazu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Změny objektu

- Přejmenovat `nextOffsetAddCount` pole [videa](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) k `nextOffset`. Způsob, jak použít posun byl také změněn. Dříve, byste měli nastavit [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametr k dotazu `nextOffset` hodnota plus předchozí hodnotu posunu plus počet videí ve výsledku. Nyní, stačí nastavit `offset` parametr k dotazu `nextOffset` hodnotu.  
  
- Změnit datový typ `relatedVideos` pole z `Video[]` k [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (viz [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

