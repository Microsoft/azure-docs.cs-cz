---
title: 'Rychlý start: Rozhraní API pro automatické návrhy Bingu'
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak začít používat rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: quickstart
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 446d271854a4e45bcea8c261a0dc078e549f8229
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830749"
---
# <a name="quickstart-making-your-first-autosuggest-query"></a>Rychlý start: Odeslání prvního dotazu rozhraní API pro automatické návrhy Bingu

Před prvním voláním je potřeba získat klíč předplatného služeb Cognitive Services. Postup, jak získat klíč, naleznete v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Pokud chcete získat výsledky hledávání na webu, odešlete požadavek GET na následující koncový bod:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Koncový bod v7 Preview:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

Požadavek musí používat protokol HTTPS.

Doporučujeme, aby byly všechny požadavky odeslány ze serveru. Distribuce klíče v rámci klientské aplikace nabízí více příležitostí pro přístup škodlivých třetích stran. Voláním ze serveru také zajistíte, že bude stačit upgradovat pouze jediný bod na budoucí verze rozhraní API.

Požadavek musí obsahovat parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), který obsahuje částečný hledaný termín daného uživatele. Přestože je volitelný, měl by požadavek obsahovat také parametr [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) dotazu, který identifikuje trh, ze kterého chcete obdržet výsledky. Seznam volitelných parametrů dotazu najdete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.

Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Přestože jsou volitelné, doporučujeme, aby požadavek obsahoval i následující hlavičky:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

IP a hlavičky klienta jsou důležité pro vrácení obsahu závislého na umístění.

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [Hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>Požadavek

Požadavek by měl obsahovat všechny navrhované parametry dotazu a hlavičky. Toto rozhraní API byste volali pokaždé, když uživatel do vyhledávacího pole zadá nový znak. Úplnost řetězce dotazu má vliv na relevanci navrhovaných termínů dotazu, které rozhraní API vrátí. Čím je řetězec dotazu úplnější, tím relevantnější seznam navrhovaných termínů dotazu se zobrazí. Například návrhy, které může rozhraní API vrátit pro *s*, budou pravděpodobně méně relevantní než dotazy, které vrátí pro *sailing dinghies*. 

Následující příklad ukazuje požadavek, který vrací navrhované řetězce dotazu pro *sail*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Požadavek v7 Preview:

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

Pokud uživatel vybere dotaz z rozevíracího seznamu, můžete řetězec dotazu použít v poli `query` k volání [rozhraní API pro vyhledávání Bingu](../bing-web-search/search-the-web.md) a zobrazení výsledků. Nebo můžete použít adresu URL v poli `url` a přesměrovat uživatele na stránku s výsledky hledání Bingu.

```  
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

Vyzkoušejte si rozhraní API. Přejděte do [testovací konzoly rozhraní API pro automatické návrhy Bingu](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Podrobnosti o využití objektů odpovědi najdete v tématu [Získání navrhovaných hledaných termínů](./get-suggested-search-terms.md).
