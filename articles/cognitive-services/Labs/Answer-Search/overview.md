---
title: Co je Project Answer Search?
titlesuffix: Azure Cognitive Services
description: Úvod k Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 54438a99a710a24daac9e7482cb92a9781d06746
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099086"
---
# <a name="what-is-project-answer-search"></a>Co je Project Answer Search?
Rozhraní API služby Project Answer Search používá k získání odpovědi na zvídavé dotazy koncový bod Bingu v7. Například na otázku „Jaký je obvod Země?“ vrátí odpověď s faktickými informacemi.  Na dotaz týkající se osoby, místa nebo věci, vrátí informace o entitě identifikované dotazem. Tyto scénáře můžou být užitečné v aplikacích, jako jsou například konverzační roboti,aplikace zasílání zpráv, čtenáři, atd.  

Dotazy vrací odpovědi, které závisí na povaze dotazu: webové stránky vrátí vždy, zatímco [fakta](fact-queries.md) a nebo [entity](entity-queries.md) vrátí jenom tehdy, pokud jsou relevantní.

## <a name="endpoint"></a>Koncový bod
Pokud chcete získat odpovědi na dotaz či informace o osobě, místě nebo věci, odešlete požadavek na koncový bod rozhraní API Answer Search. Můžete použít záhlaví a parametry adresy URL pro různé specifikace.  Použijte hlavičku *Ocp-Apim-Subscription-Key* s platným tokenem.  Parametr market je povinný. V tuto chvíli je podporován jenom market `en-us`.

Následující dotaz načte odpovědi na otázky: "Jak se obvod všech koutech světa?"

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

K určení předmětu vyhledávání je nutné zadat parametr adresy URL `q=`.

## <a name="response-object"></a>Objekt odpovědi

Odpověď obsahuje záhlaví HTTP, webové stránky, fakta nebo entity.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Podmínky použití
Na Project Answer Search a Project Video Trends se uplatňují [požadavky Vyhledávání Bingu pro zobrazení a použití ](use-display-requirements.md).

Vy nebo třetí strana jednající vaším jménem nesmí používat, uchovávat, ukládat, ukládat do mezipaměti, sdílet, nebo distribuovat jakákoliv data z rozhraní API ve verzi Preview adresy URL pro účely testování, vývoje, školení, distribuce nebo zpřístupnění službám nebo funkcím jiných společností než Microsoft. 

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Atribuce dat  

Odpovědi Project Answer Search obsahují informace vlastněné třetími stranami. Je vaší zodpovědností zajistit odpovídající použití, například tím, že dodržováním podmínek licencí Creative Commons, na kterých vaše uživatelské prostředí bude stát.  
  
Pokud odpověď nebo výsledek obsahuje pole `contractualRules`, `attributions`, nebo `provider`, musíte provést atribuci dat. Pokud odpověď neobsahuje žádné z těchto polí, není atribuce potřeba. Pokud odpověď obsahuje pole `contractualRules` a pole `attributions` nebo `provider`, musíte použít smluvní pravidla pro atribuci dat.  
  
Následující příklad ukazuje entitu, která obsahuje smluvní pravidlo MediaAttribution a Image zahrnující pole`provider`. Pravidlo MediaAttribution označuje image jako cíl pravidla, takže v takovém případě ignorujte pole `provider` image a místo toho použijte k atribuci pravidlo MediaAttribution.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Pokud smluvní pravidlo obsahuje pole `targetPropertyName`, pak se pravidlo vztahuje pouze na cílové pole. V opačném případě se pravidlo vztahuje na nadřazený objekt, který obsahuje pole `contractualRules`.  
  
  
V následujícím příkladu pravidlo`LinkAttribution` obsahuje pole `targetPropertyName`, takže se pravidlo vztahuje na pole `description`. Pro pravidla, která se vztahují na určitá pole, musíte bezprostředně za cílová data vložit řádek, který bude obsahovat hypertextový odkaz na web zprostředkovatele. Pokud budete například provádět atribuci popisu, vložte bezprostředně za text popisu řádek, který bude obsahovat hypertextový odkaz na data na webu zprostředkovatele, v tomto případě vytvořte odkaz na en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Atribuce licencí  

Pokud obsahuje seznam smluvních pravidel pravidlo [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), musíte uvést poznámku na řádek bezprostředně za obsah, na který se licence vztahuje. Pravidlo`LicenseAttribution` používá pole `targetPropertyName` k identifikaci služby, na kterou se licence vztahuje.  
  
Následující příklad ukazuje použití pravidla`LicenseAttribution`.  
  
![Atribuce licencí](./media/licenseattribution.png)  
  
Poznámky k licencím, které uvádíte, musí obsahovat hypertextový odkaz na web obsahující informace o dané licenci. Obvykle vytvoříte z názvu licence hypertextový odkaz. Pokud je například poznámka **Text je pod licencí CC-BY-SA** a CC-BY-SA je název licence, uděláte z CC-BY-SA hypertextový odkaz.  
  
### <a name="link-and-text-attribution"></a>Atribuce odkazu a textu  

Pravidla [LinkAttribution](reference.md#linkattribution) a [TextAttribution](reference.md#textattribution) se obvykle používají k identifikaci zprostředkovatele dat. Pole `targetPropertyName` určuje pole, na které se pravidlo vztahuje.  
  
Pro určení zdroje zprostředkovatelů, vložte řádek bezprostředně za obsah,na který se určení zdroje vztahuje (například za cílové pole). Řádek by měl jasně označovat, že zdrojem dat jsou zprostředkovatelé. Například „Data z: en.wikipedia.org“. Pro pravidla `LinkAttribution` musíte vytvořit hypertextový odkaz na web zprostředkovatele.  
  
Následující příklad ukazuje použití pravidel `LinkAttribution` a `TextAttribution`.  
  
![Atribuce odkazu a textu](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Určení zdroje médií  

Pokud entita obsahuje image a vy ji zobrazujete, musíte zadat 	klikací odkaz na web zprostředkovatele. Pokud entita obsahuje pravidlo[MediaAttribution](reference.md#mediaattribution), použijte k vytvoření klikacího odkazu adresu URL pravidla. V opačném případě použijte k vytvoření klikacího odkazu adresu URL, která je v poli `provider` dané image.  
  
Tady je příklad, který obsahuje pole `provider` pro image a smluvní pravidla. Tento příklad zahrnuje smluvní pravidlo, a proto budete ignorovat pole `provider` pro image a použijete pravidlo `MediaAttribution`.  
  
![Atribuce médií](./media/mediaattribution.png)  

## <a name="next-steps"></a>Další postup
- [Rychlý start pro jazyk C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
