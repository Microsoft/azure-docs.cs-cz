---
title: Procházení dostupných webových stránek – vlastní vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak stránce přes všechny webové stránky, která vrací vlastní vyhledávání Bingu.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: b4e022d6237712d8281c6f4f9332a68821dfd5b1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470077"
---
# <a name="paging-webpages"></a>Webové stránky stránkování 

Při volání rozhraní API pro vlastní vyhledávání Bingu vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které můžou být relevantní pro dotaz. Odhadované celkový počet dostupných výsledků získáte přístup k objektu odpovědi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) pole.  
  
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
  
Na stránce prostřednictvím webové stránky k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 50. Výchozí hodnota je 10. Skutečný počet doručení může být menší než požadovaný.

`offset` Parametr určuje počet výsledků, chcete-li přeskočit. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).  
  
Pokud chcete zobrazit 15 webové stránky na stránku, nastavíte `count` 15 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Pro každou stránku následné zvýšení `offset` 15 (například 15, 30).  
  
Následuje příklad, který požaduje začínající na posunu 45 15 webové stránky.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnota se dá použít pro vaší implementace, je třeba určit pouze `offset` parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

