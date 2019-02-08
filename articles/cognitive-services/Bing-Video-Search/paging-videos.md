---
title: Jak lze procházet dostupné videí – Video Bingu pro vyhledávání
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak na stránce prostřednictvím všech videí vrácený API Bingu pro vyhledávání videí.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: d75a71fe5ba5a275aa8703bc59398cc8a7a5a622
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867133"
---
# <a name="paging-through-video-search-results"></a>Stránkování výsledků vyhledávání videí

API pro vyhledávání videí Bingu vrátí podmnožinu výsledků hledání, nalezen pro váš dotaz. Podle stránkování prostřednictvím těchto výsledků s následující volání rozhraní API, můžete získat a zobrazí je ve vaší aplikaci.

> [!NOTE]
> Stránkování platí pouze pro vyhledávání videí (/ videa/hledání) a nikoli do nové poznatky z videí (/ videa/details) nebo populární videa (/ videa /).

## <a name="total-estimated-matches"></a>Celkový odhadovaný shody

Chcete-li získat odhadovaný počet nalezených výsledků, použijte [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) pole v odpovědi JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Stránkování prostřednictvím videí

Na stránce prostřednictvím k dispozici videa, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametrů dotazu při odesílání vaší žádosti.  
  

|Parametr  |Popis  |
|---------|---------|
|`count`     | Určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 100. Výchozí hodnota je 10. Skutečný počet doručení může být menší než požadovaný.        |
|`offset`     | Určuje počet výsledků pro přeskočení. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).          |

Například pokud chcete zobrazit 20 článků na jedné stránce nastavíte `count` na 20 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Pro každou stránku následné zvýšení `offset` 20 (například 20, 40).  
  
Následující příklad požádá o 20 videa začínající na posunu 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud nechcete použít výchozí hodnotu pro [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count), budete muset zadat `offset` parametr, jako v následujícím příkladu dotazu.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud stránkovat 35 videa v čase, nastavte `offset` parametr na hodnotu 0 na první žádosti o dotazu a potom zvýší `offset` podle 35 na každý další požadavek. Další odpovědi na některé výsledky však může obsahovat duplicitní video výsledky z předchozí odpovědi. První dvě videa v odpovědi například může být stejný jako poslední dva videa z předchozí odpovědi.

Chcete-li odstranit duplicitní výsledky, použijte [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) pole `Videos` objektu.

Například pokud chcete na stránku 30 videí současně, můžete nastavit `count` 30 a `offset` na 0 v první požadavek. V další požadavek, byste měli nastavit `offset` parametr k dotazu `nextOffset` hodnotu.

> [!NOTE]
> `TotalEstimatedAnswers` Pole je odhad celkový počet výsledků hledání můžete načíst aktuálního dotazu.  Pokud nastavíte `count` a `offset` parametry, `TotalEstimatedAnswers` číslo může změnit. 
  
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Získejte nové poznatky z videí](video-insights.md)
