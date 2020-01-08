---
title: 'Rychlý Start: hledání videí pomocí REST API a C# -Vvyhledávání videí Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání videí Vvyhledávání videí Bingu REST API pomocí C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 28c900adadf7d942c9e331e7b77a369db64acf55
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382697"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Rychlý Start: hledání videí pomocí Vvyhledávání videí Bingu REST API aC#

Pomocí tohoto rychlého startu můžete provést vaše první volání na rozhraní API Bingu pro vyhledávání videí a zobrazit výsledky hledání z odpovědi JSON. Tato jednoduchá C# aplikace pošle do rozhraní API dotaz pro vyhledávání videí http a zobrazí odpověď. Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) s dalšími zpracováním chyb, funkcemi a poznámkami ke kódu.

## <a name="prerequisites"></a>Požadavky
* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly v aplikaci Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Přidejte proměnné pro svůj klíč předplatného, koncový bod a hledaný termín. `uriBase` může být globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```csharp
    const string accessKey = "enter your key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    const string searchTerm = "kittens";
    ```

### <a name="create-a-struct-to-format-the-bing-video-search-api-response"></a>Vytvoření struktury pro naformátování odezvy rozhraní API Bingu pro vyhledávání videí

1. Definujte strukturu `SearchResult` tak, aby obsahovala výsledky hledání obrázků, a informace v hlavičce JSON.

    ```csharp
    struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }
    ```

## <a name="create-and-handle-a-video-search-request"></a>Vytvoření a zpracování žádosti o vyhledávání videí

Vytvořte metodu s názvem `BingVideoSearch`, která provede volání rozhraní API a nastaví typ vrácení na strukturu `SearchResult` vytvořenou dříve. V metodě proveďte následující kroky:

1. Sestavte URI pro žádost o vyhledávání. Všimněte si, že hledaný výraz toSearch musí být před připojením k řetězci zformátován.

    ```csharp
    
    static SearchResult BingVideoSearch(string toSearch){
    
        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Webovou žádost můžete provést tak, že přidáte svůj klíč do hlavičky `Ocp-Acpim-Subscription-Key` a pomocí objektu `HttpWebResponse` uložíte odpověď rozhraní API. Pak použijte `StreamReader` k získání řetězce JSON.

    ```csharp
    //...
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    //...
    ```

## <a name="process-the-result"></a>Zpracovat výsledek

1. Vytvořte objekt výsledků hledání a extrahujte hlavičky HTTP Bingu. Pak vraťte objekt `searchResult`. 

    ```csharp
    var searchResult = new SearchResult();
    searchResult.jsonResult = json;
    searchResult.relevantHeaders = new Dictionary<String, String>();

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

2. Pak můžete odpověď vytisknout.

    ```csharp
    Console.WriteLine(result.jsonResult);
    ```

## <a name="example-json-response"></a>Příklad odpovědi JSON 

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
