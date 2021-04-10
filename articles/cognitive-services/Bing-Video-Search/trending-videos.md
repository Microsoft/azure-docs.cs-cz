---
title: Hledání v trendech na webu pro vývoj videí pomocí rozhraní API Bingu pro vyhledávání videí
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API Bingu pro vyhledávání videí k vyhledávání trendů na webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: cfeebcda201df592f6c396dcc780208a36d1e989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353795"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Získejte vývoj videí pomocí rozhraní API Bingu pro vyhledávání videí 

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Bingu pro vyhledávání videí vám umožní najít dnešní vývojové videa z celého webu a v různých kategoriích. 

## <a name="get-request"></a>Požadavek GET

Pokud si chcete z rozhraní API Bingu pro vyhledávání videí dostat dnešní vývojové videa, pošlete následující požadavek GET:  
  
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

Následující trhy podporují vývojové videa.  
 
-   EN-AU (angličtina, Austrálie)  
-   en-CA (angličtina, Kanada)  
-   en-GB (angličtina, Velká Británie)  
-   EN-ID (angličtina, Indonésie)  
-   EN-IE (angličtina, Irsko)  
-   en-IN (angličtina, Indie)  
-   EN-NZ (angličtina, Nový Zéland)  
-   EN-PH (angličtina, Filipínská republika)  
-   EN-SG (angličtina, Singapur)  
-   EN-US (angličtina, USA)  
-   EN-WW (v angličtině, celosvětově agregovaný kód)  
-   EN-ZA (angličtina, Jihoafrická Amerika)  
-   zh-CN (čínština, Čína)

## <a name="example-json-response"></a>Příklad odpovědi JSON  

Následující příklad ukazuje odpověď rozhraní API obsahující vývojová videa, která jsou uvedena v kategorii a podkategorií. Odpověď také obsahuje videa s bannery, která jsou nejoblíbenějším trendem videí a která může pocházet z jedné nebo více kategorií.  

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