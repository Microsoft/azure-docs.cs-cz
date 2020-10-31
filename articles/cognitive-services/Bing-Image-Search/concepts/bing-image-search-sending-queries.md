---
title: Přizpůsobení a návrh dotazů na hledání obrázků – rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Seznamte se s přizpůsobením vyhledávacích dotazů odesílaných do rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: f697449fffe6c93d8e5082b210678d3f51c0c736
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084406"
---
# <a name="customize-and-suggest-image-search-queries"></a>Přizpůsobení a návrh dotazů na hledání obrázků

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto článku se dozvíte, jak přizpůsobit dotazy a navrhovat hledané výrazy k odeslání do rozhraní API Bingu pro vyhledávání obrázků.

## <a name="suggest-search-terms"></a>Navrhnout hledané výrazy

Pokud vaše aplikace obsahuje vyhledávací pole, ve kterém jsou zadány hledané výrazy, můžete použít [rozhraní API pro automatické návrhy Bingu](../../bing-autosuggest/get-suggested-search-terms.md) ke zlepšení prostředí. Rozhraní API může zobrazit navrhované hledané výrazy v reálném čase. Rozhraní API vrátí navrhované řetězce dotazů na základě podmínek částečného vyhledávání a Cognitive Services.

## <a name="pivot-the-query"></a>Kontingenční dotaz

Pokud může Bing rozdělit původní vyhledávací dotaz, obsahuje objekt vrácených [obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) `pivotSuggestions` . Návrhy pivotu se dají uživateli zobrazit jako volitelné hledané výrazy. Například pokud byl původní dotaz na *Microsoft Surface* , Bing může segmentovat dotaz do *Microsoftu* a *Surface* a pro každý z nich navrhovat navržené pivoty. Tyto návrhy se dají uživateli zobrazit jako volitelné výrazy pro dotazování.

Následující příklad ukazuje návrhy pivotu pro *Microsoft Surface* :  

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

Pole `pivotSuggestions` obsahuje seznam segmentů (pivotů), na které se rozdělil původní dotaz. Pro každý pivot obsahuje odpověď seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj), které obsahují navrhované dotazy. `text`Pole obsahuje navrhovaný dotaz. `displayText`Pole obsahuje termín, který nahradí Pivot v původním dotazu. Příkladem je datum vydání povrchu.

Pokud je řetězec kontingenčního dotazu, který uživatel hledá, použijte `text` `thumbnail` pole a k zobrazení řetězců kontingenčního dotazu. Nastavte miniaturu a text tak, aby se na něj mohla kliknout pomocí `webSearchUrl` adresy URL nebo `searchLink` adresy URL. Slouží `webSearchUrl` k odeslání uživatele do výsledků vyhledávání Bingu. Pokud zadáte vlastní stránku výsledků, použijte `searchLink` .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Rozbalí dotaz

Pokud Bing dokáže rozšířením dotazu zúžit původní hledání, bude objekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obsahovat pole `queryExpansions`. Pokud byl například dotaz na *Microsoft Surface* , rozšířené dotazy mohou být:
- Microsoft surfing **pro 3** .
- Microsoft Surface **RT** .
- Microsoft Surface **Phone** .
- Microsoft Surface **hub** .

Následující příklad ukazuje rozšířené dotazy pro dotaz *Microsoft Surface* .

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

Pole `queryExpansions` obsahuje seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). `text`Pole obsahuje rozbalený dotaz. `displayText`Pole obsahuje výraz rozšíření. Pokud je řetězec rozšířeného dotazu hledaný uživatelem, použijte `text` `thumbnail` pole a k zobrazení rozšířených řetězců dotazu. Nastavte miniaturu a text tak, aby se na něj mohla kliknout pomocí `webSearchUrl` adresy URL nebo `searchLink` adresy URL. Slouží `webSearchUrl` k odeslání uživatele do výsledků vyhledávání Bingu. Pokud zadáte vlastní stránku výsledků, použijte `searchLink` .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky

Pokud jste rozhraní API Bingu pro vyhledávání obrázků ještě nezkoušeli, vyzkoušejte si [rychlý Start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, zkuste vytvořit [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md)pomocí kurzu.
