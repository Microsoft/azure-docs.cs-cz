---
title: Odesílání požadavků do rozhraní API automatického návrhu Bingu
titleSuffix: Azure Cognitive Services
description: Rozhraní API automatického návrhu bingu vrátí seznam navržených dotazů na základě řetězce částečného dotazu ve vyhledávacím poli. Přečtěte si další informace o odesílání žádostí.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072852"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Odesílání požadavků do rozhraní API automatického návrhu Bingu.

Pokud vaše aplikace odesílá dotazy na některý z rozhraní API vyhledávání Bing, můžete použít rozhraní API automatického návrhu Bingu ke zlepšení možnosti vyhledávání uživatelů. Rozhraní API automatického návrhu bingu vrátí seznam navržených dotazů na základě řetězce částečného dotazu ve vyhledávacím poli. Při zadávání znaků do vyhledávacího pole v aplikaci můžete zobrazit návrhy v rozevíracím seznamu. V tomto článku se dozvíte další informace o odesílání požadavků do tohoto rozhraní API. 

## <a name="bing-autosuggest-api-endpoint"></a>Koncový bod rozhraní API automatického návrhu bingu

**Rozhraní API automatického návrhu Bingu** obsahuje jeden koncový bod, který vrací seznam navržených dotazů z částečného hledaného výrazu.

Chcete-li získat navrhované dotazy pomocí rozhraní `GET` API Bing, odešlete požadavek na následující koncový bod. Pomocí záhlaví a parametrů adresy URL definujte další specifikace.

**Koncový bod:** Vrátí návrhy hledání jako výsledky JSON, které jsou `?q=""`relevantní pro vstup uživatele definované .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Podrobnosti o záhlavích, parametrech, kódech trhu, objektech odpovědí, chybách atd., naleznete v odkazu [rozhraní API automatického návrhu Bingu v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)

Bing **Bing** API podporují akce vyhledávání, které vracejí výsledky podle jejich typu.Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON.
Všechny koncové body podporují dotazy, které vracejí určitý jazyk nebo umístění podle zeměpisné délky, šířky a poloměru hledání.

Úplné informace o parametrech podporovaných jednotlivými koncovými body naleznete v referenčních stránkách pro každý typ.
Příklady základních požadavků pomocí rozhraní Autosuggest API naleznete v tématu [Autosuggest Quickstarts](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Požadavky rozhraní API automatického návrhu bingu

> [!NOTE]
> * Požadavky na rozhraní API automatického návrhu Bingu musí používat protokol HTTPS.

Doporučujeme, aby všechny požadavky pocházely ze serveru. Distribuce klíče jako součást klientské aplikace poskytuje více příležitostí škodlivý přístup třetích stran. Volání ze serveru navíc poskytuje jeden bod upgradu pro budoucí aktualizace.

Požadavek musí obsahovat parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), který obsahuje částečný hledaný termín daného uživatele. Přestože je volitelný, měl by požadavek obsahovat také parametr dotazu [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), který identifikuje trh, ze kterého chcete obdržet výsledky. Seznam volitelných parametrů dotazu najdete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.

Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Přestože jsou volitelné, doporučujeme, aby požadavek obsahoval i následující hlavičky:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Adresa IP a hlavičky klienta jsou důležité pro vrácení obsahu závislého na umístění.

Seznam všech hlaviček žádostí a odpovědí najdete v části s [hlavičkami](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Při volání rozhraní API automatického návrhu Bingu z javascriptu vám integrované funkce zabezpečení prohlížeče mohou bránit v přístupu k hodnotám těchto záhlaví.

Chcete-li tento problém vyřešit, můžete vytvořit požadavek rozhraní API automatického návrhu Bingu prostřednictvím serveru proxy CORS. Odpověď z takového proxy `Access-Control-Expose-Headers` má záhlaví, které whitelists odpovědi záhlaví a zpřístupní je javascriptu.

Je snadné nainstalovat proxy CORS, aby naše [výukové aplikace](../tutorials/autosuggest.md) pro přístup k volitelným záhlavím klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Poté zadejte následující příkaz na příkazovém řádku.

    npm install -g cors-proxy-server

Dále změňte koncový bod rozhraní API automatického návrhu Bingu v souboru HTML takto:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

Požadavky by měly obsahovat všechny navrhované parametry dotazu a záhlaví. 

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

Následující webová skupina návrhů je odpovědí na výše uvedený požadavek. Skupina obsahuje seznam návrhů vyhledávacích dotazů s `displayText` `query`každým `url` návrhem včetně pole , a pole.

Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.  

Pokud uživatel vybere dotaz z rozevíracího seznamu, můžete jej použít k volání jednoho z [rozhraní API vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) a zobrazení výsledků `url` sami nebo odeslání uživatele na stránku s výsledky bingu pomocí vráceného pole.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

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

## <a name="next-steps"></a>Další kroky

- [Co jsou Automatické návrhy Bingu?](../get-suggested-search-terms.md)
- [Referenční materiály rozhraní API pro automatické návrhy Bingu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Získání navržených hledaných výrazů z rozhraní API automatického návrhu Bingu](get-suggestions.md)
