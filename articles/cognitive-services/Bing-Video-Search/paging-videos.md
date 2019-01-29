---
title: Jak lze procházet dostupné videí – Video Bingu pro vyhledávání
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak stránkovat všechna videa, které můžou vrátit Bingu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 26e706b054653b8f0ad1ea14d0a9c2ca97cd227f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181788"
---
# <a name="paging-videos"></a>Videa o stránkování

Když zavoláte rozhraní API pro vyhledávání videí, Bing vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Odhadované celkový počet dostupných výsledků získáte přístup k objektu odpovědi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, které obsahuje odpovědi na Video.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Na stránce prostřednictvím k dispozici videa, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 105. Výchozí hodnota je 35. Skutečný počet doručení může být menší než požadovaný.

`offset` Parametr určuje počet výsledků, chcete-li přeskočit. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).  
  
Pokud chcete zobrazit 20 videa na jedné stránce nastavíte `count` na 20 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Pro každou stránku následné zvýšení `offset` 20 (například 20, 40).  

Následuje příklad, který vyžaduje 20 videa začínající na posunu 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnota se dá použít pro vaší implementace, je třeba určit pouze `offset` parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Obvykle, pokud stránka 35 videa v čase, nastavte `offset` parametr na hodnotu 0 na první žádosti o dotazu a potom zvýší `offset` podle 35 na každý další požadavek. Ale některé výsledky v následné odpovědi může být duplicitní předchozí odpovědi. První dvě videa v odpovědi například může být stejný jako poslední dva videa z předchozí odpovědi.

Chcete-li odstranit duplicitní výsledky, použijte [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) pole `Videos` objektu.

Například pokud chcete na stránku 30 videí současně, nastavíte `count` 30 a `offset` na 0 v první požadavek. V další požadavek, byste měli nastavit `offset` parametr k dotazu `nextOffset` hodnotu.


> [!NOTE]
> Stránkování platí pouze pro vyhledávání videí (/ videa/hledání) a nikoli do nové poznatky z videí (/ videa/details) nebo populární videa (/ videa /).

> [!NOTE]
> `TotalEstimatedAnswers` Pole je odhad celkový počet výsledků hledání můžete načíst aktuálního dotazu.  Pokud nastavíte `count` a `offset` parametry, `TotalEstimatedAnswers` číslo může změnit. 
