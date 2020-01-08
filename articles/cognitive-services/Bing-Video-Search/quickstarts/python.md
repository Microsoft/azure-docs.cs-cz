---
title: 'Rychlý Start: hledání videí pomocí REST API a Pythonu – Vvyhledávání videí Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání videí do Vvyhledávání videí Bingu REST API pomocí Pythonu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: fbf20c2d54506b0f314697d6df34f9a430e7c016
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382680"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-python"></a>Rychlý Start: hledání videí pomocí Vvyhledávání videí Bingu REST API a Pythonu

Pomocí tohoto rychlého startu můžete provést vaše první volání na rozhraní API Bingu pro vyhledávání videí a zobrazit výsledky hledání z odpovědi JSON. Tato jednoduchá aplikace v Pythonu pošle dotaz na hledání videa HTTP do rozhraní API a zobrazí odpověď. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této ukázky je dostupný na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingVideoSearchv7.py) s dalším ošetřením chyb a poznámkami ke kódu.

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Požadavky

* Python [2. x nebo 3. x](https://python.org)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor Pythonu a naimportujte následující knihovny:

    ```python
    import requests
    from IPython.display import HTML
    ```
2.  Vytvořte proměnné pro klíč předplatného, koncový bod hledání a hledaný termín. `search_url` může být globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.
    
    ```python
    subscription_key = None
    assert subscription_key
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
    search_term = "kittens"
    ```

3. Přidejte klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key` vytvořením nového slovníku, který přiřadí řetězec hlavičky k vašemu klíči.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="send-your-request"></a>Odeslat žádost

1. Přidejte parametry do žádosti vytvořením slovníku s názvem `params`. Přidejte hledaný výraz do parametru `q`, počet videí 5, `free` pro ceny vrácených videí a `short` pro délku videa.

    ```python
    params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
    ```

2. Použijte knihovnu `requests` v Pythonu k volání rozhraní API Bingu pro vyhledávání videí. Pomocí slovníku `headers` a `params` předejte klíč rozhraní API a parametry vyhledávání.
    
    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

3. Chcete-li zobrazit jedno ze vrácených videí, Získejte výsledek hledání z objektu `search_results`. Do `IFrame`vložte Vlastnost Result `embedHtml`.  
    
    ```python
    HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
    ```


## <a name="json-response"></a>Odpověď JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s jednou stránkou](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Co je rozhraní API Bingu pro vyhledávání videí?](../overview.md)
