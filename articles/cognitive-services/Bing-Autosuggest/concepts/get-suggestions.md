---
title: Navrhování hledaných výrazů pomocí rozhraní API pro automatické návrhy bingu
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje koncept navrhování termíny dotazu pomocí Bing Autosuggest ROZHRANÍ API a dopad délky dotazu na relevanci.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072888"
---
# <a name="suggesting-query-terms"></a>Návrhy termínů dotazu

Obvykle byste volat Bing Autosuggest ROZHRANÍ API pokaždé, když uživatel zadá nový znak ve vyhledávacím poli vaší aplikace. Úplnost řetězce dotazu má vliv na relevanci navrhovaných termínů dotazu, které rozhraní API vrátí. Čím je řetězec dotazu úplnější, tím relevantnější seznam navrhovaných termínů dotazů se zobrazí. Například návrhy, které rozhraní `s` API může vrátit pro jsou pravděpodobně méně `sailing dinghies`relevantní než dotazy, které vrátí pro .

## <a name="example-request"></a>Příklad požadavku

Následující příklad ukazuje požadavek, který vrací navrhované řetězce dotazu pro *sail*. Při nastavování parametru dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) nezapomeňte zakódovat částečný termín dotazu uživatele s použitím kódování URL. Pokud například uživatel zadal *sailing les*, nastavte parametr `q` na `sailing+les` nebo `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Následující odpověď obsahuje seznam objektů [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction), které obsahují navrhované termíny dotazu.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Použití doporučených termínů dotazu

Každý návrh obsahuje pole `displayText`, `query` a `url`. Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.

Následující příklad ukazuje rozevírací vyhledávací pole s navrženými termíny dotazu z rozhraní API automatického návrhu Bingu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Pokud uživatel vybere z rozevíracího seznamu navrhovaný dotaz, použijete termín dotazu v poli `query` k volání [rozhraní API Bingu pro vyhledávání na webu](../../bing-web-search/search-the-web.md) a zobrazení výsledků. Nebo můžete použít adresu URL v poli `url` a odeslat uživatele na stránku s výsledky hledání Bingu.

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro automatické návrhy Bingu?](../get-suggested-search-terms.md)