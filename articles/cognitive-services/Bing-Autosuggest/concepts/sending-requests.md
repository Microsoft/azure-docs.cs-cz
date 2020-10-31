---
title: Odesílání požadavků do rozhraní API pro automatické návrhy Bingu
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro automatické návrhy Bingu vrátí seznam navrhovaných dotazů na základě částečného řetězce dotazu ve vyhledávacím poli. Přečtěte si další informace o odesílání požadavků.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: aad00d60c5a1b75e200b49b0cdcf0d396012a5f9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101948"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Odesílání požadavků do rozhraní API pro automatické návrhy Bingu.

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Pokud vaše aplikace odesílá dotazy do některého z rozhraní API pro vyhledávání Bingu, můžete pomocí rozhraní API pro automatické návrhy Bingu vylepšit možnosti vyhledávání vašich uživatelů. Rozhraní API pro automatické návrhy Bingu vrátí seznam navrhovaných dotazů na základě částečného řetězce dotazu ve vyhledávacím poli. Do vyhledávacího pole ve vaší aplikaci se jako znaky zadávají v rozevíracím seznamu, kde můžete zobrazit návrhy. V tomto článku se dozvíte víc o odesílání požadavků do tohoto rozhraní API. 

## <a name="bing-autosuggest-api-endpoint"></a>rozhraní API pro automatické návrhy Bingu koncový bod

**Rozhraní API pro automatické návrhy Bingu** obsahuje jeden koncový bod, který vrací seznam navrhovaných dotazů z částečného hledaného výrazu.

Pokud chcete získat navrhované dotazy pomocí rozhraní API Bingu, odešlete `GET` požadavek na následující koncový bod. K definování dalších specifikací použijte záhlaví a parametry URL.

**Koncový bod:** Vrátí návrhy hledání jako výsledky JSON, které jsou relevantní pro vstup uživatele definovaný pomocí `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. naleznete v tématu [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) reference.

Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON.
Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro automatické návrhy najdete v tématu [rychlé starty](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)pro automatické návrhy.

## <a name="bing-autosuggest-api-requests"></a>rozhraní API pro automatické návrhy Bingu žádosti

> [!NOTE]
> * Požadavky na rozhraní API pro automatické návrhy Bingu musí používat protokol HTTPS.

Doporučujeme, aby všechny požadavky pocházely ze serveru. Při distribuci klíče jako součásti klientské aplikace je k dispozici více příležitostí ke škodlivému přístupu třetích stran. Kromě toho volání ze serveru poskytuje jeden bod upgradu pro budoucí aktualizace.

Požadavek musí obsahovat parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), který obsahuje částečný hledaný termín daného uživatele. Přestože je volitelný, měl by požadavek obsahovat také parametr dotazu [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), který identifikuje trh, ze kterého chcete obdržet výsledky. Seznam volitelných parametrů dotazu najdete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.

Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Přestože jsou volitelné, doporučujeme, aby požadavek obsahoval i následující hlavičky:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Adresa IP a hlavičky klienta jsou důležité pro vrácení obsahu závislého na umístění.

Seznam všech hlaviček žádostí a odpovědí najdete v části s [hlavičkami](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Při volání rozhraní API pro automatické návrhy Bingu z JavaScriptu, integrované funkce zabezpečení prohlížeče vám můžou zabránit v přístupu k hodnotám těchto hlaviček.

Pokud to chcete vyřešit, můžete žádost o rozhraní API pro automatické návrhy Bingu vytvořit prostřednictvím serveru proxy CORS. Odpověď z takového proxy serveru obsahuje `Access-Control-Expose-Headers` hlavičku, která filtruje hlavičky odpovědí a zpřístupňuje je pro JavaScript.

Je snadné nainstalovat proxy CORS, aby mohla naše [aplikace](../tutorials/autosuggest.md) získat přístup k volitelným hlavičkám klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak na příkazovém řádku zadejte následující příkaz.

```console
npm install -g cors-proxy-server
```

Dále změňte koncový bod rozhraní API pro automatické návrhy Bingu v souboru HTML na:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

```console
cors-proxy-server
```

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

Požadavky by měly zahrnovat všechny navrhované parametry dotazu a hlavičky. 

Následující příklad ukazuje požadavek, který vrací navrhované řetězce dotazu pro *sail* .

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. Hlavičku ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení.

Následující skupina návrhů webu představuje odpověď na výše uvedený požadavek. Skupina obsahuje seznam návrhů vyhledávacích dotazů, přičemž každý návrh zahrnuje `displayText` `query` pole, a `url` .

Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.  

Pokud uživatel vybere dotaz z rozevíracího seznamu, můžete jej použít k volání jedné z [rozhraní API pro vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) a zobrazení výsledků sami nebo můžete uživatele odeslat na stránku výsledků Bingu pomocí vráceného `url` pole.

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
- [Načítají se Doporučené hledané výrazy z rozhraní API pro automatické návrhy Bingu.](get-suggestions.md)
