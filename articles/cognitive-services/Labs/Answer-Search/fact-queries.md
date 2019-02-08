---
title: 'Rychlý start: Projekt hledání odpovědí fakt dotazu'
titlesuffix: Azure Cognitive Services
description: Dotazy na fakta pomocí Project Answer Search
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: 2a2e899d9c1c624a5d516a819e7bcf409975c981
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883215"
---
# <a name="quickstart-query-for-facts"></a>Rychlý start: Dotaz pro fakty

Pokud se dotaz týká faktu, jako je například datum nebo identifikovatelná znalost, může odpověď obsahovat odpovědi typu `facts`. Odpovědi faktů obsahují relevantní výsledky extrahované z odstavců ve webových dokumentech.  Tyto dotazy vždycky vrací webové stránky. [Fakta](fact-queries.md) nebo [entity](entity-queries.md) jsou závislé na dotazu.

Dotazy, jako je například valentines+2016 nebo when+is+easter jsou považovány za dotazy související s datem. Pokud Bing určí, že dotaz souvisí s datem, bude odpověď obsahovat odpověď typu `facts`.

V následujícím příkladu je odpověď typu `facts` související s datem.

**Dotaz:**
```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

```

**Odpověď:** `subjectName` Pole obsahuje zobrazení verzi uživatele dotaz, který můžete použít jako popisek při zobrazení na skutečnost. Pokud je řetězec dotazu valentines+2016, Bing jej může změnit na Valentine's Day 2016. Pole description obsahuje fakt.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "valentines 2016"
    },
    "facts" : {
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",
        "value" : [{
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",
            "subjectName" : "Valentine's Day 2016"
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Facts",
                "value" : {
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"
                }
            }]
        }
    }
}

```

Dotaz "Why is the sky blue?" vrátí příklad odpovědi na znalostní otázku.

**Dotaz:**

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

```

**Odpověď:** `value/description` Pole obsahuje znalostní báze nebo dotazem požadované informace.

```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

```

## <a name="tabular-data"></a>Tabulková data
V některých případech můžou být data vrácena jako typ `_type: StructuredValue/TabularData`. Následující dotaz načte tabulková data s informacemi o rozdílech mezi kávou a čajem.

```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us

```
Výsledky `facts` obsahují následující řádky a buňky:
```
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "http://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

```

## <a name="next-steps"></a>Další postup
- [Rychlý start pro jazyk C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
