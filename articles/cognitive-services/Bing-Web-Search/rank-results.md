---
title: Použití hodnocení k zobrazení výsledků hledání – rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Naučte se používat hodnocení k zobrazení výsledků hledání z rozhraní API Bingu pro vyhledávání na webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 6c328c681874ba171eab1341a16cf059e359feea
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076274"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Jak používat hodnocení k zobrazení výsledků rozhraní API Bingu pro vyhledávání na webu  

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Každá odpověď na hledání zahrnuje odpověď [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) , která určuje, jak se má zobrazit výsledky hledání. Reakce na hodnocení seskupuje výsledky podle obsahu hlavní a obsahu bočního panelu pro tradiční stránku výsledků hledání. Pokud výsledky nezobrazíte v tradičním formátu hlavní a postranního panelu, musíte poskytnout obsah hlavní větší viditelnost než obsah bočního panelu.  

V rámci každé skupiny (hlavní nebo postranní panel) pole [položky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) identifikuje pořadí, ve kterém se obsah musí objevit. Každá položka poskytuje následující dva způsoby, jak identifikovat výsledek v odpovědi.  

-   `answerType` a `resultIndex` – `answerType` pole identifikuje odpověď (například webovou stránku nebo novinky) a `resultIndex` identifikuje výsledek v odpovědi (například v článku News). Index je založený na nule.  

-   `value` – `value` Pole obsahuje ID, které odpovídá ID odpovědi nebo výsledku v odpovědi. Buď odpověď, nebo výsledky obsahují ID, ale ne obojí.  

Použití ID se zjednodušuje, protože stačí pouze porovnat ID hodnocení s ID odpovědi nebo jedním z jeho výsledků. Pokud objekt odpovědi obsahuje `id` pole, zobrazí se všechny výsledky odpovědi společně. Například pokud `News` objekt obsahuje `id` pole, zobrazí se všechny články ve zprávách dohromady. Pokud `News` objekt neobsahuje `id` pole, pak jednotlivé příspěvky obsahují `id` pole a odpověď na řazení kombinuje články s výsledky z jiných odpovědí.  

Použití `answerType` a `resultIndex` je trochu složitější. Použijete `answerType` k identifikaci odpovědi, která obsahuje výsledky k zobrazení. Pak použijete `resultIndex` k indexování výsledků odpovědi a získáte tak výsledek zobrazení. ( `answerType` Hodnota je název pole v objektu [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) .) Pokud chcete zobrazit všechny výsledky odpovědi společně, položka odpovědi na řazení neobsahuje `resultIndex` pole.  

## <a name="ranking-response-example"></a>Příklad odpovědi na řazení

Následující příklad ukazuje příklad [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Vzhledem k tomu, že webová odpověď neobsahuje `id` pole, měli byste všechny webové stránky zobrazit jednotlivě podle hodnocení (Každá webová stránka obsahuje `id` pole). A vzhledem k tomu, že obrázky, videa a související dotazy hledání obsahují `id` pole, měli byste zobrazit výsledky každé z těchto odpovědí společně na základě hodnocení.

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

V závislosti na této odpovědi na řazení by hlavní zobrazoval následující výsledky hledání:  

-   Výsledek první webové stránky
-   Všechny Image  
-   Výsledky druhé a třetí webové stránky  
-   Všechna videa  
-   Výsledky na čtvrté, 5 a šest webových stránek  

A postranní panel zobrazí následující výsledky hledání:  

-   Všechna související hledání  


## <a name="next-steps"></a>Další kroky

Informace o podpoře neseřazených výsledků najdete v tématu [propagace odpovědí, které nejsou seřazené](./filter-answers.md#promoting-answers-that-are-not-ranked).

Informace o omezení počtu odpovědí seřazených v odpovědi najdete v tématu [omezení počtu odpovědí v odpovědi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Příklad jazyka C#, který používá hodnocení k zobrazení výsledků, naleznete v tématu [kurz pro jazyk c#](./csharp-ranking-tutorial.md).
