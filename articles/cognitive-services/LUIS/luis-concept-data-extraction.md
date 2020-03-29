---
title: Extrakce dat - LUIS
description: Extrahovat data z utterance textu s záměry a entity. Zjistěte, jaký druh dat lze extrahovat z jazyka porozumění (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221081"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahování dat z textu utterance s záměry a entitami
Služba LUIS umožňuje získat informace z projevy přirozeného jazyka uživatele. Informace jsou extrahovány tak, aby je mohl použít program, aplikace nebo chatovací robot k provedení akce. V následujících částech se dozvíte, jaká data jsou vrácena ze záměrů a entit s příklady JSON.

Nejtěžší data extrahovat je stroj-naučil data, protože není přesná shoda textu. Extrakce dat [strojově](luis-concept-entity-types.md) získaných entit musí být součástí [cyklu vytváření,](luis-concept-app-iteration.md) dokud si nebudete jisti, že obdržíte data, která očekáváte.

## <a name="data-location-and-key-usage"></a>Umístění dat a využití klíče
Služba LUIS poskytuje data z publikovaného [koncového bodu](luis-glossary.md#endpoint). **Požadavek HTTPS** (POST nebo GET) obsahuje utterance, stejně jako některé volitelné konfigurace, jako je pracovní nebo produkční prostředí.

#### <a name="v2-prediction-endpoint-request"></a>[Požadavek koncového bodu predikce V2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[Požadavek koncového bodu predikce V3](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

K `appID` dispozici je na stránce **Nastavení** aplikace LUIS a také `/apps/`část adresy URL (po) při úpravách této aplikace LUIS. Je `subscription-key` to klíč koncového bodu, který se používá pro dotazování vaší aplikace. Zatímco můžete použít bezplatný klíč pro vytváření a startování, když se učíte LUIS, je důležité změnit klíč koncového bodu na klíč, který podporuje [očekávané použití služby LUIS](luis-boundaries.md#key-limits). Jednotka `timezoneOffset` je pár minut.

**Odpověď HTTPS** obsahuje všechny informace o záměru a entitě, které může služba LUIS určit na základě aktuálního publikovaného modelu pracovního nebo produkčního koncového bodu. Adresa URL koncového bodu se nachází na webu [LUIS,](luis-reference-regions.md) v části **Spravovat,** na stránce **Klíče a koncové body.**

## <a name="data-from-intents"></a>Data ze záměrů
Primární data jsou název **záměru nejvyššího**bodování . Odpověď koncového bodu je:

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|
|Záměr|Řetězec|topScoringIntent.intent|"GetStoreInfo"|

Pokud váš chatbot nebo luis volání aplikace učiní rozhodnutí na základě více než jeden záměr skóre, vrátí všechny záměry skóre.


#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

Nastavte parametr řetězce `verbose=true`dotazu . Odpověď koncového bodu je:

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Nastavte parametr řetězce `show-all-intents=true`dotazu . Odpověď koncového bodu je:

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

Záměry jsou seřazeny od nejvyššího k nejnižšímu skóre.

|Datový objekt|Typ dat|Umístění dat|Hodnota|Skóre|
|--|--|--|--|:--|
|Záměr|Řetězec|záměry[0].záměr|"GetStoreInfo"|0.984749258|
|Záměr|Řetězec|záměry[1].záměr|"Žádné"|0.0168218873|

Pokud přidáte předem sestavené domény, název záměru `Utilties` označuje `Communication` doménu, například nebo také záměr:

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

|Domain (Doména)|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|--|
|Nástroje|Záměr|Řetězec|záměry[0].záměr|"<b>Nástroje</b>. Zobrazit další"|
|Communication|Záměr|Řetězec|záměry[1].záměr|<b>komunikace</b>. Startover"|
||Záměr|Řetězec|záměry[2].záměr|"Žádné"|


## <a name="data-from-entities"></a>Data od subjektů
Většina chatbotů a aplikací potřebuje více než název záměru. Tato další volitelná data pochází z entit zjištěných v utterance. Každý typ entity vrátí různé informace o shodě.

Jedno slovo nebo frázi v utterance může odpovídat více než jednu entitu. V takovém případě je každá odpovídající entita vrácena se svým skóre.

Všechny entity jsou vráceny v poli **entit** odpovědi z koncového bodu:

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Vrácená tokenovaná entita

Zkontrolujte [podporu tokenu](luis-language-support.md#tokenization) v LUIS.

## <a name="simple-entity-data"></a>Jednoduchá data entit

[Jednoduchá entita](reference-entity-simple.md) je strojově navoděná hodnota. Může to být slovo nebo fráze.

## <a name="composite-entity-data"></a>Data složené entity

[Složená entita](reference-entity-composite.md) se skládá z jiných entit, jako jsou předem vytvořené entity, jednoduché, regulární výraz y a seznam entity. Samostatné entity tvoří celou entitu.

## <a name="list-entity-data"></a>Seznam dat entity

[Seznam entit](reference-entity-list.md) představují pevnou, uzavřenou sadu souvisejících slov spolu s jejich synonymy. Služba LUIS nezjišťuje další hodnoty pro entity seznamu. Pomocí funkce **Doporučit** můžete zobrazit návrhy nových slov na základě aktuálního seznamu. Pokud existuje více než jeden seznam entity se stejnou hodnotou, každá entita je vrácena v dotazu koncového bodu.

## <a name="prebuilt-entity-data"></a>Předem vytvořená data entity
[Předem sestavené](luis-concept-entity-types.md) entity jsou zjištěny na základě shody regulárních výrazů pomocí projektu open source [Recognizers-Text.](https://github.com/Microsoft/Recognizers-Text) Předem sestavené entity jsou vráceny v poli entit `builtin::`a používají název typu s předponou . Následující text je příklad utterance s vrácené předdefinované entity:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Bez parametru querystring: `verbose=true`

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

S parametrem querystring: `verbose=true`

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Data entity regulárního výrazu

[Entita regulárního výrazu](reference-entity-regular-expression.md) extrahuje entitu na základě zadaný vzor regulárního výrazu.

## <a name="extracting-names"></a>Extrahování názvů
Získání jména z utterance je obtížné, protože název může být téměř libovolnou kombinaci písmen a slov. V závislosti na tom, jaký typ názvu extrahujete, máte několik možností. Následující návrhy nejsou pravidla, ale další pokyny.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Přidání předem sestavených entit PersonName a GeographyV2

[Entity PersonName](luis-reference-prebuilt-person.md) a [GeographyV2](luis-reference-prebuilt-geographyV2.md) jsou k dispozici v [některých jazykových kulturách](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Jména osob

Jméno lidí může mít nějaký mírný formát v závislosti na jazyku a jazykové verzi. Použijte buď předem sestavenou entitu **[personName,](luis-reference-prebuilt-person.md)** nebo **[jednoduchou entitu](luis-concept-entity-types.md#simple-entity)** s [rolemi](luis-concept-roles.md) křestního jména a příjmení.

Pokud používáte jednoduchou entitu, nezapomeňte uvést příklady, které používají křestní jméno a příjmení v různých částech utterance, v projevy různých délek a projevy napříč všechny záměry, včetně None záměru. [Zkontrolujte](luis-how-to-review-endoint-utt.md) projevy koncového bodu v pravidelných intervalech označit všechny názvy, které nebyly předpovězeny správně.

### <a name="names-of-places"></a>Názvy míst

Názvy lokalit jsou nastaveny a známé, například města, okresy, státy, provincie a země nebo oblasti. K extrahování informací o poloze použijte předem vytvořenou **[entitu geographyV2.](luis-reference-prebuilt-geographyv2.md)**

### <a name="new-and-emerging-names"></a>Nová a vznikající jména

Některé aplikace musí být schopny najít nové a nově vznikající názvy, jako jsou produkty nebo společnosti. Tyto typy názvů jsou nejobtížnějším typem extrakce dat. Začněte **[jednoduchou entitou](luis-concept-entity-types.md#simple-entity)** a přidejte [seznam frází](luis-concept-feature.md). [Zkontrolujte](luis-how-to-review-endoint-utt.md) projevy koncového bodu v pravidelných intervalech označit všechny názvy, které nebyly předpovězeny správně.

## <a name="pattern-roles-data"></a>Data rolí vzorku
Role jsou kontextové rozdíly entit.


#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

Název entity `Location`je , `Origin` se `Destination`dvěma rolemi a .

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Ve V3 je **název role** primární název objektu.

Název entity `Location`je , `Origin` se `Destination`dvěma rolemi a .

Bez parametru querystring: `verbose=true`

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

S parametrem querystring: `verbose=true`

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Data entity Pattern.any

[Pattern.any](reference-entity-pattern-any.md) je zástupný symbol proměnné délky používaný pouze v utterance šablony vzoru k označení místa, kde entita začíná a končí.

## <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurována analýza mínění, odpověď LUIS json zahrnuje analýzu mínění. Další informace o analýze mínění najdete v dokumentaci [k Analýze textu.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

### <a name="sentiment-data"></a>Data mínění
Data mínění je skóre mezi 1 a 0 označující kladné (blíže k 1) nebo negativní (blíže k 0) mínění dat.

Pokud je `en-us`jazyková verze , odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Pro všechny ostatní kultury je odpověď:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Data entity extrakce klíčových frází
Entita extrakce klíčových frází vrátí klíčové fráze v utterance, poskytované [textovou analýzou](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Bez parametru querystring: `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

S parametrem querystring: `verbose=true`

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Data odpovídající více entitam

Služba LUIS vrátí všechny entity zjištěné v utterance. V důsledku toho může být nutné, aby se váš chatbot rozhodl na základě výsledků. Utterance může mít mnoho entit v utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

Koncový bod SLUŽBY LUIS můžete zjistit stejná data v různých entit.

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Bez `verbose=true` parametru querystring.

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

S `verbose=true` jako parametr querystring.


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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Data odpovídající více entitům seznamu

Pokud slovo nebo frázi odpovídá více než jedné entitě seznamu, vrátí dotaz koncového bodu každou entitu Seznamu.

Pro dotaz `when is the best time to go to red rock?`a aplikace má `red` slovo ve více než jednom seznamu, LUIS rozpozná všechny entity a vrátí pole entit jako součást odpovědi koncového bodu JSON:

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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



#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu [Přidání entit.](luis-how-to-add-entities.md)
