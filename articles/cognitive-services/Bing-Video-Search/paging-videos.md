---
title: Jak lze procházet k dispozici videa | Microsoft Docs
description: Ukazuje, jak na stránku prochází všechna videa, které můžou vrátit Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342381"
---
# <a name="paging-videos"></a>Stránkování videa

Při volání rozhraní API pro vyhledávání Video Bing vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které jsou relevantní pro dotaz. Odhadovaný celkový počet dostupných výsledků získáte přístup k objektu odpovědí [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, které zahrnuje Video odpovědí.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Na stránku prostřednictvím k dispozici videa, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které můžete požádat v odpovědi je 105. Výchozí hodnota je 35. Skutečný počet doručit může být nižší než požadovaný.

`offset` Parametr určuje počet výsledků tak, aby přeskočil. `offset` Je počítáno od nuly a měl by být menší než (`totalEstimatedMatches` - `count`).  
  
Pokud chcete zobrazit 20 videa na stránce, nastavíte `count` až 20 číslic a `offset` na hodnotu 0, chcete-li získat první stránky s výsledky. Pro každý další stránku, zvýšit `offset` ve 20 (například 20, 40).  

Následuje příklad, který požaduje 20 videa začínající na posunu 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnotu funguje týkající se vaší implementace, budete muset zadat `offset` parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Obvykle, pokud stránka 35 videa současně nastavíte `offset` parametr na hodnotu 0 na první požadavek dotazu a poté zvýšit `offset` podle 35 na každý další požadavek. Ale některé výsledky v odpovědi na následné může být duplicitní předchozí odpovědi. První dva videa v odpovědi například může být stejný jako poslední dva videa z předchozí odpovědi.

Chcete-li odstranit duplicitní výsledky, použijte [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) pole z `Videos` objektu.

Například pokud chcete na stránku 30 videí najednou, nastavíte `count` na 30 a `offset` na 0 v první požadavek. V další požadavek, byste měli nastavit `offset` parametr k dotazu `nextOffset` hodnotu.


> [!NOTE]
> Stránkování platí pouze pro vyhledávání videa (nebo videa nebo hledání) a ne k video přehledy (/ videa/podrobnosti) nebo trendů videa (nebo videa nebo trendů).