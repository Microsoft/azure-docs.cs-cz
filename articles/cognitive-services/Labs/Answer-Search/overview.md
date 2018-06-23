---
title: Projekt odpovědí vyhledávání přehled – kognitivní služby Microsoft | Microsoft Docs
description: Úvod k vyhledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d87cf1390970d2c815b94bcaee7e07c19bc03cce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343276"
---
# <a name="what-is-project-answer-search"></a>Co je projekt odpovědí hledání?
Projekt odpovědí vyhledávání API používá koncový bod služby Bing v7 získat odpovědi na interrogative dotazy. Otázka A jako "Co je obvodu zemském povrchu?" vrátí odpověď s konkrétní informace.  Dotaz pro osoby, místní nebo věc vrací informace o entitu identifikovanou pomocí dotazu. Tyto scénáře může být užitečné v aplikacích jako konverzačního robotů, zasílání zpráv na úrovni aplikace, čteček atd.  

Dotazy vracet odpovědi, které závisí na scénáři dotazu: webové stránky jsou vždy vrátí, při [fakty](fact-queries.md) nebo [entity](entity-queries.md) se vrátí v případě relevantní.

## <a name="endpoint"></a>Koncový bod
Získejte odpovědi na otázky nebo informace o osoby, místní nebo věcí, poslat žádost o koncový bod rozhraní API pro vyhledávání odpovědí. Použijte pro různé specifikace hlaviček a parametrů adresy URL.  Zahrnout *Ocp-Apim-Subscription-Key* hlavička s platný token.  Je požadován parametr trhu. Pouze `en-us` trhu v současné době podporuje.

Následující dotaz načte odpovědi na otázku: "Co je obvodu zemském povrchu?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Parametr adresy URL `q=` je potřeba zadat objekt hledání.

## <a name="response-object"></a>Objekt odpovědi

Odpověď obsahuje záhlaví HTTP, webové stránky, faktů nebo entity.

````
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

````

## <a name="terms-of-use"></a>Podmínky použití
Hledání odpovědí projekt a projekt Video trendy, odpovídají rolím [požadavky zobrazení a hledání použijte Bing](use-display-requirements.md).

Můžete nebo třetích stran vaším jménem nemusí používat, zachovat, ukládat, mezipaměti, sdílet, nebo distribuovat všechna data z rozhraní API Preview adresu URL pro účely testování, vývoj, školení, distribuci nebo zpřístupnění jakoukoli službu, jiných společností než Microsoft nebo funkci. 

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Uvedení dat  

Odpovědi na hledání odpovědí projektu obsahují informace ve vlastnictví třetích stran. Odpovídáte Ujistěte se, že používání je vhodné, například při dodržení licence creative commons, které může vaše uživatelské prostředí závisí na.  
  
Pokud odpověď nebo výsledek obsahuje `contractualRules`, `attributions`, nebo `provider` polí, musí atributů data. Pokud odpověď nesmí obsahovat žádný z těchto polí, není potřeba žádná porušení. Pokud odpověď obsahuje `contractualRules` pole a `attributions` nebo `provider` pole, musíte použít smluvními pravidla do atribut data.  
  
Následující příklad ukazuje, entita, která obsahuje pravidlo smluvními MediaAttribution a obsahující bitovou kopii `provider` pole. Pravidlo MediaAttribution identifikuje bitovou kopii jako cíl pravidla, takže by ignorovat obrázku `provider` pole a místo toho použít pravidlo MediaAttribution zajistit porušení.  
  
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
  
Pokud obsahuje smluvními pravidlo `targetPropertyName` pole, toto pravidlo se vztahuje pouze na cílové pole. Toto pravidlo vztahuje, jinak hodnota nadřazeného objektu, který obsahuje `contractualRules` pole.  
  
  
V následujícím příkladu `LinkAttribution` pravidlo obsahuje `targetPropertyName` pole, aby se pravidlo vztahuje `description` pole. Pro pravidla, která se vztahují na určitá pole je nutné zahrnout řádek hned za cílových dat, který obsahuje hypertextový odkaz na webu poskytovatele. Například do atribut popis, zahrnují řádek hned následující text, který obsahuje hypertextový odkaz na data, se na poskytovatele webu v tomto případě vytvořit odkaz na en.wikipedia.org.  
  
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

### <a name="license-attribution"></a>Přiřazení licencí  

Pokud obsahuje seznam smluvními pravidel [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) pravidlo, je třeba zobrazit upozornění na řádek bezprostředně za obsah, který licence, které se vztahují na. `LicenseAttribution` Pravidlo používá `targetPropertyName` pro identifikaci vlastnosti, která licence, které se vztahují na pole.  
  
Zobrazuje následující příklad, který zahrnuje `LicenseAttribution` pravidlo.  
  
![Přiřazení licencí](./media/licenseattribution.png)  
  
Všimněte si licence, že je zobrazit musí obsahovat hypertextový odkaz na web, který obsahuje informace o licenci. Obvykle provedete název licence hypertextový odkaz. Například, pokud je upozornění. **Text v rámci licence kopie SA** a kopie SA je název licence, provedete by kopie SA hypertextový odkaz.  
  
### <a name="link-and-text-attribution"></a>Odkaz a Text porušení  

[LinkAttribution](reference.md#linkattribution) a [TextAttribution](reference.md#textattribution) pravidla jsou obvykle používány k identifikaci zprostředkovatele dat. `targetPropertyName` Pole identifikuje pole, které se pravidlo vztahuje.  
  
Do atributu zprostředkovatele, zahrnují řádku bezprostředně za obsah, který práv použít (třeba na cílové pole). Na řádku by měl být označen jasně označuje, že zprostředkovatele je zdroj dat. Například "Data z: en.wikipedia.org". Pro `LinkAttribution` pravidla, musíte vytvořit hypertextový odkaz na webu poskytovatele.  
  
Zobrazuje následující příklad, který zahrnuje `LinkAttribution` a `TextAttribution` pravidla.  
  
![Uvedení text odkazu](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Uvedení média  

Pokud je entita obsahuje bitovou kopii a zobrazit ji, je nutné zadat odkaz kliknutí na webu poskytovatele. Pokud obsahuje entity [MediaAttribution](reference.md#mediaattribution) pravidla, použijte adresu URL pravidla pro vytvoření odkazu kliknutí. Jinak použijte adresu URL zahrnuté do bitové kopie `provider` pole pro vytvoření odkazu kliknutí.  
  
Zobrazuje následující příklad, který zahrnuje obrázek na `provider` pole a smluvními pravidla. V příkladu zahrnuje smluvními pravidlo, a proto bude ignorovat obrázku `provider` pole a použít `MediaAttribution` pravidlo.  
  
![Uvedení média](./media/mediaattribution.png)  

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](c-sharp-quickstart.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)
