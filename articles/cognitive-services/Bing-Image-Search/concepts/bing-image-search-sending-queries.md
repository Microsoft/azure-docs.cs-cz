---
title: Odesílání dotazů na rozhraní API Bingu pro vyhledávání obrázků | Dokumentace Microsoftu
description: Další informace o odesílání a přizpůsobení vyhledávací dotazy odeslané do rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: bf0db0b6d2aa54a853ba86b570ca05fba902dbc1
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049554"
---
# <a name="send-queries-to-the-bing-image-search-api"></a>Odesílání dotazů na rozhraní API Bingu pro vyhledávání obrázků

Rozhraní API pro vyhledávání obrázků Bingu poskytuje funkce podobné Bing.com/Images. Můžete ho odeslat dotaz do Bingu a získat seznam určit prioritu relevantních obrázků.

## <a name="use-and-suggest-search-terms"></a>Použít a navrhnout hledané termíny

Po zadání hledaného termínu kódování URL období před můžete nastavit [ **q** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametr dotazu. Pokud zadáte například *řízení dinghies*, nastavte `q` k `sailing+dinghies` nebo `sailing%20dinghies`.

Pokud vaše aplikace obsahuje vyhledávací pole, ve kterém se zadávají hledané výrazy, můžete použít [rozhraní API pro automatické návrhy Bingu](../../bing-autosuggest/get-suggested-search-terms.md) na zlepšení uživatelského rozhraní. Rozhraní API můžete zobrazit navrhovaný hledané termíny v reálném čase. Rozhraní API vrátí řetězce navrhovaných dotazů na základě částečné hledané termíny a Cognitive Services.

## <a name="pivot-the-query"></a>Kontingenční tabulky – dotaz

Pokud Bingu mohou například segmentovat původní dotaz vyhledávání, vrácený [image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objekt obsahuje `pivotSuggestions`. Návrhy kontingenční tabulky může být zobrazen jako volitelné hledané výrazy pro uživatele. Například, pokud byl původní dotaz *Microsoft Surface*, může Bing segmentovat dotaz do *Microsoft* a *Surface* a zadejte navrhovaný pivotů. Tyto návrhy může být zobrazen jako volitelné výrazy pro uživatele.

V následujícím příkladu se zobrazenými návrhy pivot pro *Microsoft Surface*:  

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

Pole `pivotSuggestions` obsahuje seznam segmentů (pivotů), na které se rozdělil původní dotaz. Pro každý pivot obsahuje odpověď seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj), které obsahují navrhované dotazy. `text` Pole obsahuje navrhované dotazu. `displayText` Pole obsahuje výraz, který nahrazuje pivotu v původní dotaz. Příkladem je verze datum povrchu.

Pokud řetězec dotazu pivot je, co se uživatel dívá, použijte `text` a `thumbnail` řetězce dotazu polí k zobrazení kontingenční tabulky. Vytvoření pomocí miniaturu a text po kliknutí `webSearchUrl` adresu URL nebo `searchLink` adresy URL. Použití `webSearchUrl` odešlete mu výsledky vyhledávání Bingu. Pokud zadáte vlastní stránka s výsledky, použijte `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Rozbalit dotaz

Pokud Bing dokáže rozšířením dotazu zúžit původní hledání, bude objekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obsahovat pole `queryExpansions`. Například, pokud se dotaz *Microsoft Surface*, rozšířené dotazy nesmusí být: 
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **centra**.

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

Pole `queryExpansions` obsahuje seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). `text` Pole obsahuje rozšířené dotazu. `displayText` Pole obsahuje výraz rozšíření. Pokud řetězec rozšířené dotazu je, co se uživatel dívá, použijte `text` a `thumbnail` polí k zobrazení řetězce dotazu rozbalený. Vytvoření pomocí miniaturu a text po kliknutí `webSearchUrl` adresu URL nebo `searchLink` adresy URL. Použití `webSearchUrl` odešlete mu výsledky vyhledávání Bingu. Pokud zadáte vlastní stránka s výsledky, použijte `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

Pokud jste dosud API pro vyhledávání obrázků Bingu před, zkuste [rychlý Start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, projděte si kurz pro vytvoření [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md).
