---
title: Navrhněte hledané výrazy pomocí rozhraní API pro automatické návrhy Bingu
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: cee4f68b734f0c2bec9fd629986ba7f6559f207e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882450"
---
# <a name="suggesting-query-terms"></a>Návrhy termínů dotazu

Obvykle byste vyvolali rozhraní API pro automatické návrhy Bingu pokaždé, když uživatel do vyhledávacího pole aplikace zadá nový znak. Úplnost řetězce dotazu má vliv na relevanci navrhovaných termínů dotazu, které rozhraní API vrátí. Čím je řetězec dotazu úplnější, tím relevantnější seznam navrhovaných termínů dotazů se zobrazí. Například návrhy, které může rozhraní API vracet pro `s` , mohou být méně důležité než dotazy, pro `sailing dinghies`které se vrátí.

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

## <a name="using-suggested-query-terms"></a>Použití výrazů navrhovaných pro dotazování

Každý návrh obsahuje pole `displayText`, `query` a `url`. Pole `displayText` obsahuje navrhovaný dotaz, který můžete použít k vyplnění rozevíracího seznamu vašeho vyhledávacího pole. Musíte zobrazit všechny návrhy, které odpověď obsahuje, v uvedeném pořadí.

Následující příklad ukazuje rozevírací vyhledávací pole s navrhovanými výrazy dotazu z rozhraní API pro automatické návrhy Bingu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Pokud uživatel vybere z rozevíracího seznamu navrhovaný dotaz, použijete termín dotazu v poli `query` k volání [rozhraní API Bingu pro vyhledávání na webu](../../bing-web-search/search-the-web.md) a zobrazení výsledků. Nebo můžete použít adresu URL v poli `url` a odeslat uživatele na stránku s výsledky hledání Bingu.

## <a name="next-steps"></a>Další postup

* [Co je rozhraní API pro automatické návrhy Bingu?](../get-suggested-search-terms.md)