---
title: Hledat na webu pro populárních videí pomocí API pro vyhledávání videí Bingu
titlesuffix: Azure Cognitive Services
description: Další informace o použití API pro vyhledávání videí Bingu pro vyhledávání na webu populárních videí.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2aa3c0cd9112f2de9ccda818e74b0b2c377133bf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869360"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Získat populární videa s API pro vyhledávání videí Bingu 

API pro vyhledávání videí Bingu umožňuje najít dnešní populárních videí z webu i v různých kategoriích. 

## <a name="get-request"></a>Žádost o získání

Chcete-li získat dnešní populárních videí z API Bingu pro vyhledávání videí, odešlete následující požadavek GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Podpora na trhu

Následujících zemích podporují populárních videí.  
 
-   cs Austrálie (angličtina, Austrálie)  
-   cs CA (angličtina, Kanada)  
-   en-GB (angličtina, Velká Británie)  
-   cs ID (angličtina, Indonésie)  
-   cs IE (angličtina, Irsko)  
-   cs Indie (angličtina, Indie)  
-   cs NZ (angličtina, Nový Zéland)  
-   cs PH (angličtina, Filipíny)  
-   cs SG (angličtina, Singapur)  
-   en US (angličtina, USA)  
-   cs TT (angličtina, celosvětové agregovat kódu)  
-   cs ZA (angličtina, Jižní Afrika)  
-   zh-CN (čínština, Čína)

## <a name="example-json-response"></a>Příklad JSON odpovědi  

Následující příklad ukazuje odpověď rozhraní API, která obsahuje populárních videí, které jsou uvedené podle kategorie a podkategorie. Odpověď obsahuje také nápis videa, které jsou nejoblíbenější populárních videí a můžou pocházet z jedné nebo více kategorií.  

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Získejte nové poznatky z videí](video-insights.md)