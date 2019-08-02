---
title: Jak stránkovat prostřednictvím dostupných videí – Vvyhledávání videí Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak stránkovat prostřednictvím všech videí vrácených rozhraní API Bingu pro vyhledávání videí.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500710"
---
# <a name="paging-through-video-search-results"></a>Stránkování pomocí výsledků hledání videí

Rozhraní API Bingu pro vyhledávání videí vrátí podmnožinu všech výsledků hledání, které pro váš dotaz najde. Pomocí stránkování prostřednictvím těchto výsledků s následným voláním rozhraní API je můžete získat a zobrazit v aplikaci.

> [!NOTE]
> Stránkování se vztahuje jenom na video hledání (/Videos/Search), ne na video Insights (/Videos/Details) nebo vývojové videa (/Videos/Trending).

## <a name="total-estimated-matches"></a>Odhadované shody celkem

K získání odhadovaného počtu nalezených výsledků hledání použijte pole [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) v odpovědi JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Stránkování prostřednictvím videí

Na stránku z dostupných videí použijte parametry dotazu [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) a [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) při odesílání vaší žádosti.  
  

|Parametr  |Popis  |
|---------|---------|
|`count`     | Určuje počet výsledků, které se mají vrátit v odpovědi. Maximální počet výsledků, které mohou být požadovány v odpovědi, je 100. Výchozí hodnota je 10. Skutečný počet dodaných hodnot může být menší, než je požadováno.        |
|`offset`     | Určuje počet výsledků, které se mají přeskočit. Hodnota `offset` je počítána od nuly a měla by být menší`totalEstimatedMatches`než ( - `count`).          |

Například pokud chcete zobrazit 20 článků na stránku, měli byste nastavit `count` na hodnotu 20 a `offset` na 0 a získat tak první stránku výsledků. Pro každou následnou stránku byste narůsti `offset` o 20 (například 20, 40).  
  
Následující příklad vyžádá 20 videí, počínaje posunem 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Použijete-li výchozí hodnotu pro [počet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count), stačí zadat `offset` parametr dotazu, jak je uvedeno v následujícím příkladu.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud po 35 videích provedete na stránce, nastavíte `offset` parametr dotazu na hodnotu 0 u prvního požadavku a potom na každou následnou žádost zvýšíte `offset` o 35. Některé výsledky v další reakci ale můžou obsahovat duplicitní výsledky videa z předchozí odpovědi. Například první dvě videa v odpovědi mohou být stejná jako poslední dvě videa z předchozí odpovědi.

Chcete-li odstranit duplicitní výsledky, [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) použijte pole `Videos` nextOffset objektu.

Například pokud chcete, aby stránka 30 videí byla v průběhu času, v první žádosti můžete `count` nastavit na hodnotu `offset` 30 a na 0. V dalším požadavku byste nastavili `offset` parametr `nextOffset` dotazu na hodnotu.

> [!NOTE]
> Toto `TotalEstimatedAnswers` pole představuje odhad celkového počtu výsledků hledání, které můžete načíst pro aktuální dotaz.  Při nastavení `count` parametrů `offset` asemůžečíslozměnit.`TotalEstimatedAnswers` 
  
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat video přehledy](video-insights.md)
