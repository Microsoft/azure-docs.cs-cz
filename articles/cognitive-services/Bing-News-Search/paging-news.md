---
title: Postup stránky Vyhledávání zpráv Bingu výsledky
titleSuffix: Azure Cognitive Services
description: Naučte se, jak stránkovat prostřednictvím článků s novinkami, které rozhraní API Bingu pro vyhledávání zpráv vrací.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423746"
---
# <a name="how-to-page-through-news-search-results"></a>Jak stránkovat prostřednictvím zpráv výsledků hledání

Když zavoláte rozhraní Vyhledávání zpráv API, Bing vrátí seznam výsledků, které jsou pro váš dotaz relevantní. K získání odhadovaného celkového počtu dostupných výsledků získáte přístup k poli [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) objektu odpovědi.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, které obsahuje odpověď na zprávy.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
K stránkám podle dostupných článků použijte parametry dotazu [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) a [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) .  
 

|Parametr  |Popis  |
|---------|---------|
|`count`     | Určuje počet výsledků, které se mají vrátit v odpovědi. Maximální počet výsledků, které mohou být požadovány v odpovědi, je 100. Výchozí hodnota je 10. Skutečný počet dodaných hodnot může být menší, než je požadováno.        |
|`offset`     | Určuje počet výsledků, které se mají přeskočit. Hodnota `offset` je počítána od nuly a měla by být menší`totalEstimatedMatches`než ( - `count`).          |

Například pokud chcete zobrazit 20 článků na stránku, měli byste nastavit `count` na hodnotu 20 a `offset` na 0 a získat tak první stránku výsledků. Pro každou následnou stránku byste narůsti `offset` o 20 (například 20, 40).  
  
Následující příklad vyžádá 20 novinek počínaje posunem 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Pokud výchozí `count` hodnota pro vaši implementaci funguje, zadejte `offset` pouze parametr dotazu, jak je znázorněno v následujícím příkladu:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Stránkování se vztahuje pouze na hledání zpráv (/News/Search) a ne na témata týkající se trendů (/News/trendingtopics) nebo kategorií zpráv (/News).

> [!NOTE]
> Toto `TotalEstimatedAnswers` pole představuje odhad celkového počtu výsledků hledání, které můžete načíst pro aktuální dotaz.  Při nastavení `count` parametrů `offset` asemůžečíslozměnit.`TotalEstimatedAnswers` 
