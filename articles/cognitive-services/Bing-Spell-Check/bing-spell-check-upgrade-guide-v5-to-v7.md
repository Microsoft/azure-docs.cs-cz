---
title: Upgrade rozhraní API Bingu pro kontrolu pravopisu V5 na v7
titleSuffix: Azure Cognitive Services
description: Určuje části aplikace, které je třeba aktualizovat, aby používaly verzi 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 545772a28a67310b12eb55cd1fb14e8d12a95a58
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "68500821"
---
# <a name="spell-check-api-upgrade-guide"></a>Průvodce upgradem rozhraní API pro kontrolu pravopisu

Tento průvodce upgradem identifikuje změny mezi verzemi 5 a verze 7 rozhraní API Bingu pro kontrolu pravopisu. Tento průvodce vám pomůže identifikovat části aplikace, které potřebujete aktualizovat, aby používaly verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu bylo změněno z verze 5 na v7. Například, `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Objekty a chybové kódy pro odpověď na chybu

- Všechny neúspěšné žádosti by nyní měly `ErrorResponse` obsahovat objekt v těle odpovědi.

- Do `Error` objektu byla přidána následující pole.  
  - `subCode`&mdash;Rozdělí kód chyby do diskrétních kontejnerů, pokud je to možné.
  - `moreDetails`&mdash;Další informace o chybě popsané v `message` poli
   

- Kódy chyb 5 nahradily následujícími možnými `code` hodnotami a `subCode` .  
  
|kód|Podřízeného kódu|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing vrátí ServerError vždy, když dojde ke kterékoli z podmínek subkódu. Odpověď zahrnuje tyto chyby, pokud je stavový kód HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest, pokud jakákoli část žádosti není platná. Například požadovaný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud je chyba HttpNotAllowed, kód stavu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. `Ocp-Apim-Subscription-Key` Hlavička například chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Požadavky na zobrazení a použití](./UseAndDisplayRequirements.md)
