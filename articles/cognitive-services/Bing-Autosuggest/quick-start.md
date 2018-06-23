---
title: Rychlý start rozhraní API pro automatické návrhy | Microsoft Docs
description: Ukazuje, jak začít používat rozhraní API pro automatické návrhy v Bingu.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343430"
---
# <a name="making-your-first-autosuggest-query"></a>Provedení svůj první dotaz automatických návrhů

Předtím, než bude možné vytvářet první volání, které je potřeba získat předplatné klíč kognitivní služby. Získat klíč, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Výsledky hledání webové získáte by odeslat požadavek GET na následující koncový bod:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Koncový bod v7 Preview:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

Žádost musí používat protokol HTTPS.

Doporučujeme vám, že všechny požadavky pocházejí ze serveru. Distribuce klíč v rámci klientské aplikace poskytuje další možnost pro škodlivý třetích stran k přístupu. Navíc volání ze serveru poskytuje snadný upgrade pro budoucí verze rozhraní API.

Musíte zadat požadavek [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) parametr dotazu, který obsahuje uživatele částečné hledaný termín. Přestože je volitelné, žádost by také určit [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) parametr dotazu, který identifikuje na trhu, kam chcete výsledky pocházet z. Seznam parametrů dotazu volitelné, najdete v tématu [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí být kódovaná adresou URL.

Musíte zadat požadavek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) záhlaví. I když je volitelné, vám doporučujeme, aby se také určit následující hlavičky:

- [Uživatelský Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-vyhledávání – když](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [Umístění X vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Hlavičky protokolu IP a umístění klienta jsou důležité pro vrácení obsahu vědět umístění.

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>Požadavek

Požadavek by měly obsahovat všechny parametry navrhované dotazu a hlavičky. Toto rozhraní API by volání pokaždé, když uživatel zadá znak nového do vyhledávacího pole. Vrátí úplnosti dopad řetězec dotazu, relevance navrhovaných dotazu podmínek, které rozhraní API. Čím více dokončit řetězce dotazu, více relevantní, seznam navrhované, že jsou podmínky dotazu. Například návrhy, které můžou vrátit rozhraní API pro *s* by mohly být méně důležité než dotazy vrátí pro *řízení dinghies*. 

Následující příklad ukazuje požadavek, který vrátí řetězce dotazu navržené pro *vedení*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Žádost o v7 Preview:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Pokud je poprvé volání některé z rozhraní API služby Bing, neobsahují záhlaví ID klienta. Zahrnout pouze záhlaví ID klienta, pokud jste dříve volat rozhraní API pro Bing a Bing vrátil ID klienta pro uživatele a zařízení kombinaci.

Následující obrázek znázorňuje odpovědi na předchozí požadavek. Odpověď obsahuje skupinu webových návrhu, která obsahuje seznam návrhy vyhledávání dotazu. Zahrnuje každého návrhu `displayText`, `query`, a `url` pole.

`displayText` Pole obsahuje navrhované dotaz, který byste použili k naplnění rozevíracího seznamu vyhledávacího pole. Je třeba zobrazit všechny návrhy, které jsou zahrnuty v odpovědi, a v uvedeném pořadí.  

Pokud uživatel vybere dotazu z rozevíracího seznamu, můžete použít řetězce dotazu v `query` pole k volání [rozhraní API služby Bing Search](../bing-web-search/search-the-web.md) a zobrazit výsledky. Nebo můžete použít adresu URL v `url` pole, které chcete uživateli odeslat do služby Bing search stránka s výsledky.

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

## <a name="next-steps"></a>Další postup

Vyzkoušejte rozhraní API. Přejděte na [testování konzoly rozhraní API pro automatické návrhy](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Podrobnosti o spotřebě objektů odpovědi najdete v tématu [získávání navrhované hledaných termínů](./get-suggested-search-terms.md).
