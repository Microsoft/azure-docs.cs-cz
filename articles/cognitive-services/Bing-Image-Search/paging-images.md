---
title: Tom, jak procházet dostupné Image | Microsoft Docs
description: Ukazuje, jak na stránku prostřednictvím všechny bitové kopie, které můžou vrátit Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a74ee817e84be5bb563c5fdaf25afc1dc14732e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342366"
---
# <a name="paging-results"></a>Výsledky stránkování

Při volání rozhraní API pro vyhledávání bitové kopie Bingu vrátí seznam výsledků. V seznamu je podmnožinou celkový počet výsledků, které jsou relevantní pro dotaz. Odhadovaný celkový počet dostupných výsledků získáte přístup k objektu odpovědí [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) pole.  
  
Následující příklad ukazuje `totalEstimatedMatches` pole, který obsahuje odpověď bitové kopie.  
  
```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  
  
Na stránku prostřednictvím dostupné Image, pomocí [počet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) a [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametrů dotazu.  
  
`count` Parametr určuje počet výsledků vrátit v odpovědi. Maximální počet výsledků, které můžete požádat v odpovědi je 150. Výchozí hodnota je 35. Skutečný počet doručit může být nižší než požadovaný.

`offset` Parametr určuje počet výsledků tak, aby přeskočil. `offset` Je počítáno od nuly a měl by být menší než (`totalEstimatedMatches` - `count`).  
  
Pokud chcete zobrazit obrázky 20 na každé stránce, nastavíte `count` až 20 číslic a `offset` na hodnotu 0, chcete-li získat první stránky s výsledky. Následuje příklad, který požaduje 20 Image začínající na posunu 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Pokud výchozí `count` hodnotu funguje týkající se vaší implementace, budete muset zadat `offset` parametr dotazu.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Můžou očekávat, že pokud stránce 35 Image současně nastavíte `offset` parametr na hodnotu 0 na první požadavek dotazu a poté zvýšit `offset` podle 35 na každý další požadavek. Ale některé výsledky v odpovědi na následné může být duplicitní předchozí odpovědi. První dvě bitové kopie v odpovědi například může být stejný jako poslední dvě bitové kopie z předchozí odpovědi.

Chcete-li odstranit duplicitní výsledky, použijte [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) pole z `Images` objektu. `nextOffset` Pole zjistíte `offset` chcete použít pro další požadavek. Například pokud chcete na stránku 30 bitové kopie v čase, nastavíte `count` na 30 a `offset` na 0 v první požadavek. V další požadavek, byste měli nastavit `count` na 30 a `offset` na hodnotu předchozí odpovědi `nextOffset`. Na stránce zpátky, doporučujeme zachování zásobníku předchozí odsazení a odebrání nejnovější.

> [!NOTE]
> Stránkování platí pouze pro vyhledávání bitovou kopii (nebo bitové kopie nebo hledání) a nechcete Statistika bitovou kopii nebo trendů obrázků (nebo bitové kopie nebo trendů).