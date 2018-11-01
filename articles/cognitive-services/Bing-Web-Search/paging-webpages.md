---
title: Jak stránkovat výsledky hledání – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak stránkovat výsledky hledání z rozhraní API webové vyhledávání Bingu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: erhopf
ms.openlocfilehash: e98407dface348dfdbc99a1146d50c46f3bb03a7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739239"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Jak stránkovat výsledky z rozhraní API webové vyhledávání Bingu

Při volání rozhraní API pro webové vyhledávání Bingu vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které můžou být relevantní pro dotaz. Odhadované celkový počet dostupných výsledků získáte přístup k objektu odpovědi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) pole.  

Následující příklad ukazuje `totalEstimatedMatches` pole, které obsahuje webovou odpověď.  

```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

Na stránce prostřednictvím webové stránky k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) parametrů dotazu.  

`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 50. Výchozí hodnota je 10. Skutečný počet doručení může být menší než požadovaný.

`offset` Parametr určuje počet výsledků, chcete-li přeskočit. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).  

Pokud chcete zobrazit 15 webové stránky na stránku, nastavíte `count` 15 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Pro každou stránku následné zvýšení `offset` 15 (například 15, 30).  

Následující příklad požádá o 15 webových stránkách začínající na posunu 45.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Pokud výchozí `count` hodnota se dá použít pro vaší implementace, je třeba určit pouze `offset` parametr dotazu.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Webové rozhraní API hledání vrátí výsledky, které zahrnují webové stránky a může taky obsahovat obrázků, videí a zpráv. Pokud je stránkování výsledků vyhledávání, jsou stránkování [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) odpovědí a není další odpovědi, jako je Image nebo zprávy. Například pokud nastavíte `count` na 50, získáte zpět 50 webovou stránku výsledků, ale odpověď může obsahovat další odpovědi a výsledky. Odpověď může obsahovat například 15 imagí a 4 články o novinkách. Je také možné, že výsledky mohou zahrnovat zprávy na stránce první, ale ne na druhé stránce, nebo naopak.   

Pokud zadáte `responseFilter` parametr dotazu a nezahrnují webové stránky v seznamu filtru, nepoužívejte `count` a `offset` parametry. 

> [!NOTE]
> `TotalEstimatedAnswers` Pole je odhad celkový počet výsledků hledání můžete načíst aktuálního dotazu.  Pokud nastavíte `count` a `offset` parametry, `TotalEstimatedAnswers` číslo může změnit. 
