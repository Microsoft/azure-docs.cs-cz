---
title: Použití pořadí zobrazíte výsledky hledání – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí hodnocení můžete zobrazit výsledky z rozhraní API webové vyhledávání Bingu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 4a46b7bbc99971ba2c142a816f7c41175a113cba
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167695"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Použití pořadí zobrazení výsledků rozhraní API webové vyhledávání Bingu  

Obsahuje každou odpověď vyhledávání [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpověď, která určuje, jak je třeba zobrazit výsledky hledání. Hodnocení odpovědí seskupí výsledky podle mainline obsahu a boční panel pro tradiční hledání stránky výsledků. Pokud nejsou zobrazení výsledků v tradiční hlavní linie a boční panel formátu, je nutné zadat hlavní linie viditelnost obsahu vyšší než postranního panelu obsahu.  

V rámci jednotlivých skupin (mainline nebo postranního panelu), [položky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) pole určuje pořadí, ve kterém obsah musí být uvedena v. Každá položka obsahuje následující dva způsoby, jak určit výsledek v rámci odpověď.  

-   `answerType` a `resultIndex` – `answerType` pole identifikuje odpovědí (například webové stránky nebo zpráv) a `resultIndex` identifikuje výsledků v rámci odpovědí (například zpravodajskému článku). Index je od nuly.  

-   `value` — `value` Pole obsahuje ID, které odpovídá ID odpověď nebo výsledků v rámci odpověď. Odpověď nebo výsledky obsahují ID, ale ne obojí.  

Pomocí ID je jednodušší použít, protože je potřeba jenom odpovídat ID hodnocení s ID odpověď nebo některou z jeho výsledky. Pokud objekt odpovědi obsahuje `id` pole, společně zobrazit výsledky všech odpovědí. Například pokud `News` objekt zahrnuje `id` pole, zobrazí všechny články společně. Pokud `News` objekt neobsahuje `id` pole, pak každý příspěvek obsahuje `id` pole a hodnocení odpovědi kombinuje vybrané články s výsledky z jiné odpovědi.  

Použití `answerType` a `resultIndex` je o něco složitější. Použijete `answerType` k identifikaci odpověď, která obsahuje výsledky k zobrazení. Potom použijte `resultIndex` do indexu prostřednictvím výsledků odpověď získat výsledek, který má zobrazit. ( `answerType` Hodnota je název pole v [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektu.) Pokud už má společně zobrazit výsledky všech odpovědí, odpověď na hodnocení položky neobsahuje `resultIndex` pole.  

## <a name="ranking-response-example"></a>Hodnocení příklad odpovědi

Následující příklad ukazuje, [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Protože nezahrnuje odpověď webové `id` pole, bude zobrazení všech webových stránek jednotlivě podle pořadí (každá webová stránka obsahuje `id` pole). A protože obsahují obrázků, videí a související hledání odpovědí `id` pole, by zobrazit výsledky podle pořadí společně tyto odpovědi.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    },  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Podle této odpovědi hodnocení, hlavní linie zobrazí tyto výsledky hledání:  

-   První výsledek webovou stránku
-   Všechny bitové kopie  
-   Druhý a třetí webová stránka výsledků  
-   Všechna videa  
-   Výsledky webová stránka 4, 5 a 6.  

A na bočním panelu zobrazí tyto výsledky hledání:  

-   Související hledání  


## <a name="next-steps"></a>Další postup

Informace o rozběhnutí unranked výsledků najdete v tématu [podporu odpovědi, které nejsou seřazené](./filter-answers.md#promoting-answers-that-are-not-ranked).

Informace o omezení počtu seřazený odpovědi v odpovědi najdete v tématu [omezením počtu odpovědi v odpovědi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Například C#, která používá řazení a zobrazte výsledky, naleznete v tématu [C# – tutoriál hodnocení](./csharp-ranking-tutorial.md).
