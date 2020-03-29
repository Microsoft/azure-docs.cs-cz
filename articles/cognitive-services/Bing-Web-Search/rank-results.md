---
title: Jak používat hodnocení k zobrazení výsledků hledání - Rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí hodnocení zobrazit výsledky hledání z rozhraní API pro vyhledávání na webu Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 677f6089f649aae720a6303a7e1512e3c7ebeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390124"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Jak pomocí hodnocení zobrazit výsledky rozhraní API pro vyhledávání na webu Bingu  

Každá odpověď hledání obsahuje odpověď [RankingResponse,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) která určuje, jak je nutné zobrazit výsledky hledání. Výsledky skupin odpovědí v hodnocení podle obsahu hlavní linie a obsahu postranního panelu pro tradiční stránku s výsledky vyhledávání. Pokud nezobrazíte výsledky v tradičním formátu hlavní čáry a postranního panelu, je nutné zadat obsah hlavní čáry vyšší viditelnost než obsah postranního panelu.  

V rámci každé skupiny (hlavní nebo postranní panel) pole [Items](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) identifikuje pořadí, ve které se musí obsah zobrazit. Každá položka poskytuje následující dva způsoby, jak identifikovat výsledek v rámci odpovědi.  

-   `answerType`a `resultIndex` – `answerType` Pole identifikuje odpověď (například webovou stránku nebo zprávy) a `resultIndex` identifikuje výsledek v rámci odpovědi (například zpravodajský článek). Index je založen na nule.  

-   `value`— `value` Pole obsahuje ID, které odpovídá ID odpovědi nebo výsledku v odpovědi. Odpověď nebo výsledky obsahují ID, ale ne obojí.  

Použití ID je jednodušší, protože stačí spárovat ID pořadí s ID odpovědi nebo jeden z jeho výsledků. Pokud objekt odpovědi `id` obsahuje pole, zobrazte všechny výsledky odpovědi společně. Pokud například `News` objekt obsahuje `id` pole, zobrazte všechny zpravodajské články společně. Pokud `News` objekt neobsahuje `id` pole, pak každý zpravodajský `id` článek obsahuje pole a odpověď pořadí kombinuje zpravodajské články s výsledky z jiných odpovědí.  

Použití `answerType` a `resultIndex` je trochu složitější. Slouží `answerType` k identifikaci odpovědi, která obsahuje výsledky k zobrazení. Potom slouží `resultIndex` k indexování výsledků odpovědi získat výsledek k zobrazení. (Hodnota `answerType` je název pole v objektu [SearchResponse.)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Pokud máte zobrazit všechny výsledky odpovědi společně, položka odpovědi pořadí neobsahuje `resultIndex` pole.  

## <a name="ranking-response-example"></a>Příklad odpovědi na pořadí

Následující ukazuje příklad [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Vzhledem k tomu, `id` že webová odpověď neobsahuje pole, zobrazíte všechny webové stránky `id` jednotlivě na základě pořadí (každá webová stránka obsahuje pole). A protože obrázky, videa a související `id` odpovědi na vyhledávání obsahují pole, zobrazíte výsledky každé z těchto odpovědí společně na základě pořadí.

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
    }],  
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

Na základě této odpovědi hodnocení by hlavní linie zobrazovala následující výsledky vyhledávání:  

-   První výsledek webové stránky
-   Všechny obrázky  
-   Výsledky druhé a třetí webové stránky  
-   Všechna videa  
-   Výsledky 4., 5.  

A postranní panel by zobrazoval následující výsledky hledání:  

-   Všechna související vyhledávání  


## <a name="next-steps"></a>Další kroky

Informace o propagaci nehodnocených výsledků naleznete v [tématu Propagace odpovědí, které nejsou hodnoceny](./filter-answers.md#promoting-answers-that-are-not-ranked).

Informace o omezení počtu hodnocených odpovědí v odpovědi naleznete v [tématu Omezení počtu odpovědí v odpovědi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Příklad jazyka C#, který k zobrazení výsledků používá hodnocení, naleznete v [tématu C# ranking tutorial](./csharp-ranking-tutorial.md).
