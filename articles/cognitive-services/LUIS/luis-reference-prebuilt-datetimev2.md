---
title: DatetimeV2 Předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě datetimeV2 v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270736"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 předem sestavená entita pro aplikaci LUIS

Předdefinovaná entita **datetimeV2** extrahuje hodnoty data a času. Tyto hodnoty vyřešit ve standardizovaném formátu pro klientské programy využívat. Pokud utterance má datum nebo čas, který není dokončen, LUIS zahrnuje _minulé i budoucí hodnoty_ v odpovědi koncového bodu. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidat příklad projevy obsahující datetimeV2 záměry aplikace.

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 se spravuje z úložiště GitHub [s textem pro rozpoznávání.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>Příklad JSON

Následující utterance a jeho částečná odpověď JSON je uveden níže.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[Odpověď V3](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Odpověď V2](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Název vlastnosti |Typ a popis vlastnosti|
|---|---|
|Entita|**řetězec** - Text extrahovaný z utterance s typem data, času, časového rozsahu nebo časového rozsahu.|
|type|**string** - Jeden [z podtypů datetimeV2](#subtypes-of-datetimev2)
|Startindex|**int** - Index v utterance, ve kterém začíná entita.|
|endIndex|**int** - Index v utterance, na kterém entita končí.|
|řešení|Má `values` pole, které má [jednu,](#values-of-resolution)dvě nebo čtyři hodnoty rozlišení .|
|end|Koncová hodnota času nebo časového období ve stejném `value`formátu jako . Používá se `type` `daterange`pouze `timerange`v případě, že je , , nebo`datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

Předestavěná entita **datetimeV2** má následující podtypy a příklady každého z nich jsou uvedeny v následující tabulce:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Hodnoty rozlišení
* Pole má jeden prvek, pokud je datum nebo čas v utterance plně zadán a jednoznačný.
* Pole má dva prvky, pokud datetimeV2 hodnota je nejednoznačná. Nejednoznačnost zahrnuje nedostatek konkrétního roku, času nebo časového rozsahu. Příklady viz [Nejednoznačná data.](#ambiguous-dates) Když je čas nejednoznačný pro A.M. nebo P.M., jsou zahrnuty obě hodnoty.
* Pole má čtyři prvky, pokud utterance má dva prvky s nejednoznačností. Tato nejednoznačnost zahrnuje prvky, které mají:
  * Datum nebo rozsah dat, který je nejednoznačný jako rok
  * Časový nebo časový rozsah, který je nejednoznačný, pokud jde o dopoledne. nebo odp. Například 3:00 3.dubna.

Každý prvek `values` pole může mít následující pole:

|Název vlastnosti|Popis vlastnosti|
|--|--|
|Timex|čas, datum nebo rozsah dat vyjádřený ve formátu TIMEX, který se řídí [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a atributy TIMEX3 pro poznámky pomocí jazyka TimeML. Tato anotace je popsána v [pokynech TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|Mod|termín používaný k popisu, jak `before`používat `after`hodnotu, jako je , .|
|type|Podtyp, který může být jednou z `datetime` `date`následujících položek: `duration` `set`, , `time`, `daterange` `timerange`, `datetimerange`, , .|
|value|**Volitelné.** Objekt datetime v formátu yyyy-MM-dd (datum), HH:mm:ss (čas) yyyy-MM-dd HH:mm:ss (datetime). Pokud `type` `duration`je , hodnota je počet sekund (doba trvání) <br/> Používá se `type` `datetime` pouze `date` `time`v případě, že je nebo , nebo 'doba trvání.|

## <a name="valid-date-values"></a>Platné hodnoty kalendářních dat

**DatetimeV2** podporuje data mezi následujícími rozsahy:

| Minimum | Maximum |
|----------|-------------|
| 1. ledna 1900   | 31. prosince 2099 |

## <a name="ambiguous-dates"></a>Nejednoznačná data

Pokud datum může být v minulosti nebo budoucnosti, LUIS poskytuje obě hodnoty. Příkladem je utterance, která zahrnuje měsíc a datum bez roku.

Například vzhledem k následující utterance:

`May 2nd`

* Pokud je dnešní datum 3.května 2017, LUIS poskytuje jako hodnoty "2017-05-02" a "2018-05-02".
* Když je dnešní datum 1.května 2017, LUIS poskytuje jako hodnoty "2016-05-02" a "2017-05-02".

Následující příklad ukazuje rozlišení entity "2.května". Toto usnesení předpokládá, že dnešní datum je datum mezi 2.5.2017 a 1.5.2018.
Pole `X` s `timex` v poli jsou části data, které nejsou explicitně zadány v utterance.

## <a name="date-resolution-example"></a>Příklad rozlišení data


Následující utterance a jeho částečná odpověď JSON je uveden níže.

`May 2nd`

#### <a name="v3-response"></a>[Odpověď V3](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[Odpověď V2](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Příklady rozlišení rozsahu kalendářních dat pro číselné datum

Entita `datetimeV2` extrahuje rozsahy data a času. Pole `start` `end` a určují začátek a konec rozsahu. Pro utterance `May 2nd to May 5th`, LUIS poskytuje **daterange** hodnoty pro aktuální rok a další rok. V `timex` poli `XXXX` hodnoty označují nejednoznačnost roku. `P3D`označuje, že časové období je dlouhé tři dny.

Následující utterance a jeho částečná odpověď JSON je uveden níže.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[Odpověď V3](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[Odpověď V2](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Příklady rozlišení rozsahu kalendářních dat pro den v týdnu

Následující příklad ukazuje, jak LUIS používá **datetimeV2** k vyřešení utterance `Tuesday to Thursday`. V tomto příkladu je aktuální datum 19. Služba LUIS obsahuje hodnoty **rozsahu dat** pro obě období, která předcházejí aktuálnímu datu a následují je.

Následující utterance a jeho částečná odpověď JSON je uveden níže.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[Odpověď V3](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Odpověď V2](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Nejednoznačný čas
Pole hodnot má dva časové prvky, pokud je čas nebo časový rozsah nejednoznačný. Když je nejednoznačný čas, hodnoty mají oba AM. a P.M. Krát.

## <a name="time-range-resolution-example"></a>Příklad rozlišení časového rozsahu

DatetimeV2 JSON odpověď byla změněna v rozhraní API V3. Následující příklad ukazuje, jak LUIS používá **datetimeV2** k vyřešení utterance, která má časový rozsah.

Změny z rozhraní API V2:
* `datetimeV2.timex.type`vlastnost již není vrácena, `datetimev2.type`protože je vrácena na nadřazené úrovni .
* Vlastnost `datetimeV2.value` byla přejmenována `datetimeV2.timex`na .

Následující utterance a jeho částečná odpověď JSON je uveden níže.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Odpověď V3](#tab/5-1)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/5-2)

Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[Odpověď V2](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Příklad rozlišení času

Následující utterance a jeho částečná odpověď JSON je uveden níže.

`8am`

#### <a name="v3-response"></a>[Odpověď V3](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[Odpověď V2](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Zastaralé předem sestavené datum

Předem `datetime` sestavená entita je zastaralá a nahrazena **datetimeV2**.

Chcete-li nahradit `datetime` `datetimeV2` v aplikaci LUIS, proveďte následující kroky:

1. Otevřete podokno **Entity** webového rozhraní LUIS.
2. Odstraňte předem sestavenou entitu **datetime.**
3. Klikněte na **Přidat předem vytvořenou entitu.**
4. Vyberte **datetimeV2** a klepněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Informace o [dimenzi](luis-reference-prebuilt-dimension.md), [e-mailových](luis-reference-prebuilt-email.md) entitách a [čísle](luis-reference-prebuilt-number.md).

