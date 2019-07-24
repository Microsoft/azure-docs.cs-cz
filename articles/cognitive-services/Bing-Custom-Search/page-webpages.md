---
title: Stránkou prostřednictvím dostupných webových stránek – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak procházet všechny webové stránky, které Vlastní vyhledávání Bingu mohou vracet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405040"
---
# <a name="paging-webpages"></a>Stránkovací webové stránky 

Když zavoláte rozhraní API pro vlastní vyhledávání, Bing vrátí seznam výsledků. Seznam je podmnožinou celkového počtu výsledků, které mohou být pro dotaz relevantní. K získání odhadovaného celkového počtu dostupných výsledků získáte přístup k poli [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) objektu odpovědi.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, které webová odpověď zahrnuje.  
  
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
  
K stránkám prostřednictvím dostupných webových stránek použijte parametry dotazu [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) a [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) .  
  
`count` Parametr určuje počet výsledků, které mají být vráceny v odpovědi. Maximální počet výsledků, které mohou být požadovány v odpovědi, je 50. Výchozí hodnota je 10. Skutečný počet dodaných hodnot může být menší, než je požadováno.

`offset` Parametr určuje počet výsledků, které se mají přeskočit. Hodnota `offset` je počítána od nuly a měla by být menší`totalEstimatedMatches`než ( - `count`).  
  
Pokud chcete zobrazit 15 webových stránek na stránku, měli byste nastavit `count` na 15 a `offset` na 0, abyste získali první stránku výsledků. Pro každou následnou stránku byste narůsti `offset` o 15 (například 15, 30).  
  
Následující příklad ukazuje příklad, který požaduje 15 webových stránek počínaje posunem 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnota pro vaši implementaci funguje, stačí `offset` zadat parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Toto `TotalEstimatedAnswers` pole představuje odhad celkového počtu výsledků hledání, které můžete načíst pro aktuální dotaz.  Při nastavení `count` parametrů `offset` asemůžečíslozměnit.`TotalEstimatedAnswers` 

