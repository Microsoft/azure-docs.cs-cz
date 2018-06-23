---
title: Použití řazení pro zobrazení webové odpovědi | Microsoft Docs
description: Ukazuje, jak používat řazení zobrazíte odpovědi, které vrací rozhraní API služby Bing webové Search.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 750146f3bb28b94594a71733b68f092880360c5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343427"
---
# <a name="using-ranking-to-display-results"></a>Chcete-li zobrazit výsledky pomocí řazení  

Každá odpověď vyhledávání zahrnuje [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpovědí, která určuje, jak je třeba zobrazit výsledky hledání. Výsledky hodnocení odpovědi seskupeny podle mainline obsah a obsah na bočním panelu pro tradiční vyhledávání stránka s výsledky. Pokud není zobrazit výsledky v tradičním nejdůležitějších a bočním panelu format, je nutné zadat nejdůležitějších viditelnost obsahu vyšší než obsah bočním panelu.  
  
V rámci jednotlivých skupin (mainline nebo bočním panelu), [položky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) pole identifikuje obsah musí být zadány v požadovaném pořadí. Každá položka poskytuje z následujících dvou způsobů k identifikaci výsledek v rámci odpověď.  
  
-   `answerType` a `resultIndex` – `answerType` pole identifikuje odpovědí (například webovou stránku nebo News) a `resultIndex` identifikuje výsledek v rámci odpovědí (například článek zprávy). Index je od nuly.  
  
-   `value` — `value` Pole obsahuje ID, které odpovídá ID odpověď nebo výsledek v rámci odpověď. Odpověď nebo výsledky obsahovat ID, ale ne obojí.  
  
Pomocí ID je jednodušší použít, protože potřebujete jenom shodovat s ID hodnocení s ID odpověď nebo jednoho z jeho výsledky. Pokud obsahuje objekt odpovědí `id` pole, společně zobrazit výsledky všech odpovědí. Například pokud `News` objekt zahrnuje `id` pole, společně zobrazit všechny články. Pokud `News` objekt se nenachází `id` pole, pak každý článek zprávy obsahuje `id` pole a odpovědi řazení zkombinuje články s příspěvky s výsledky z jiných odpovědi.  
  
Pomocí `answerType` a `resultIndex` je trochu složitější. Používáte `answerType` k identifikaci odpovědí, který obsahuje výsledky pro zobrazení. Potom použít `resultIndex` do indexu pomocí dané odpovědi výsledky získat výsledek, který má zobrazit. ( `answerType` Hodnota je název v poli [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektu.) Pokud se má zobrazit výsledky všech odpovědí společně, odpovědi řazení položky neobsahuje `resultIndex` pole.  

## <a name="ranking-response-example"></a>Příklad odpovědi řazení

Následující příklad ukazuje, [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Protože nezahrnuje odpověď webové `id` pole, se zobrazí všechny webové stránky jednotlivě podle pořadí (každý webová stránka obsahuje `id` pole). A protože zahrnují obrázků, videí a související hledání odpovědi `id` pole, by zobrazit výsledky jednotlivých podle pořadí společně tyto odpovědi.
  
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
  
Podle této odpovědi hodnocení, nejdůležitějších by zobrazit následující výsledky hledání:  
  
-   První výsledek webovou stránku 
-   Všechny bitové kopie  
-   Výsledky druhý a třetí webovou stránku  
-   Všechna videa  
-   Výsledky webová stránka 4. 5. a 6.  
  
A na bočním panelu by zobrazit následující výsledky vyhledávání:  
  
-   Všechny související hledání  
  

## <a name="next-steps"></a>Další postup

Informace o povýšení unranked výsledků najdete v tématu [povýšení odpovědi, které nejsou seřazeny](./filter-answers.md#promoting-answers-that-are-not-ranked).

Informace o omezení počtu seřazený odpovědi v odpovědi najdete v tématu [omezení počtu odpovědi v odpovědi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

C# příklad, který používá řazení zobrazíte výsledky, najdete v části [C# – tutoriál hodnocení](./csharp-ranking-tutorial.md).