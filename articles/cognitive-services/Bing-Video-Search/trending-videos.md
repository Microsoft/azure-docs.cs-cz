---
title: Hledat na webu pro populárních videí – Video Bingu pro vyhledávání
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak použít API pro vyhledávání videí Bingu pro vyhledávání na webu populárních videí.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8a6ccc9ea8cf9468d7638360c9db8131bc6dc5be
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222036"
---
# <a name="get-trending-videos"></a>Získání videí, která jsou v kurzu  

Chcete-li získat dnešní populárních videí, poslat následující požadavek GET:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

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

  
Následující příklad ukazuje odpověď obsahující populárních videí.  

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
Odpověď obsahuje seznam videí podle kategorie a podkategorie. Například pokud byl jeden ze své podkategorie horní seznam kategorií obsažených kategorie Hudba, videa, můžete vytvořit kategorii horní Hudba videa ve vašem prostředí uživatele. Můžete pak použít `thumbnailUrl`, `displayText`, a `webSearchUrl` pole k vytvoření kliknout, čímž dlaždice podle jednotlivých kategorií (například horní Hudba videa). Po kliknutí na dlaždici, přejdete do prohlížeče video Bingu kde je video přehrát.

Odpověď obsahuje také nápis videa, které jsou nejoblíbenější populárních videí. Banner videa, která mohou pocházet z jedné nebo více kategorií.  
  
