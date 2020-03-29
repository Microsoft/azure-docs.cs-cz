---
title: Přizpůsobení a navržení dotazů pro vyhledávání obrázků – rozhraní API pro vyhledávání obrázků bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o přizpůsobení vyhledávacích dotazů, které odesíláte do rozhraní API pro vyhledávání obrázků Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542746"
---
# <a name="customize-and-suggest-image-search-queries"></a>Přizpůsobení a navržení dotazů na vyhledávání obrázků

V tomto článku se dozvíte, jak přizpůsobit dotazy a navrhnout hledané termíny pro odeslání do rozhraní API pro vyhledávání obrázků Bingu.

## <a name="suggest-search-terms"></a>Navrhnout hledané termíny

Pokud má vaše aplikace vyhledávací pole, ve kterém se zadávají hledané výrazy, můžete k vylepšení prostředí použít [rozhraní API automatického návrhu Bingu.](../../bing-autosuggest/get-suggested-search-terms.md) Rozhraní API může zobrazovat navrhované hledané výrazy v reálném čase. Rozhraní API vrátí navrhované řetězce dotazů na základě částečných hledaných výrazů a služeb Cognitive Services.

## <a name="pivot-the-query"></a>Kontingenční dotaz

Pokud bing může segmentovat původní vyhledávací dotaz, objekt [vrácené obrázky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obsahuje `pivotSuggestions`. Návrhy kontingenčních hodnot lze uživateli zobrazit jako volitelné hledané výrazy. Například pokud původní dotaz byl *Microsoft Surface*, Bing může segmentovat dotaz do *Microsoft* a *Surface* a poskytnout navrhované pivots pro každý. Tyto návrhy lze uživateli zobrazit jako volitelné termíny dotazů.

Následující příklad ukazuje návrhy kontingenčních pokynů pro *microsoft surface*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Pole `pivotSuggestions` obsahuje seznam segmentů (pivotů), na které se rozdělil původní dotaz. Pro každý pivot obsahuje odpověď seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj), které obsahují navrhované dotazy. Pole `text` obsahuje navrhovaný dotaz. Pole `displayText` obsahuje termín, který nahrazuje pivot v původním dotazu. Příkladem je datum vydání povrchu.

Pokud je řetězec kontingenčního dotazu to, co uživatel hledá, zobrazte řetězce kontingenčního dotazu pomocí polí `text` a. `thumbnail` Pomocí adresy URL nebo adresy `webSearchUrl` URL lze `searchLink` miniaturu a text nastavit jako na položku. Slouží `webSearchUrl` k odeslání uživatele do výsledků hledání Bing. Pokud zadáte vlastní stránku `searchLink`s výsledky, použijte .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Rozbalení dotazu

Pokud Bing dokáže rozšířením dotazu zúžit původní hledání, bude objekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obsahovat pole `queryExpansions`. Pokud byl například dotaz *Microsoft Surface*, mohou být rozšířené dotazy:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

Následující příklad ukazuje rozšířené dotazy pro dotaz *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Pole `queryExpansions` obsahuje seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). Pole `text` obsahuje rozbalený dotaz. Pole `displayText` obsahuje termín rozšíření. Pokud je rozšířený řetězec dotazu to, co `text` `thumbnail` uživatel hledá, použijte pole a k zobrazení rozšířených řetězců dotazu. Pomocí adresy URL nebo adresy `webSearchUrl` URL lze `searchLink` miniaturu a text nastavit jako na položku. Slouží `webSearchUrl` k odeslání uživatele do výsledků hledání Bing. Pokud zadáte vlastní stránku `searchLink`s výsledky, použijte .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky

Pokud jste rozhraní API pro vyhledávání obrázků bingu ještě nevyzkoušeli, zkuste [rychlý start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, zkuste v kurzu vytvořit [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md).
