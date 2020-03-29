---
title: Jak procházet výsledky hledání – hledání ve službě Bing
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak procházet výsledky hledání z vyhledávacích api Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481734"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Jak procházet výsledky z vyhledávacích api Bingu

Když odešlete volání na web Bing, vlastní, obrázek, novinky nebo video hledání API, Bing vrátí podmnožinu celkového počtu výsledků, které mohou být relevantní pro dotaz. Chcete-li získat odhadovaný celkový počet dostupných výsledků, přezouváte do `totalEstimatedMatches` pole objektu odpovědi. 

Například: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Stránkování ve výsledcích hledání

Chcete-li procházet dostupné `count` výsledky, použijte parametry a `offset` parametry dotazu při odesílání požadavku.  

> [!NOTE]
>
> * Stránkování pomocí videa, obrázků a diskusních sam`/video/search`pro videa bingu se vztahuje pouze na obecné vyhledávání videa ( ), zprávy (`/news/search`) a obrázku (`/image/search`). Stránkování prostřednictvím trendových témat a kategorií není podporováno.  
> * Toto `TotalEstimatedMatches` pole je odhadem celkového počtu výsledků hledání pro aktuální dotaz. Když nastavíte parametry `count` a, `offset` tento odhad se může změnit.

| Parametr | Popis                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Určuje počet výsledků, které mají být vráceny v odpovědi. Všimněte si, `count`že výchozí hodnota aplikace a maximální počet výsledků, které můžete požadovat, se liší podle rozhraní API. Tyto hodnoty naleznete v referenční dokumentaci v části [Další kroky](#next-steps). |
| `offset`  | Určuje počet výsledků, které mají být přeskočte. Je `offset` založen na nule a`totalEstimatedMatches` - `count`měl by být menší než ( ).                                           |

Jako příklad, pokud chcete zobrazit 15 výsledků na `count` stránku, `offset` byste nastavili na 15 a 0 získat první stránku výsledků. Pro každé následné volání rozhraní API `offset` by se zvýší o 15. Následující příklad požaduje 15 webových stránek začínajících posunem 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Pokud použijete `count` výchozí hodnotu, stačí `offset` zadat parametr dotazu ve volání rozhraní API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Při použití souborů API pro obraz Bing `nextOffset` a video můžete tuto hodnotu použít, abyste se vyhnuli duplicitním výsledkům hledání. Získejte hodnotu `Images` `Videos` z objektů nebo odpovědi a použijte `offset` ji ve svých požadavcích s parametrem.  

> [!NOTE]
> Rozhraní API pro vyhledávání na webu Bingu vrací výsledky hledání, které mohou zahrnovat webové stránky, obrázky, videa a zprávy. Při procházení výsledků hledání z rozhraní API pro vyhledávání na webu Bingu stránkujete pouze [webové stránky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage), nikoli jiné typy odpovědí, například obrázky nebo zprávy. Výsledky hledání `WebPage` v objektech mohou zahrnovat výsledky, které se zobrazují i v jiných typech odpovědí.
>
> Pokud použijete `responseFilter` parametr dotazu bez zadání hodnot filtru, `count` `offset` nepoužívejte parametry a. 

## <a name="next-steps"></a>Další kroky

* [Co jsou webová vyhledávací api Bingu?](bing-api-comparison.md)
* [Referenční informace k rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Odkaz na rozhraní API pro vlastní vyhledávání Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Odkaz na rozhraní API pro vyhledávání zpráv bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Odkaz na rozhraní API pro vyhledávání videí bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Odkaz na rozhraní API pro vyhledávání obrázků Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
