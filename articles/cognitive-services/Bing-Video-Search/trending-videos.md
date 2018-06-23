---
title: Vyhledávání na webu trendů videa | Microsoft Docs
description: Ukazuje, jak používat rozhraní API služby Bing Video Search vyhledávání na webu pro trendů videa.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 897A28A3-0980-484E-814F-FFE1D5C885E6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8db7fcf77042631260b4b165bd3d44053827f3ce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342376"
---
# <a name="get-trending-videos"></a>Získat trendů videa  

Potřebujete dnešní trendů videa, pošlete následující požadavek GET:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Následující trhy podporovat trendů videa.  
 
-   en-AU (angličtina, Austrálie)  
-   en-CA (angličtina, Kanada)  
-   en-GB (angličtina, Velká Británie)  
-   en-ID (angličtina, Indonésie)  
-   en-IE (angličtina, Irsku)  
-   en-IN (angličtina, Indie)  
-   en-NZ (angličtina, Nový Zéland)  
-   en parametr (angličtina, Filipíny)  
-   en-SG (angličtina, Singapur)  
-   en US (angličtina, USA)  
-   en TT (angličtina, Worldwide agregovat kódu)  
-   en-ZA (angličtina, Jihoafrická republika)  
-   zh-CN (čínština, Čína)

  
Následující příklad ukazuje odpověď obsahující trendů videa.  

```  
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
Odpověď obsahuje seznam videa podle kategorie a podkategorie. Například pokud seznam kategorií obsažené kategorii Hudba, videa a jeden z jeho podkategorie byl Top, můžete vytvořit kategorii horní Hudba videa v činnost koncového uživatele. Poté můžete použít `thumbnailUrl`, `displayText`, a `webSearchUrl` pole pro vytvoření prokliknutelný dlaždice v rámci každé kategorie (například horní Hudba videa). Když uživatel klikne na dlaždici, budou se potřebný k Bing na video prohlížeče kde přehrát video.

Odpověď obsahuje také hlavičku videa, které jsou nejčastěji používané trendů videa. Hlavička videa mohou pocházet z jednoho nebo více kategorií.  
  
