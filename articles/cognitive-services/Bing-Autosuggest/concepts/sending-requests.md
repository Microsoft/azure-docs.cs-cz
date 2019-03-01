---
title: Odesílání požadavků na rozhraní API pro automatické návrhy Bingu
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak posílat žádosti na rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 597ef48fd7499a9d33b214b182d6dd1354756cdf
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011876"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Odesílání požadavků na rozhraní API pro automatické návrhy Bingu.

Pokud vaše aplikace posílá dotazy na některý z rozhraní API pro vyhledávání Bingu, můžete zlepšit možnosti vašich uživatelů hledání rozhraní API pro automatické návrhy Bingu. Rozhraní API pro automatické návrhy Bingu vrátí seznam navrhovaných dotazů na základě řetězce dotazu částečné do vyhledávacího pole. Jak znaků se zadávají do vyhledávacího pole ve vaší aplikaci, můžete zobrazit návrhy v rozevíracím seznamu. Další informace o odesílání požadavků k tomuto rozhraní API pomocí tohoto článku.

## <a name="bing-autosuggest-api-endpoint"></a>Koncový bod rozhraní API pro automatické návrhy Bingu

**Rozhraní API pro automatické návrhy Bingu** zahrnuje jeden koncový bod, který vrátí seznam navrhovaných dotazů z hledaný termín.

Chcete-li získat navrhované dotazy pomocí rozhraní API Bingu pro odeslání `GET` požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

**Koncový bod:** Návrhy hledání vrátí jako výsledky JSON, které se týkají vstupu uživatele určené `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) odkaz.

**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.
Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky na používání rozhraní API pro automatické návrhy najdete v tématu [rychlých startů pro automatické návrhy](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Žádosti rozhraní API pro automatické návrhy Bingu

> [!NOTE]
> Požadavky na rozhraní API pro automatické návrhy Bingu musí používat protokol HTTPS.

Doporučujeme, aby všechny požadavky pocházely ze serveru. Distribuce klíč v rámci klientské aplikace poskytuje další příležitosti škodlivým přístupem třetích stran. Kromě toho volání ze serveru poskytuje jediný bod upgradu pro budoucí aktualizace.

Požadavek musí obsahovat parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), který obsahuje částečný hledaný termín daného uživatele. Přestože je volitelný, měl by požadavek obsahovat také parametr dotazu [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), který identifikuje trh, ze kterého chcete obdržet výsledky. Seznam volitelných parametrů dotazu najdete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.

Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Přestože jsou volitelné, doporučujeme, aby požadavek obsahoval i následující hlavičky:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

IP a hlavičky klienta jsou důležité pro vrácení obsahu závislého na umístění.

Seznam všech hlaviček žádostí a odpovědí najdete v části s [hlavičkami](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Při volání rozhraní API pro automatické návrhy Bingu z jazyka JavaScript v prohlížeči integrovaných funkcí zabezpečení může můžete zabránit v přístupu k hodnoty těchto hlaviček.

Chcete-li tento problém vyřešit, můžete provést požadavek na rozhraní API pro automatické návrhy Bingu prostřednictvím proxy serveru CORS. Odpověď od takový proxy server má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi seznamů povolených a zpřístupňuje je pro jazyk JavaScript.

Je snadné k instalaci proxy CORS a povolit naše [ukázková aplikace](../tutorials/autosuggest.md) záhlaví volitelný klientský přístup. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Potom zadejte následující příkaz z příkazového řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte v souboru HTML, aby koncový bod rozhraní API pro automatické návrhy Bingu:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

Požadavky by měl obsahovat všechny parametry navrhované dotazu a záhlaví. 

Následující příklad ukazuje požadavek, který vrací navrhované řetězce dotazu pro *sail*.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. Hlavičku ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení.

Následující příklad ukazuje odpověď na předchozí požadavek. Odpověď zahrnuje skupinu návrhů webů, která obsahuje seznam návrhů vyhledávacích dotazů. Každý návrh obsahuje pole `displayText`, `query` a `url`.

Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.  

Pokud uživatel vybere z rozevíracího seznamu dotazu, můžete použít k volání jedné z [rozhraní API Bingu pro vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) a zobrazení výsledků nebo uživatele poslat na stránce výsledky Bingu pomocí vráceného `url` pole. Následující příklad používá rozhraní API webové vyhledávání Bingu.

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Další postup

- [Co jsou automatické návrhy Bingu?](../get-suggested-search-terms.md)
- [Referenční materiály rozhraní API pro automatické návrhy Bingu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference)
- [Získávání navrhované hledané termíny z rozhraní API pro automatické návrhy Bingu](get-suggestions.md)
