---
title: Stránkování prostřednictvím výsledků vyhledávání zpráv Bingu
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak stránkovat články o novinkách, které vrací rozhraní API pro vyhledávání zpráv Bingu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: db43912e71e196bc01507e904e3a7d3a587a35ce
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192073"
---
# <a name="how-to-page-through-news-search-results"></a>Jak stránkovat výsledky hledání zpráv

Při volání rozhraní API pro vyhledávání zpráv Bingu vrátí seznam výsledků, které jsou relevantní pro váš dotaz. Odhadované celkový počet dostupných výsledků získáte přístup k objektu odpovědi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, která obsahuje zprávy odpovědi.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Na stránce prostřednictvím články k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) parametrů dotazu.  
 

|Parametr  |Popis  |
|---------|---------|
|`count`     | Určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 100. Výchozí hodnota je 10. Skutečný počet doručení může být menší než požadovaný.        |
|`offset`     | Určuje počet výsledků pro přeskočení. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).          |

Například pokud chcete zobrazit 20 článků na jedné stránce nastavíte `count` na 20 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Pro každou stránku následné zvýšení `offset` 20 (například 20, 40).  
  
Následující příklad požádá o 20 články o novinkách začínající na posunu 40.  

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

> [!NOTE]
> `TotalEstimatedAnswers` Pole je odhad celkový počet výsledků hledání můžete načíst aktuálního dotazu.  Pokud nastavíte `count` a `offset` parametry, `TotalEstimatedAnswers` číslo může změnit. 
