---
title: Navrhněte hledané výrazy s rozhraním API pro automatické návrhy Bingu
titlesuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 293dcaadfc20116455983b3fc0069f9e9df3f843
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549253"
---
# <a name="suggesting-query-terms"></a>Návrhy termínů dotazu

Obvykle by volat rozhraní API pro automatické návrhy Bingu pokaždé, když uživatel zadá znak nového vyhledávacího pole vaší aplikace. Úplnost řetězce dotazu má vliv na relevanci navrhovaných termínů dotazu, které rozhraní API vrátí. Čím je řetězec dotazu úplnější, tím relevantnější seznam navrhovaných termínů dotazů se zobrazí. Například návrhy, které můžou vrátit rozhraní API pro `s` můžou mít méně závažné, než se vrací pro dotazy `sailing dinghies`.

## <a name="example-request"></a>Příklad požadavku

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

## <a name="using-suggested-query-terms"></a>Pomocí navrhované výrazy

Každý návrh obsahuje pole `displayText`, `query` a `url`. Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.

Následující příklad ukazuje rozevíracího seznamu vyhledávací pole s navrhované výrazy z rozhraní API pro automatické návrhy Bingu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Pokud uživatel vybere z rozevíracího seznamu navrhovaný dotaz, použijete termín dotazu v poli `query` k volání [rozhraní API Bingu pro vyhledávání na webu](../../bing-web-search/search-the-web.md) a zobrazení výsledků. Nebo můžete použít adresu URL v poli `url` a odeslat uživatele na stránku s výsledky hledání Bingu.

## <a name="next-steps"></a>Další postup

* [Co je rozhraní API pro automatické návrhy Bingu?](../get-suggested-search-terms.md)