---
title: 'Hledání vlastní Bing: Stránka prostřednictvím webové stránky k dispozici | Microsoft Docs'
description: Ukazuje, jak na stránku prostřednictvím všechny webové stránky, která vrací Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: f2f545a5a9195fc65515ea716f277723600cbb78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342374"
---
# <a name="paging-webpages"></a>Stránkování webové stránky 

Při volání rozhraní API pro vyhledávání vlastní Bing vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které mohou být relevantní pro dotaz. Odhadovaný celkový počet dostupných výsledků získáte přístup k objektu odpovědí [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) pole.  
  
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
  
Na stránku prostřednictvím webové stránky k dispozici, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které můžete požádat v odpovědi je 50. Výchozí hodnota je 10. Skutečný počet doručit může být nižší než požadovaný.

`offset` Parametr určuje počet výsledků tak, aby přeskočil. `offset` Je počítáno od nuly a měl by být menší než (`totalEstimatedMatches` - `count`).  
  
Pokud chcete zobrazit 15 webové stránky na stránku, nastavíte `count` na 15 a `offset` na hodnotu 0, chcete-li získat první stránky s výsledky. Pro každý další stránku, zvýšit `offset` 15 (například 15, 30).  
  
Následuje příklad, který požaduje 15 webové stránky začínající na posunu 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnotu funguje týkající se vaší implementace, budete muset zadat `offset` parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

