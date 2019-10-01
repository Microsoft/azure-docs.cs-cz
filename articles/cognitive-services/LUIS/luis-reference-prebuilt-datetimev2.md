---
title: DatetimeV2 předem připravené entity – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto článku jsou datetimeV2 informace o předem sestavené entitě v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4f46efaeddb0bfe789ef752abdd133c14da514da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677697"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 předem vytvořenou entitu pro aplikaci LUIS

Předdefinované entity **datetimeV2** vyextrahují hodnoty data a času. Tyto hodnoty jsou vyřešeny ve standardizovaném formátu pro použití pro klientské programy. Pokud má utterance datum nebo čas, který není dokončený, LUIS zahrne do odpovědi koncového bodu _předchozí i budoucí hodnoty_ . Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující datetimeV2. 

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 se spravuje z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) v GitHubu.

## <a name="example-json"></a>Ukázkový kód JSON 
Následující příklad odpovědi JSON má @no__t entitu-0 s podtypem `datetime`. Příklady jiných typů entit datetimeV2 naleznete v tématu [podtypy datetimeV2](#subtypes-of-datetimev2)</a>.

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

## <a name="json-property-descriptions"></a>Popisy vlastností JSON

|Název vlastnosti |Typ a popis vlastnosti|
|---|---|
|Entita|textový **řetězec** extrahovaný z utterance s typem data, času, rozsahem dat nebo časovým rozsahem.|
|type|**String** – jeden z [podtypů datetimeV2](#subtypes-of-datetimev2)
|Indexu|**int** – index v utterance, na kterém je entita začínat.|
|Hodnota endIndex|**int** – index v utterance, na kterém končí entita.|
|Rozhodnutí|Má pole `values`, které má jednu, dvě nebo čtyři [hodnoty rozlišení](#values-of-resolution).|
|Účelu|Koncová hodnota času nebo rozsahu kalendářních dat ve stejném formátu jako `value`. Používá se pouze v případě, že `type` je `daterange`, `timerange` nebo `datetimerange`.|

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

Předem sestavená entita **datetimeV2** má následující podtypy a příklady každé z nich jsou uvedeny v následující tabulce:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Hodnoty rozlišení
* Pole má jeden prvek, pokud je datum nebo čas v utterance plně zadaný a jednoznačně.
* Pole má dva prvky, pokud je hodnota datetimeV2 dvojznačná. Nejednoznačnost zahrnuje chybějící konkrétní rok, čas nebo časový rozsah. Příklady naleznete v tématu [dvojznačná data](#ambiguous-dates) . Když je čas nejednoznačný pro dop. nebo odpoledne jsou zahrnuty obě hodnoty.
* Pole má čtyři prvky, pokud má utterance dva prvky s nejednoznačnou. Tato nejednoznačnost zahrnuje prvky, které mají:
  * Datum nebo rozsah dat, který je nejednoznačný jako rok
  * Časový nebo časový rozsah, který je nejednoznačný na dop. nebo odpoledne Například 3:00. dubna 3.

Každý prvek pole `values` může obsahovat následující pole: 

|Název vlastnosti|Popis vlastnosti|
|--|--|
|Timex|čas, datum nebo rozsah dat vyjádřený ve formátu TIMEX, který následuje po [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , a TIMEX3 atributů pro anotaci pomocí jazyka TimeML. Tato poznámka je popsaná v [zásadách TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Podtyp může být jedna z následujících položek: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|hodnota|**Volitelné.** Objekt DateTime ve formátu RRRR: MM: DD (datum), HH: mm: SS (Time) rrrr: MM: DD HH: mm: SS (DateTime). Pokud je hodnota `type` `duration`, jedná se o počet sekund (trvání). <br/> Používá se pouze v případě, že `type` je `datetime` nebo `date`, `time` nebo "Duration".|

## <a name="valid-date-values"></a>Platné hodnoty data

**DatetimeV2** podporuje data mezi následujícími rozsahy:

| Minimum | Max. |
|----------|-------------|
| 1\. ledna 1900   | 31. prosince 2099 |

## <a name="ambiguous-dates"></a>Dvojznačná data

Pokud může být datum v minulosti nebo budoucnosti, LUIS poskytuje obě hodnoty. Příkladem je utterance, který obsahuje měsíc a datum bez roku.  

Například s ohledem na utterance "Květen 2":
* Pokud je dnešní datum třetí 2017, LUIS poskytuje hodnoty "2017-05-02" i "2018-05-02". 
* Pokud je dnešní datum 1. května 2017, LUIS poskytuje jako hodnoty hodnotu "2016-05-02" i "2017-05-02".

Následující příklad ukazuje řešení entity "Květen 2". Toto řešení předpokládá, že dnešní datum je v rozmezí od 2. května 2017 do 1. května 2018.
Pole s `X` v poli `timex` jsou části data, která nejsou explicitně určena v utterance.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Příklady rozlišení rozsahu dat pro číselné datum

Entita `datetimeV2` extrahuje rozsahy data a času. Pole `start` a `end` určují začátek a konec rozsahu. Pro utterance "může 2. května až 5. LUIS poskytuje hodnoty **DateRange** pro aktuální rok i další rok. V poli `timex` označují hodnoty `XXXX` nejednoznačnost roku. `P3D` znamená, že časové období je dlouhé tři dny.

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Příklady rozlišení rozsahu dat pro den v týdnu

Následující příklad ukazuje, jak LUIS používá **datetimeV2** k překladu utterance "úterý na čtvrtek". V tomto příkladu je aktuální datum 19. června. LUIS zahrnuje **DateRange** hodnoty pro oba rozsahy dat, které předcházejí a sledují aktuální datum.

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
## <a name="ambiguous-time"></a>Dvojznačný čas
Pole hodnoty má dva prvky času, pokud je čas nebo časový rozsah nejednoznačný. Pokud je k dispozici dvojznačný čas, hodnoty mají jak dop. a odpoledne časový.

## <a name="time-range-resolution-example"></a>Příklad rozlišení časového rozsahu

Následující příklad ukazuje, jak LUIS používá **datetimeV2** k překladu utterance s časovým rozsahem.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

V rozhraní API V3 se změnila odpověď DatetimeV2 JSON. 

Změny z rozhraní API v2:
* vlastnost `datetimeV2.timex.type` již není vrácena, protože je vrácena na nadřazené úrovni, `datetimev2.type`. 
* Vlastnost `datetimeV2.timex` byla přejmenována na `datetimeV2.value`.

Pro utterance, `8am on may 2nd 2017`, verze V3 DatetimeV2 je:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
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
    }
}
```


* * * 

## <a name="deprecated-prebuilt-datetime"></a>Zastaralá předdefinovaná hodnota DateTime

Předem sestavená entita `datetime` je zastaralá a nahrazuje ji **datetimeV2**. 

Pokud chcete nahradit `datetime` pomocí `datetimeV2` v aplikaci LUIS, proveďte následující kroky:

1. Otevřete podokno **entity** webového rozhraní Luis. 
2. Odstraňte předem vytvořenou entitu **DateTime** .
3. Klikněte na **Přidat předem vytvořenou entitu** .
4. Vyberte **datetimeV2** a klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si informace o [dimenzích](luis-reference-prebuilt-dimension.md), [e-mailových](luis-reference-prebuilt-email.md) entitách a [číslech](luis-reference-prebuilt-number.md). 

