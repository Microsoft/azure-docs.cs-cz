---
title: Tom, jak procházet dostupné webové stránky | Microsoft Docs
description: Ukazuje, jak na stránku prostřednictvím všechny webové stránky, která vrací Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: bf29783246c603270d59b20b63027fccdbd45b89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342413"
---
# <a name="paging-webpages"></a>Stránkování webové stránky 

Při volání webového rozhraní API vyhledávání Bing vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které mohou být relevantní pro dotaz. Odhadovaný celkový počet dostupných výsledků získáte přístup k objektu odpovědí [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, které zahrnuje webové odpovědí.  
  
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
  
Na stránku prostřednictvím webové stránky k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které můžete požádat v odpovědi je 50. Výchozí hodnota je 10. Skutečný počet doručit může být nižší než požadovaný.

`offset` Parametr určuje počet výsledků tak, aby přeskočil. `offset` Je počítáno od nuly a měl by být menší než (`totalEstimatedMatches` - `count`).  
  
Pokud chcete zobrazit 15 webové stránky na stránku, nastavíte `count` na 15 a `offset` na hodnotu 0, chcete-li získat první stránky s výsledky. Pro každý další stránku, zvýšit `offset` 15 (například 15, 30).  
  
Následující příklad požádá o 15 webové stránky začínající na posunu 45.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Pokud výchozí `count` hodnotu funguje týkající se vaší implementace, budete muset zadat `offset` parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Hledání webového rozhraní API vrátí výsledky, které zahrnují webové stránky a může zahrnovat obrázků, videí a zprávy. Pokud je stránkování výsledků vyhledávání, jsou stránkování [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) odpovědí a není jiné odpovědi například obrázky nebo zprávy. Například pokud nastavíte `count` na 50, se zobrazí zpět 50 webovou stránku výsledků, ale odpověď může zahrnovat výsledky pro jiné odpovědi také. Odpovědi může obsahovat třeba 15 bitové kopie a 4 články s příspěvky. Je také možné, že výsledky mohou být zprávy na první stránce, ale ne druhé stránce, a naopak.   
    
Pokud zadáte `responseFilter` parametr dotazu a nezahrnují webové stránky v seznamu filtrů, nepoužívejte `count` a `offset` parametry.  
