---
title: Co je Project Answer Search?
titlesuffix: Azure Cognitive Services
description: Úvod k hledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 87fe7b008e3e7c6cd8d1a9a870c0fb8ce2f6a7cd
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868240"
---
# <a name="what-is-project-answer-search"></a>Co je Project Answer Search?
Rozhraní API pro hledání odpovědí projekt používá k získání odpovědi na dotazy interrogative koncový bod Bingu v7. Například otázku A "Jak se obvod všech koutech světa?" vrátí odpověď s konkrétní informace.  Dotaz pro osoby, místa nebo věc, kterou vrátí informace o entitu identifikovanou pomocí dotazu. Tyto scénáře může být užitečný v aplikacích, jako je například konverzační robotů, zasílání zpráv aplikace, čtenáři, atd.  

Dotazy vracet odpovědi, které závisí na scénáři dotazu: webové stránky jsou vždy vrátí, zatímco [faktů](fact-queries.md) a/nebo [entity](entity-queries.md) jsou vráceny, pokud je to potřeba.

## <a name="endpoint"></a>Koncový bod
Pokud chcete získat odpovědi na dotaz či informace o osobě, místě nebo věc, odeslat požadavek na koncový bod rozhraní API pro hledání odpovědí. Použijte pro různé specifikace hlaviček a parametrů adresy URL.  Zahrnout *Ocp-Apim-Subscription-Key* záhlaví s platný token.  Na trhu parametr je povinný. Pouze `en-us` momentálně se podporuje na trhu.

Následující dotaz načte odpovědi na otázku: "Co je obvod všech koutech světa?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Parametr adresy URL `q=` je nutné zadat objekt vyhledávání.

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
Hledání odpovědí projektu a projekt Video trendy platí pro ně [požadavky na zobrazení a použití vyhledávání Bingu](use-display-requirements.md).

Jste nebo třetích stran vaším jménem nemusí použít, zachovat, ukládat, ukládat do mezipaměti, sdílet, nebo distribuovat všechna data z rozhraní API ve verzi Preview. adresa URL pro účely testování, vývoje, školení, distribuci nebo zpřístupnění službám jiných společností než Microsoft nebo funkce. 

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Přidělení dat  

Odpovědi na vyhledávání odpovědí na projektu obsahovat informace o vlastní třetími stranami. Budete muset zajistit, aby že využití je vhodná, například při dodržení licence creative commons, které využívají ke svému činnost koncového uživatele.  
  
Pokud odpověď nebo výsledek obsahuje `contractualRules`, `attributions`, nebo `provider` polí, musí atribut data. Pokud odpověď nesmí obsahovat žádný z těchto polí, není třeba žádná přiřazení. V případě, že odpověď obsahuje `contractualRules` pole a `attributions` a/nebo `provider` polí, musíte použít smluvní pravidla pro atribut data.  
  
Následující příklad ukazuje entita, která obsahuje smluvní pravidlo MediaAttribution a bitovou kopii, která zahrnuje `provider` pole. Pravidlo MediaAttribution označuje obrázek, který jako cíl pravidla, takže by ignorovat na obrázku `provider` pole a místo toho použít pravidlo MediaAttribution k označení autorství.  
  
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
  
Pokud obsahuje smluvní pravidlo `targetPropertyName` pole, se pravidlo vztahuje pouze na cílové pole. V opačném případě se pravidlo vztahuje na nadřazený objekt, který obsahuje `contractualRules` pole.  
  
  
V následujícím příkladu `LinkAttribution` pravidlo obsahuje `targetPropertyName` pole, aby se pravidlo vztahuje `description` pole. Pro pravidla, které se vztahují na konkrétní pole musí obsahovat řádek bezprostředně následuje cílových dat, která obsahuje hypertextový odkaz na webu poskytovatele. Například kterému budou připsány popis, přidejte řádek bezprostředně následující text, který obsahuje hypertextový odkaz na data na webu poskytovatele, v tomto případě vytvořit odkaz na en.wikipedia.org.  
  
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

Pokud obsahuje seznam pravidel smluvní [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) pravidlo, je třeba zobrazit oznámení na řádek bezprostředně za obsah, který se vztahuje licence na. `LicenseAttribution` Pravidlo používá `targetPropertyName` pole pro vlastnost, která se vztahuje licence k identifikaci.  
  
Následující příklad zobrazuje příklad, který zahrnuje `LicenseAttribution` pravidlo.  
  
![Přiřazení licencí](./media/licenseattribution.png)  
  
Licence Všimněte si, že zobrazení musí obsahovat hypertextový odkaz na web, který obsahuje informace o licenci. Obvykle vytvořit název licence hypertextový odkaz. Například, pokud je oznámení **Text v rámci licence kopie SA** a kopie SA je název licence, byste vytvořit kopie SA hypertextový odkaz.  
  
### <a name="link-and-text-attribution"></a>Odkaz a Text Attribution  

[LinkAttribution](reference.md#linkattribution) a [TextAttribution](reference.md#textattribution) pravidla se obvykle používají k identifikaci zprostředkovatele data. `targetPropertyName` Pole určuje pole, které se pravidlo vztahuje.  
  
Pro atribut zprostředkovatele, přidejte řádek bezprostředně následující obsah, který se použije Poděkování (třeba na cílové pole). Označuje, že zprostředkovatelů je zdroj dat by měly být jasně popsány řádku. Například "Data z: en.wikipedia.org". Pro `LinkAttribution` pravidla, je nutné vytvořit hypertextový odkaz na webu poskytovatele.  
  
Následující příklad zobrazuje příklad, který zahrnuje `LinkAttribution` a `TextAttribution` pravidla.  
  
![Attribution text odkazu](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Attribution média  

Pokud entita obsahuje bitovou kopii a je zobrazit, je nutné zadat odkaz pomocí kliknutí na webu poskytovatele. Pokud obsahuje entitu [MediaAttribution](reference.md#mediaattribution) pravidla, použijte adresu URL pravidla pro vytvoření odkazu pomocí kliknutí. V opačném případě použijte adresu URL do image zahrnout `provider` pole pro vytvoření odkazu pomocí kliknutí.  
  
Ukazuje následující příklad, který zahrnuje obrazu `provider` pole a smluvní pravidla. Tento příklad zahrnuje smluvní pravidlo, a proto bude ignorovat na obrázku `provider` pole a použít `MediaAttribution` pravidlo.  
  
![Attribution média](./media/mediaattribution.png)  

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start uzlu](node-quickstart.md)
- [Rychlý start Pythonu](python-quickstart.md)
