---
title: Postup stránky pomocí výsledků hledání – rozhraní API pro vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak stránkovat výsledky hledání z rozhraní API pro vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: 9fc05ab42c75bac1f8e192dd4fe20bb142881479
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176908"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Postup stránky pomocí výsledků z rozhraní API pro vyhledávání Bingu

Když odešlete volání webu Bing web, Custom, image, News nebo Vyhledávání videí rozhraní API, Bing vrátí podmnožinu celkového počtu výsledků, které mohou být pro dotaz relevantní. K získání odhadovaného celkového počtu dostupných výsledků získáte přístup k poli `totalEstimatedMatches` objektu odpovědi. 

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

## <a name="paging-through-search-results"></a>Stránkování prostřednictvím výsledků hledání

K procházení dostupných výsledků použijte parametry dotazu `count` a `offset` při odesílání vaší žádosti.  

> [!NOTE]
>
> * Stránkování s rozhraními API videa, obrázků a novinek Bingu se vztahují jenom na Obecné hledání videa (`/video/search`), News (`/news/search`) a image (`/image/search`). Stránkování přes témata a kategorie v rámci trendů se nepodporuje.  
> * Pole `TotalEstimatedMatches` představuje odhad celkového počtu výsledků hledání pro aktuální dotaz. Když nastavíte parametry `count` a `offset`, může se tento odhad změnit.

| Parametr | Popis                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Určuje počet výsledků, které se mají vrátit v odpovědi. Všimněte si, že výchozí hodnota `count` a maximální počet výsledků, které se mohou vyžádat, se liší podle rozhraní API. Tyto hodnoty najdete v referenční dokumentaci v části [Další kroky](#next-steps). |
| `offset`  | Určuje počet výsledků, které se mají přeskočit. @No__t-0 je založený na nule a musí být menší než (`totalEstimatedMatches` @ no__t-2 @ no__t-3).                                           |

Pokud například chcete zobrazit 15 výsledků na stránce, nastavili jste `count` až 15 a `offset` na 0 pro získání první stránky výsledků. Pro každé následné volání rozhraní API se zvýší `offset` o 15. Následující příklad vyžádá 15 webových stránek počínaje posunem 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Pokud použijete výchozí hodnotu `count`, stačí zadat parametr dotazu `offset` v voláních rozhraní API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Rozhraní API Bingu pro vyhledávání na webu vrátí výsledky hledání, které mohou zahrnovat webové stránky, obrázky, videa a novinky. Při procházení výsledků hledání z rozhraní API Bingu pro vyhledávání na webu se jedná o [webové stránky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)jenom stránkování, a ne další typy odpovědí, jako jsou obrázky nebo novinky. Výsledky hledání v objektech `WebPage` můžou zahrnovat výsledky, které se zobrazují i v jiných typech odpovědí.
>
> Pokud použijete parametr dotazu `responseFilter` bez zadání hodnot filtru, nepoužívejte parametry `count` a `offset`. 

## <a name="next-steps"></a>Další kroky

* [Jaká jsou rozhraní API pro Vyhledávání na webu Bingu?](bing-api-comparison.md)
* [Referenční informace k rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Odkaz na rozhraní API pro vlastní vyhledávání Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Odkaz na rozhraní API Bingu pro vyhledávání zpráv v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Odkaz na rozhraní API Bingu pro vyhledávání videí v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Odkaz na rozhraní API Bingu pro vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)