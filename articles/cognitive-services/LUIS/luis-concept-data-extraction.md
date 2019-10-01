---
title: Extrakce dat – LUIS
titleSuffix: Azure Cognitive Services
description: Extrahovat data z utterance textu s záměry a entitami. Zjistěte, jaký druh dat je možné extrahovat z Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: ff0a9838d1fcc9db3b6cc25b47c840e01056e6cd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703139"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrakce dat z utterance textu s využitím záměrů a entit
LUIS poskytuje možnost získávat informace z přirozeného jazyka projevy uživatele. Informace jsou extrahovány způsobem, který může být použit programem, aplikací nebo robotem chatu k provedení akce. V následujících částech se dozvíte, jaká data se vracejí z záměrů a entit s příklady JSON.

Nejzávažnější data k extrakci jsou data získaná počítačem, protože se neshoduje s přesným textem. Extrakce dat v počítačích, které [se naučila](luis-concept-entity-types.md) , musí být součástí [cyklu vytváření](luis-concept-app-iteration.md) , dokud nebudete mít jistotu, že obdržíte očekávaná data.

## <a name="data-location-and-key-usage"></a>Umístění dat a použití klíče
LUIS poskytuje data z publikovaného [koncového bodu](luis-glossary.md#endpoint). **Požadavek https** (post nebo Get) obsahuje utterance a také některé volitelné konfigurace, například pracovní nebo produkční prostředí.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Hodnota koncového bodu předpovědi v2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Požadavek na koncový bod verze V3](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

@No__t-0 je k dispozici na stránce **Nastavení** aplikace Luis a také v rámci adresy URL (po `/apps/`) při úpravách této aplikace Luis. @No__t-0 je klíč koncového bodu, který slouží k dotazování aplikace. I když se naučíte LUIS, můžete použít bezplatný a počáteční klíč, ale je důležité změnit klíč koncového bodu na klíč, který podporuje [očekávané využití Luis](luis-boundaries.md#key-limits). Jednotka `timezoneOffset` má minuty.

**Odpověď https** obsahuje všechny informace o záměru a entitě, které Luis může určit na základě aktuálního publikovaného modelu pracovního nebo produkčního koncového bodu. Adresa URL koncového bodu se nachází na webu [Luis](luis-reference-regions.md) v části **Správa** na stránce **klíče a koncové body** .

## <a name="data-from-intents"></a>Data z záměrů
Primární data jsou nejvyšším **názvem záměru**hodnocení. Odpověď koncového bodu:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

|Datový objekt|Datový typ|Umístění dat|Hodnota|
|--|--|--|--|
|Úmysl|String|topScoringIntent. záměr|"GetStoreInfo"|

Pokud vaše aplikace chatovací robot nebo LUIS volá rozhodnutí na základě více než jednoho skóre záměru, vrátí všechny skóre záměrů.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Nastavte parametr QueryString `verbose=true`. Odpověď koncového bodu:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Nastavte parametr QueryString `show-all-intents=true`. Odpověď koncového bodu:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

Záměry jsou seřazené od nejvyšších po nejnižší skóre.

|Datový objekt|Datový typ|Umístění dat|Hodnota|Podtržítk|
|--|--|--|--|:--|
|Úmysl|String|záměry [0]. záměr|"GetStoreInfo"|0,984749258|
|Úmysl|String|záměry [1]. záměr|NTato|0,0168218873|

Pokud přidáte předem připravené domény, název záměru označuje doménu, například `Utilties` nebo `Communication`, a také záměr:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

|Domain|Datový objekt|Datový typ|Umístění dat|Hodnota|
|--|--|--|--|--|
|Nástroje|Úmysl|String|záměry [0]. záměr|"<b>Nástroje</b>. ShowNext"|
|Telecommunication|Úmysl|String|záměry [1]. záměr|<b>Komunikace</b>. StartOver"|
||Úmysl|String|záměry [2]. záměr|NTato|


## <a name="data-from-entities"></a>Data z entit
Většina chatovacích robotů o a aplikací vyžaduje více než název záměru. Tato další volitelná data pocházejí z entit zjištěných v utterance. Každý typ entity vrátí různé informace o shodě.

Jedno slovo nebo fráze v utterance se může shodovat s více než jednou entitou. V takovém případě se každá odpovídající entita vrátí se svým skóre.

Všechny entity se vrátí v poli **entity** odpovědi z koncového bodu:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

## <a name="tokenized-entity-returned"></a>Byla vrácena entita s tokenem.
Několik [kultur](luis-language-support.md#tokenization) vrací objekt entity s hodnotou `entity` s [tokenem](luis-glossary.md#token). Hodnota startIndex a hodnota endIndex vrácená nástrojem LUIS v objektu entity není mapována na novou hodnotu s tokeny, ale místo na původní dotaz, aby bylo možné nezpracované entity programově extrahovat. 

Například v němčině je slovo `das Bauernbrot` rozděleno do `das bauern brot`. Vyhodnocená hodnota `das bauern brot` se vrátí a původní hodnota se dá programově určit z hodnoty startIndex a hodnota endIndex původního dotazu. tím `das Bauernbrot`.

## <a name="simple-entity-data"></a>Jednoduchá data entity

[Jednoduchá entita](reference-entity-simple.md) je hodnota zjištěná počítačem. Může se jednat o slovo nebo frázi.

## <a name="composite-entity-data"></a>Data složených entit

[Složená entita](reference-entity-composite.md) je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celou entitu. 

## <a name="list-entity-data"></a>Vypsat data entity

[Seznam entit](reference-entity-list.md) představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. LUIS nezjistí další hodnoty pro entity seznamu. Pomocí funkce **doporučit** můžete zobrazit návrhy nových slov na základě aktuálního seznamu. Pokud existuje více než jedna entita seznamu se stejnou hodnotou, Každá entita se vrátí v dotazu koncového bodu. 

## <a name="prebuilt-entity-data"></a>Předem vytvořená data entity
[Předem připravené](luis-concept-entity-types.md) entity jsou zjištěny na základě regulárního výrazu, který se shoduje s použitím open source [rozpoznávacích výrazů – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Předem připravené entity jsou vráceny v poli entity a používají název typu s předponou `builtin::`. Následující text je příklad utterance s vrácenými předem vytvořenými entitami:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Bez parametru QueryString `verbose=true`:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

S parametrem QueryString `verbose=true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 
## <a name="regular-expression-entity-data"></a>Data entity regulárního výrazu

[Entita regulárního výrazu](reference-entity-regular-expression.md) extrahuje entitu na základě vzoru regulárního výrazu, který zadáte.

## <a name="extracting-names"></a>Extrahování názvů
Získání názvů z utterance je obtížné, protože název může být skoro libovolná kombinace písmen a slov. V závislosti na tom, jaký typ názvu se chystáte extrahovat, máte několik možností. Následující návrhy nejsou pravidla, ale další pokyny.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Přidání předdefinovaných entit Person a GeographyV2

Entity [Person](luis-reference-prebuilt-person.md) a [GeographyV2](luis-reference-prebuilt-geographyV2.md) jsou k dispozici v některých [jazykových kulturách](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Jména lidí

Jméno osoby může mít v závislosti na jazyku a jazykové verzi trochu mírné formátování. Použijte buď předem sestavenou entitu **[Person](luis-reference-prebuilt-person.md)** , nebo **[jednoduchou entitu](luis-concept-entity-types.md#simple-entity)** s [rolemi](luis-concept-roles.md) jména a příjmení. 

Pokud používáte jednoduchou entitu, nezapomeňte uvést příklady, které používají jméno a příjmení v různých částech utterance, v projevy různých délek a projevy napříč všemi záměry, včetně záměru None. Pravidelně [kontrolujte](luis-how-to-review-endoint-utt.md) koncový bod projevy, abyste mohli popsat všechny názvy, které nebyly předpovídat správně.

### <a name="names-of-places"></a>Názvy míst

Názvy umístění se nastavují a označují jako města, okresy, stavy, provincie a země nebo oblasti. K extrakci informací o poloze použijte předem sestavenou entitu **[geographyV2](luis-reference-prebuilt-geographyv2.md)** .

### <a name="new-and-emerging-names"></a>Nové a vycházející názvy

Některé aplikace musí být schopné najít nové a nově vznikající názvy, jako jsou produkty nebo společnosti. Tyto typy názvů jsou nejobtížnějším typem extrakce dat. Začněte **[jednoduchou entitou](luis-concept-entity-types.md#simple-entity)** a přidejte [seznam frází](luis-concept-feature.md). Pravidelně [kontrolujte](luis-how-to-review-endoint-utt.md) koncový bod projevy, abyste mohli popsat všechny názvy, které nebyly předpovídat správně.

## <a name="pattern-roles-data"></a>Data vzorových rolí
Role jsou kontextové rozdíly mezi entitami.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Název entity je `Location` se dvěma rolemi `Origin` a `Destination`.

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

V v3 je **název role** primárním názvem objektu. 

Název entity je `Location` se dvěma rolemi `Origin` a `Destination`.

Bez parametru QueryString `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

S parametrem QueryString `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Vzor. libovolná data entity

[Pattern. any](reference-entity-pattern-any.md) je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita.  

## <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurovaná analýza mínění, odpověď LUIS JSON zahrnuje analýzu mínění. Další informace o analýze mínění najdete v dokumentaci k [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .

### <a name="sentiment-data"></a>Mínění data
Mínění data jsou skóre mezi 1 a 0 značící kladné (blíže k 1) nebo záporné (navýšení na 0) mínění dat.

Pokud je jazyková verze `en-us`, odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Pro všechny ostatní jazykové verze je odpověď:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Data entity extrakce klíčových frází
Entita pro extrakci klíčových frází vrátí klíčové fráze v utterance, které poskytuje [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Bez parametru QueryString `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

S parametrem QueryString `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Data, která odpovídají více entitám

LUIS vrátí všechny entity zjištěné ve utterance. V důsledku toho může vaše chatovací robot vyžadovat rozhodnutí na základě výsledků. Utterance může mít mnoho entit v utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

Koncový bod LUIS může vyhledat stejná data v různých entitách.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Bez `verbose=true` jako parametru QueryString.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

With `verbose=true` jako parametr řetězce dotazu.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Data, která odpovídají více entitám seznamu

Pokud slovo nebo fráze odpovídá více entitám seznamu, dotaz koncového bodu vrátí každou entitu seznamu.

U dotazu `when is the best time to go to red rock?` a aplikace má slovo `red` ve více než jednom seznamu, LUIS rozpoznává všechny entity a vrátí pole entit jako součást odpovědi koncového bodu JSON: 

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Bez `verbose=true` v řetězci dotazu:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


S `verbose=true` v řetězci dotazu:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .
