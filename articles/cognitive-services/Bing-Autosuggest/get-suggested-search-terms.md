---
title: Co jsou Automatické návrhy Bingu?
titlesuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: b5959e014b7e531b8f52fcbe6f6492576eedd61a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875667"
---
# <a name="what-is-bing-autosuggest"></a>Co jsou Automatické návrhy Bingu?

Pokud odesíláte dotazy do jakéhokoli rozhraní API pro vyhledávání Bingu, můžete použít rozhraní API pro automatické návrhy Bingu a zlepšit tak hledání pomocí vyhledávacího pole. Rozhraní API pro automatické návrhy Bingu vrací seznam navrhovaných dotazů založený na části řetězce dotazu, který uživatel do vyhledávacího pole zadává. Podívejte se na návrhy v rozevíracím seznamu vyhledávacího pole. Navrhované termíny jsou založené na navrhovaných dotazech, které hledali jiní uživatelé, a na záměru uživatele.

Obvykle byste toto rozhraní API volali pokaždé, když uživatel do vyhledávacího pole zadá nový znak. Úplnost řetězce dotazu má vliv na relevanci navrhovaných termínů dotazu, které rozhraní API vrátí. Čím je řetězec dotazu úplnější, tím relevantnější seznam navrhovaných termínů dotazů se zobrazí. Například návrhy, které může rozhraní API vrátit pro *s*, budou pravděpodobně méně relevantní než dotazy, které vrátí pro *sailing dinghies*.

## <a name="getting-suggested-search-terms"></a>Získávání navrhovaných hledaných termínů

Následující příklad ukazuje požadavek, který vrací navrhované řetězce dotazu pro *sail*. Při nastavování parametru dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query) nezapomeňte zakódovat částečný termín dotazu uživatele s použitím kódování URL. Pokud například uživatel zadal *sailing les*, nastavte parametr `q` na `sailing+les` nebo `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Následující odpověď obsahuje seznam objektů [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction), které obsahují navrhované termíny dotazu.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

Každý návrh obsahuje pole `displayText`, `query` a `url`. Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.

Následující obrázek ukazuje příklad vyhledávacího pole s rozevíracím seznamem a navrhovanými termíny dotazu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Pokud uživatel vybere z rozevíracího seznamu navrhovaný dotaz, použijete termín dotazu v poli `query` k volání [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md) a zobrazení výsledků. Nebo můžete použít adresu URL v poli `url` a odeslat uživatele na stránku s výsledky hledání Bingu.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

Pokud chcete rychle začít s vaším prvním požadavkem, projděte si popis [vytvoření prvního dotazu](quickstarts/csharp.md).

Seznamte se s referenčními informacemi k [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na navrhované výrazy dotazů, a definice objektů odpovědi.

Naučte prohledávat web s využitím [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md).

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md), abyste neporušili žádná pravidla používání výsledků hledání.
