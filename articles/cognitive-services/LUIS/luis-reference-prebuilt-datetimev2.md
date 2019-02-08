---
title: DatetimeV2 předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje datetimeV2 informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 59fbe18fe7a3cc5b9cf5b31f3e580382b9de08ff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879033"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 předem připravených entit pro aplikace LUIS

**DatetimeV2** předem připravených entit extrahuje hodnoty data a času. Tyto hodnoty vyřešit ve standardizovaném formátu pro klientské programy používat. Když utterance má data nebo času, který není kompletní, LUIS zahrnuje _minulosti i budoucí hodnoty_ v odpovědi koncového bodu. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující datetimeV2 k záměry aplikace. 

## <a name="types-of-datetimev2"></a>Typy datetimeV2
Spravuje se z DatetimeV2 [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) úložiště GitHub

## <a name="example-json"></a>Ukázkový soubor JSON 
Následující příklad odpověď JSON má `datetimeV2` entita s podtypem typu `datetime`. Příklady dalších typů entit datetimeV2 najdete v tématu [podtypy datetimeV2](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Popisy vlastností JSON

|Název vlastnosti |Typ vlastnosti a popis|
|---|---|
|Entita|**řetězec** -Text extrahovaný z utterance s typem, čas, rozsah kalendářních dat nebo časový rozsah.|
|type|**řetězec** – jeden z [podtypy datetimeV2](#subtypes-of-datetimev2)
|Počáteční index|**int** -index utterance, při kterém začíná entity.|
|hodnota endIndex|**int** -index utterance, u které končí entity.|
|řešení|Má `values` pole, které má jednu, dvě nebo čtyři [hodnot rozlišení](#values-of-resolution).|
|konec|Koncová hodnota čas nebo rozsah dat ve stejném formátu jako `value`. Použít jenom v případě `type` je `daterange`, `timerange`, nebo `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

**DatetimeV2** předem připravených entit má následující podtypy a příklady pro každou z jsou uvedeny v následující tabulce:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Hodnoty rozlišení
* Pole má jeden prvek, pokud data nebo času v utterance je plně zadaný a jednoznačný.
* Pole má dva elementy, pokud je hodnota datetimeV2 nejednoznačný. Nejednoznačnosti obsahuje nedostatek konkrétního roku, čas nebo časový rozsah. Zobrazit [nejednoznačných](#ambiguous-dates) příklady. Když je nejednoznačný čas pro dop. odp., nebo obě hodnoty jsou zahrnuty.
* Pole má čtyři elementy, pokud utterance má dva elementy se nejednoznačnosti. Tuto nejednoznačnost obsahuje prvky, které mají:
  * Datum nebo rozsah, který je nejednoznačný, rok
  * Čas nebo časový rozsah, který je nejednoznačný, čas. nebo odp. Příklad 3:00 3. dubna.

Každý prvek `values` pole může obsahovat následující pole: 

|Název vlastnosti|Popis vlastnosti|
|--|--|
|Timex|čas, datum nebo období vyjádřena v TIMEX formátu, který následuje [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a atributy TIMEX3 poznámky v jazyce TimeML. Tato poznámka je popsána v [TIMEX pokyny](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Podtyp, což může být jeden z následujících položek: datum a čas, datum, čas, daterange, timerange, datetimerange, doba trvání, set.|
|hodnota|**Volitelné.** Datum a čas objektu ve formátu yyyy:MM:dd (datum), hh: mm: (čas) yyyy:MM:dd hh: mm: (datetime). Pokud `type` je `duration`, hodnota je počet sekund (doba trvání) <br/> Použít jenom v případě `type` je `datetime` nebo `date`, `time`, nebo "doba trvání.|

## <a name="valid-date-values"></a>Hodnoty platné datum.

**DatetimeV2** podporuje kalendářní data mezi následující rozsahy:

| Minimum | Maximum |
|----------|-------------|
| 1. ledna 1900   | 31. prosince 2099 |

## <a name="ambiguous-dates"></a>Nejednoznačný kalendářních dat

Pokud se data můžou být v minulosti nebo budoucí, LUIS obsahuje obě hodnoty. Příkladem je utterance, obsahující měsíc a den bez rok.  

Mějme například utterance "Května 2":
* Pokud je dnešní datum 3. května 2017, LUIS poskytuje "2017-05-02" a "2018-05-02" jako hodnoty. 
* Když je dnešní datum 1. května 2017, LUIS poskytuje "2016-05-02" a "2017-05-02" jako hodnoty.

Následující příklad ukazuje rozlišení entity "května 2". Toto řešení předpokládá, že je dnešní datum mezi 2. května 2017 a 1. května 2018.
Pole s `X` v `timex` pole jsou částí data, které nejsou explicitně zadané v utterance.

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
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum rozsahu rozlišení příklady číselná data

`datetimeV2` Entity extrahuje data a času rozsahy. `start` a `end` pole ukazují na začátek a konec rozsahu. Pro utterance "Května 2. na 5. května", LUIS poskytuje **daterange** hodnoty do aktuálního roku a příští rok. V `timex` pole, `XXXX` hodnoty označují nejednoznačnosti v roce. `P3D` Určuje časové období je tři dny dlouho.

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
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Datum rozsahu rozlišení příklady pro den v týdnu

Následující příklad ukazuje, jak se využívá LUIS **datetimeV2** vyřešit utterance "Úterý na čtvrtek". V tomto příkladu je aktuální datum 19. června. Služba LUIS zahrnuje **daterange** hodnoty pro oba rozsahy kalendářních dat, které jí předcházejí a postupujte podle aktuálního data.

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
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Nejednoznačný čas
Pole hodnot má dva prvky času, pokud čas nebo časový rozsah je nejednoznačný. Když je nejednoznačný čas, hodnoty mají i čas. a odpoledne. časy.

## <a name="time-range-resolution-example"></a>Příklad řešení rozsah času

Následující příklad ukazuje, jak se využívá LUIS **datetimeV2** vyřešit utterance, který má časový rozsah.

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

## <a name="deprecated-prebuilt-datetime"></a>Nepoužívané předem připravených data a času

`datetime` Předem připravených entit je zastaralé a nahrazují **datetimeV2**. 

Chcete-li nahradit `datetime` s `datetimeV2` ve vaší aplikaci LUIS, proveďte následující kroky:

1. Otevřít **entity** podokno webového rozhraní LUIS. 
2. Odstranit **data a času** předem připravených entit.
3. Klikněte na tlačítko **přidat předem připravených entit**
4. Vyberte **datetimeV2** a klikněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace o [dimenze](luis-reference-prebuilt-dimension.md), [e-mailu](luis-reference-prebuilt-email.md) entity, a [číslo](luis-reference-prebuilt-number.md). 

