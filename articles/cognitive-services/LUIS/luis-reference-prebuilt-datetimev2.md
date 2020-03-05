---
title: DatetimeV2 předem připravené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje datetimeV2 informace předem připravených entit v Language Understanding (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270736"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 předem vytvořenou entitu pro aplikaci LUIS

Předdefinované entity **datetimeV2** vyextrahují hodnoty data a času. Tyto hodnoty vyřešit ve standardizovaném formátu pro klientské programy používat. Pokud má utterance datum nebo čas, který není dokončený, LUIS zahrne do odpovědi koncového bodu _předchozí i budoucí hodnoty_ . Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující datetimeV2 k záměry aplikace.

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 se spravuje z úložiště GitHub [pro rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) .

## <a name="example-json"></a>Ukázkový soubor JSON

Níže se zobrazí následující utterance a jeho částečná odpověď JSON.

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

#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/1-2)

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

#### <a name="v2-response"></a>[Odpověď v2](#tab/1-3)

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

|Název vlastnosti |Typ vlastnosti a popis|
|---|---|
|Entita|textový **řetězec** extrahovaný z utterance s typem data, času, rozsahem dat nebo časovým rozsahem.|
|type|**String** – jeden z [podtypů datetimeV2](#subtypes-of-datetimev2)
|Počáteční index|**int** – index v utterance, na kterém je entita začínat.|
|hodnota endIndex|**int** – index v utterance, na kterém končí entita.|
|řešení|Má `values` pole, které má jednu, dvě nebo čtyři [hodnoty rozlišení](#values-of-resolution).|
|end|Koncová hodnota času nebo rozsahu kalendářních dat ve stejném formátu jako `value`. Používá se pouze v případě, že je `type` `daterange`, `timerange`nebo `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

Předem sestavená entita **datetimeV2** má následující podtypy a příklady každé z nich jsou uvedeny v následující tabulce:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Hodnoty rozlišení
* Pole má jeden prvek, pokud data nebo času v utterance je plně zadaný a jednoznačný.
* Pole má dva elementy, pokud je hodnota datetimeV2 nejednoznačný. Nejednoznačnosti obsahuje nedostatek konkrétního roku, čas nebo časový rozsah. Příklady naleznete v tématu [dvojznačná data](#ambiguous-dates) . Když je nejednoznačný čas pro dop. odp., nebo obě hodnoty jsou zahrnuty.
* Pole má čtyři elementy, pokud utterance má dva elementy se nejednoznačnosti. Tuto nejednoznačnost obsahuje prvky, které mají:
  * Datum nebo rozsah, který je nejednoznačný, rok
  * Čas nebo časový rozsah, který je nejednoznačný, čas. nebo odp. Příklad 3:00 3. dubna.

Každý prvek `values` pole může obsahovat následující pole:

|Název vlastnosti|Popis vlastnosti|
|--|--|
|Timex|čas, datum nebo rozsah dat vyjádřený ve formátu TIMEX, který následuje po [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , a TIMEX3 atributů pro anotaci pomocí jazyka TimeML. Tato poznámka je popsaná v [zásadách TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|střední|termín používaný k popisu způsobu použití hodnoty, jako je například `before`, `after`.|
|type|Podtyp může být jedna z následujících položek: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|hodnota|**Volitelné.** Objekt DateTime ve formátu RRRR-MM-DD (datum), HH: mm: SS (Time) RRRR-MM-DD HH: mm: SS (DateTime). Pokud je `type` `duration`, hodnota je počet sekund (trvání). <br/> Používá se pouze v případě, že je `type` `datetime` nebo `date`, `time`nebo Duration.|

## <a name="valid-date-values"></a>Hodnoty platné datum.

**DatetimeV2** podporuje data mezi následujícími rozsahy:

| Minimum | Maximum |
|----------|-------------|
| 1\. ledna 1900   | 31. prosince 2099 |

## <a name="ambiguous-dates"></a>Nejednoznačný kalendářních dat

Pokud se data můžou být v minulosti nebo budoucí, LUIS obsahuje obě hodnoty. Příkladem je utterance, obsahující měsíc a den bez rok.

Například s ohledem na následující utterance:

`May 2nd`

* Pokud je dnešní datum 3. května 2017, LUIS poskytuje "2017-05-02" a "2018-05-02" jako hodnoty.
* Když je dnešní datum 1. května 2017, LUIS poskytuje "2016-05-02" a "2017-05-02" jako hodnoty.

Následující příklad ukazuje rozlišení entity "května 2". Toto řešení předpokládá, že je dnešní datum mezi 2. května 2017 a 1. května 2018.
Pole s `X` v poli `timex` jsou části data, která nejsou explicitně určena v utterance.

## <a name="date-resolution-example"></a>Příklad řešení data


Níže se zobrazí následující utterance a jeho částečná odpověď JSON.

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

#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/2-2)

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

#### <a name="v2-response"></a>[Odpověď v2](#tab/2-3)

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum rozsahu rozlišení příklady číselná data

Entita `datetimeV2` extrahuje rozsahy data a času. Pole `start` a `end` určují začátek a konec rozsahu. Pro utterance `May 2nd to May 5th`poskytuje LUIS hodnoty **DateRange** pro aktuální rok i pro příští rok. V poli `timex` hodnoty `XXXX` označují nejednoznačnost roku. `P3D` označuje, že časové období je dlouhé tři dny.

Níže se zobrazí následující utterance a jeho částečná odpověď JSON.

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


#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/3-2)

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

#### <a name="v2-response"></a>[Odpověď v2](#tab/3-3)

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Datum rozsahu rozlišení příklady pro den v týdnu

Následující příklad ukazuje, jak LUIS používá **datetimeV2** k vyřešení utterance `Tuesday to Thursday`. V tomto příkladu je aktuální datum 19. června. LUIS zahrnuje **DateRange** hodnoty pro oba rozsahy dat, které předcházejí a sledují aktuální datum.

Níže se zobrazí následující utterance a jeho částečná odpověď JSON.

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

#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/4-2)

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

#### <a name="v2-response"></a>[Odpověď v2](#tab/4-3)

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
Pole hodnot má dva prvky času, pokud čas nebo časový rozsah je nejednoznačný. Když je nejednoznačný čas, hodnoty mají i čas. a odpoledne. časy.

## <a name="time-range-resolution-example"></a>Příklad řešení rozsah času

V rozhraní API V3 se změnila odpověď DatetimeV2 JSON. Následující příklad ukazuje, jak LUIS používá **datetimeV2** k překladu utterance s časovým rozsahem.

Změny z rozhraní API v2:
* vlastnost `datetimeV2.timex.type` již není vrácena, protože je vrácena na nadřazenou úroveň `datetimev2.type`.
* Vlastnost `datetimeV2.value` byla přejmenována na `datetimeV2.timex`.

Níže se zobrazí následující utterance a jeho částečná odpověď JSON.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Odpověď V3](#tab/5-1)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

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
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/5-2)

Následující JSON je s parametrem `verbose` nastaveným na `true`:

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/5-3)

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

## <a name="time-resolution-example"></a>Příklad časového rozlišení

Níže se zobrazí následující utterance a jeho částečná odpověď JSON.

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
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/6-2)

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/6-3)

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

## <a name="deprecated-prebuilt-datetime"></a>Nepoužívané předem připravených data a času

Předdefinovaná entita `datetime` je zastaralá a nahrazuje ji **datetimeV2**.

Pokud chcete nahradit `datetime` `datetimeV2` v aplikaci LUIS, proveďte následující kroky:

1. Otevřete podokno **entity** webového rozhraní Luis.
2. Odstraňte předem vytvořenou entitu **DateTime** .
3. Klikněte na **Přidat předem vytvořenou entitu** .
4. Vyberte **datetimeV2** a klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si informace o [dimenzích](luis-reference-prebuilt-dimension.md), [e-mailových](luis-reference-prebuilt-email.md) entitách a [číslech](luis-reference-prebuilt-number.md).

