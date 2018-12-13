---
title: Procházení obrázků vrácené rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Procházet různé stránky imagí vrácené rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 69829066e543e5ab3badc4d1d3f77ff3c1d16ffd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259745"
---
# <a name="page-through-the-images-results"></a>Stránkovat výsledky imagí

Při volání rozhraní API pro vyhledávání obrázků Bingu vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Odhadované celkový počet dostupných výsledků získáte přístup k objektu odpovědi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) pole.  

Následující příklad ukazuje `totalEstimatedMatches` pole, které zahrnuje obrázky odpověď.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

Chcete-li stránkovat dostupných imagí, použijte [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametrů dotazu.  

`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které může vyžadovat v odpovědi je 150. Výchozí hodnota je 35. Skutečný počet doručení může být menší než požadovaný.

`offset` Parametr určuje počet výsledků, chcete-li přeskočit. `offset` Je založený na nule a musí být menší než (`totalEstimatedMatches` - `count`).  

Pokud chcete zobrazit 20 obrázky na jedné stránce nastavíte `count` na 20 a `offset` na hodnotu 0, chcete-li získat první stránka výsledků. Následuje příklad, který vyžaduje 20 imagí začínající na posunu 40.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnota se dá použít pro vaší implementace, je třeba určit pouze `offset` parametr dotazu.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Očekáváte, že pokud stránce 35 Image současně nastavíte `offset` parametr na hodnotu 0 na první žádosti o dotazu a potom zvýší `offset` podle 35 na každý další požadavek. Ale některé výsledky v následné odpovědi může být duplicitní předchozí odpovědi. První dvě bitové kopie v odpovědi například může být stejný jako poslední dvě bitové kopie z předchozí odpovědi.

Chcete-li odstranit duplicitní výsledky, použijte [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) pole `Images` objektu. `nextOffset` Pole zjistíte `offset` pro další požadavek. Například pokud chcete na stránku 30 imagí v čase, nastavte `count` 30 a `offset` na 0 v první požadavek. V další požadavek, byste měli nastavit `count` 30 a `offset` hodnotu pro předchozí odpověď `nextOffset`. Na stránce zpět, doporučujeme údržbu zásobníku předchozí posuny a automaticky otevíraného nejnovější.

> [!NOTE]
> Stránkování platí pouze pro vyhledávání obrázků (/ imagí/hledání) a není pro přehledy obrázků nebo populárních obrázků (/ imagí a sledování trendů).

> [!NOTE]
> `TotalEstimatedAnswers` Pole je odhad celkový počet výsledků hledání můžete načíst aktuálního dotazu.  Pokud nastavíte `count` a `offset` parametry, `TotalEstimatedAnswers` číslo může změnit. 
