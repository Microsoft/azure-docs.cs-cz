---
title: Hledání populárních videí na webu pomocí rozhraní API pro vyhledávání videí Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí rozhraní API pro vyhledávání videí Bingu vyhledávat na webu populární videa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500619"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Získejte populární videa pomocí rozhraní API pro vyhledávání videí Bingu 

Rozhraní API pro vyhledávání videí Bing umožňuje najít dnešní populární videa z celého webu a v různých kategoriích. 

## <a name="get-request"></a>Požadavek GET

Chcete-li získat dnešní trendová videa z rozhraní API pro vyhledávání videí Bingu, odešlete následující žádost GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Podpora trhu

Následující trhy podporují populární videa.  
 
-   en-AU (anglicky, Austrálie)  
-   cs-CA (angličtina, Kanada)  
-   en-GB (anglicky, Velká Británie)  
-   en-ID (anglicky, indonésie)  
-   en-IE (angličtina, Irsko)  
-   en-IN (angličtina, Indie)  
-   en-NZ (angličtina, Nový Zéland)  
-   en-PH (angličtina, Filipíny)  
-   en-SG (Angličtina, Singapur)  
-   en-US (Angličtina, Spojené státy americké)  
-   en-WW (angličtina, celosvětový souhrnný kód)  
-   en-ZA (angličtina, Jihoafrická republika)  
-   zh-CN (čínština, Čína)

## <a name="example-json-response"></a>Příklad odpovědi JSON  

Následující příklad ukazuje odpověď rozhraní API, která obsahuje trendová videa, která jsou uvedena podle kategorie a podkategorie. Odpověď také obsahuje bannerová videa, která jsou nejoblíbenějšími populárními videi a mohou pocházet z jedné nebo více kategorií.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání přehledů videí](video-insights.md)