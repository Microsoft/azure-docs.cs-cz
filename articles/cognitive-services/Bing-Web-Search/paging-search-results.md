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
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73481734"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Postup stránky pomocí výsledků z rozhraní API pro vyhledávání Bingu

Když odešlete volání webu Bing web, Custom, image, News nebo Vyhledávání videí rozhraní API, Bing vrátí podmnožinu celkového počtu výsledků, které mohou být pro dotaz relevantní. K získání odhadovaného celkového počtu dostupných výsledků získáte přístup k `totalEstimatedMatches` poli objektu odpovědi. 

Příklad: 

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

K procházení dostupných výsledků při posílání žádosti použijte `count` parametry `offset` dotazu a.  

> [!NOTE]
>
> * Stránkování s rozhraními API videa, obrázků a zpráv Bingu se vztahují jenom na obecná`/video/search`hledání videa ()`/news/search`, News ()`/image/search`a image (). Stránkování přes témata a kategorie v rámci trendů se nepodporuje.  
> * Toto `TotalEstimatedMatches` pole představuje odhad celkového počtu výsledků hledání pro aktuální dotaz. Při nastavení parametrů `count` a `offset` se tento odhad může změnit.

| Parametr | Popis                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Určuje počet výsledků, které se mají vrátit v odpovědi. Všimněte si, že výchozí hodnota `count`a maximální počet výsledků, které se mohou vyžádat, se liší podle rozhraní API. Tyto hodnoty najdete v referenční dokumentaci v části [Další kroky](#next-steps). |
| `offset`  | Určuje počet výsledků, které se mají přeskočit. Hodnota `offset` je počítána od nuly a měla by být menší`totalEstimatedMatches` - `count`než ().                                           |

Pokud například chcete zobrazit 15 výsledků na stránce, měli byste nastavit `count` na hodnotu 15 a `offset` na 0 a získat tak první stránku výsledků. Pro každé následné volání rozhraní API se zvýší `offset` o 15. Následující příklad vyžádá 15 webových stránek počínaje posunem 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Pokud použijete výchozí `count` hodnotu, stačí zadat parametr `offset` dotazu v volání rozhraní API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Pokud používáte rozhraní API pro Image Bingu a videa, můžete `nextOffset` hodnotu použít k zamezení duplicit výsledků hledání. Získat hodnotu z objektů `Images` nebo `Videos` odpovědí a použít ji ve svých žádostech s `offset` parametrem.  

> [!NOTE]
> Rozhraní API Bingu pro vyhledávání na webu vrátí výsledky hledání, které mohou zahrnovat webové stránky, obrázky, videa a novinky. Při procházení výsledků hledání z rozhraní API Bingu pro vyhledávání na webu se jedná o [webové stránky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)jenom stránkování, a ne další typy odpovědí, jako jsou obrázky nebo novinky. Výsledky hledání v `WebPage` objektech mohou zahrnovat výsledky, které se zobrazují i v jiných typech odpovědí.
>
> Použijete-li `responseFilter` parametr dotazu bez zadání hodnot filtru, nepoužívejte parametry `count` a. `offset` 

## <a name="next-steps"></a>Další kroky

* [Jaká jsou rozhraní API pro Vyhledávání na webu Bingu?](bing-api-comparison.md)
* [Referenční informace k rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Odkaz na rozhraní API pro vlastní vyhledávání Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Odkaz na rozhraní API Bingu pro vyhledávání zpráv v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Odkaz na rozhraní API Bingu pro vyhledávání videí v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Odkaz na rozhraní API Bingu pro vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
