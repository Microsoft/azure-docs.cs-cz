---
title: Upgrade z rozhraní API v5 na v7 – rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Určete, které části aplikace vyžadují aktualizace pro použití souborů API bingového vyhledávání na webu v7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 2133cd59c524112ae8a77c0a20cbce1d1336a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881305"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade z rozhraní API pro vyhledávání na webu Bingu v5 na v7

Tato příručka pro upgrade identifikuje změny mezi verzí 5 a verzí 7 rozhraní API pro vyhledávání na webu Bing. V této příručce můžete identifikovat části aplikace, které je třeba aktualizovat, abyste měli používat verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu se změnilo z verze v5 na v7. Například https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Objekty odpovědí na chybu a kódy chyb

- Všechny neúspěšné požadavky by `ErrorResponse` nyní měly obsahovat objekt v těle odpovědi.

- Do objektu byla `Error` přidána následující pole.  
  - `subCode`&mdash;Rozdělí kód chyby do samostatných bloků, pokud je to možné
  - `moreDetails`&mdash;Další informace o chybě popsané `message` v poli


- Kódy chyb v5 byly nahrazeny následujícími možnými `code` hodnotami a `subCode` hodnotami.

|kód|Dílčí kód|Popis
|-|-|-
|Chyba serveru|Neočekávaná chyba<br/>Chyba zdroje<br/>Není implementováno|Bing vrátí chybu serveru vždy, když dojde k některé z podmínek podkódu. Odpověď bude obsahovat tyto chyby, pokud je stavový kód HTTP 500.
|Neplatný požadavek|ParametrMissing ParametrMissing ParametrMissing ParametrMissing<br/>ParametrInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest vždy, když žádná část požadavku není platná. Například chybí požadovaný parametr nebo hodnota parametru není platná.<br/><br/>Pokud je chyba ParametrMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód HTTP 410.
|RateLimit překročena||Bing vrátí RateLimitExceeded vždy, když překročíte vaše dotazy za sekundu (QPS) nebo dotazy za měsíc (QPM) kvóta.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|Neplatná autorizace|AutorizaceChybí<br/>Redundance autorizace|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo klíč předplatného není platný.<br/><br/>K redundanci dochází, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, stavový kód HTTP je 401.
|Nedostatečná autorizace|Autorizace zakázána.<br/>Platnost oprávnění vypršela.|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění k přístupu k prostředku. K této chybě může dojít, pokud byl klíč odběru zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, stavový kód HTTP je 403.

- Následující mapy předchozí kódy chyb na nové kódy. Pokud jste přijali závislost na kódech chyb v5, aktualizujte kód odpovídajícím způsobem.

|Kód verze 5|Kód 7 verze.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|Neplatný parametr Invalid.Neplatný parametr
ResourceAccessDenied|Nedostatečná autorizace
ExceededVolume|RateLimit překročena
Limit překročil qpsLimit|RateLimit překročena
Zakázáno|InsufficientAuthorization.Authorization.AuthorizationDisabled
Neočekávaná chyba|Chyba_serveru_nechybě
Chyby zdroje dat|Chyba_serveru.ResourceError
AutorizaceChybí|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.httpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Není implementováno|ServerError.NotImplemented
Neplatná autorizace|Neplatná autorizace
Metoda Neplatnostauthorizationm|Neplatná autorizace
Metoda multipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
Platnost tokenu AuthorizationToken|InsufficientAuthorization.AuthorizationVypršela
Nedostatečný rozsah|Nedostatečná autorizace
Blokované|Neplatný požadavek.blokován


## <a name="non-breaking-changes"></a>Nenarušující změny  

### <a name="headers"></a>Hlavičky

- Byla přidána volitelná hlavička požadavku [Pragma.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste Bingu zabránili ve vrácení obsahu uloženého v mezipaměti, hlavičku Pragma nastavte na hodnotu no-cache (například Pragma: no-cache).

### <a name="query-parameters"></a>Parametry dotazu

- Byl přidán parametr dotazu [answerCount.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) Tento parametr slouží k určení počtu odpovědí, které má odpověď obsahovat. Odpovědi jsou vybírány na základě pořadí. Pokud například nastavíte tento parametr na tři (3), odpověď obsahuje tři nejvyšší hodnocené odpovědi.  

- Byl přidán parametr [propojovacího](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) dotazu. Pomocí tohoto parametru spolu s `answerCount` explicitně zahrnout jeden nebo více typů odpovědí, bez ohledu na jejich pořadí. Chcete-li například propagovat videa a obrázky do odpovědi, nastavte propagujete *videa, obrázky*. Seznam odpovědí, které chcete propagovat, se `answerCount` do limitu nezapočítává. Pokud `answerCount` je například `promote` 2 a je nastavena na *videa, obrázky*, může odpověď zahrnovat webové stránky, zprávy, videa a obrázky.

### <a name="object-changes"></a>Změny objektů

- Pole `someResultsRemoved` bylo přidáno do objektu [WebAnswer.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) Pole obsahuje logickou hodnotu, která označuje, zda odpověď vyloučila některé výsledky z webové odpovědi.  
