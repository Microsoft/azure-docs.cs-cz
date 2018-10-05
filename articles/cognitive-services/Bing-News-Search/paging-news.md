---
title: Jak lze procházet dostupné vybrané články - Bingu pro vyhledávání zpráv
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak stránce přes všechny články o novinkách, které můžou vrátit nové vyhledávání Bingu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: fff1da15df2e690cd0b37bb82654a4d30159325a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803388"
---
# <a name="paging-news"></a>Stránkování zpráv

Při volání rozhraní API pro vyhledávání zpráv Bingu vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které můžou být relevantní pro dotaz. Odhadované celkový počet dostupných výsledků získáte přístup k objektu odpovědi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, která obsahuje zprávy odpovědi.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Na stránce prostřednictvím články k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 100. Výchozí hodnota je 10. Skutečný počet doručení může být menší než požadovaný.

`offset` Parametr určuje počet výsledků, chcete-li přeskočit. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).  

Pokud chcete zobrazit 20 článků na jedné stránce nastavíte `count` na 20 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Pro každou stránku následné zvýšení `offset` 20 (například 20, 40).  
  
Následuje příklad, který vyžaduje 20 články o novinkách začínající na posunu 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Pokud výchozí `count` hodnota funguje pro implementaci, zadejte pouze `offset` parametr dotazu, jak je znázorněno v následujícím příkladu:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Stránkování platí pouze pro vyhledávání zpráv (/ news/hledání) a nechcete populárních témat (/ news/trendingtopics) nebo nové kategorie (/ zpráv).