---
title: Tom, jak procházet dostupné příspěvků | Microsoft Docs
description: Ukazuje, jak na stránku prostřednictvím všechny články, které můžou vrátit Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342392"
---
# <a name="paging-news"></a>Příspěvky stránkování

Při volání rozhraní API pro vyhledávání zprávy Bing vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které mohou být relevantní pro dotaz. Odhadovaný celkový počet dostupných výsledků získáte přístup k objektu odpovědí [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, které obsahuje odpovědi na zprávy.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Na stránku prostřednictvím články k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které můžete požádat v odpovědi je 100. Výchozí hodnota je 10. Skutečný počet doručit může být nižší než požadovaný.

`offset` Parametr určuje počet výsledků tak, aby přeskočil. `offset` Je počítáno od nuly a měl by být menší než (`totalEstimatedMatches` - `count`).  

Pokud chcete zobrazit články 20 na každé stránce, nastavíte `count` až 20 číslic a `offset` na hodnotu 0, chcete-li získat první stránky s výsledky. Pro každý další stránku, zvýšit `offset` ve 20 (například 20, 40).  
  
Následuje příklad, který požaduje začínající na posunu 40 20 články s příspěvky.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Pokud výchozí `count` hodnotu funguje týkající se vaší implementace, zadejte pouze `offset` parametr dotazu, jak je znázorněno v následujícím příkladu:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Stránkování platí pouze pro vyhledávání zprávy (nebo zprávy nebo hledání) a nechcete trendů témata (nebo zprávy nebo trendingtopics) nebo kategorie zprávy (nebo zprávy).