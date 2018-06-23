---
title: Předem sestavené LEOŠ entity datetimeV2 odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek má datetimeV2 předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 261f6f27c39c280efdcd070888d735374a473c85
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321887"
---
# <a name="datetimev2-entity"></a>DatetimeV2 entity

**DatetimeV2** předem entity extrahuje hodnoty data a času. Tyto hodnoty vyřešit ve formátu standardizované pro programy klienta využívají. Pokud utterance datum nebo čas, který není kompletní, zahrnuje LEOŠ _minulosti a budoucí hodnoty_ v odpovědi koncového bodu. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující datetimeV2 do tříd Intent aplikace. 

## <a name="types-of-datetimev2"></a>Typy datetimeV2
Spravované DatetimeV2 od [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) úložiště Github

## <a name="example-json"></a>Příklad JSON 
Následující příklad odpověď JSON má `datetimeV2` entita s podtypem `datetime`. Příklady dalších typů entit datetimeV2 najdete v tématu [podtypů datetimeV2](#subtypes-of-datetimev2)</a>.

```JSON
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
|Entita|**řetězec** – Text, na které se extrahují z utterance s typem datum, čas, rozsah nebo časový rozsah.|
|type|**řetězec** – jeden z [podtypů datetimeV2](#subtypes-of-datetimev2)
|Počáteční index|**int** – index v utterance, při kterém začíná entity.|
|endIndex|**int** – index v utterance, u které končí entity.|
|řešení|Má `values` pole, která obsahuje jeden, dva nebo čtyři [hodnoty řešení](#values-of-resolution).|
|konec|Koncová hodnota čas nebo rozsah dat ve stejném formátu jako `value`. Použít jen v případě `type` je `daterange`, `timerange`, nebo `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Podtypů datetimeV2

**DatetimeV2** předem entita, která má následující podtypů a příklady jednotlivých jsou uvedeny v následující tabulce:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Hodnoty řešení
* Pole má jeden element. Pokud data a času v utterance plně zadaný a jednoznačné.
* Pole má dva elementy, pokud je hodnota datetimeV2 nejednoznačný. Nejednoznačnosti zahrnuje nedostatek konkrétní roku, čas nebo časový rozsah. V tématu [nejednoznačných dat](#ambiguous-dates) příklady. Když je nejednoznačný čas pro dop. hodinou, nebo obě hodnoty jsou zahrnuty.
* Pole má čtyři prvky, pokud utterance má dva elementy se nejednoznačnosti. Tato nejednoznačnost obsahuje prvky, které mají:
  * Datum nebo rozsah dat, který je nejednoznačné, roku
  * Čas nebo časového rozsahu, který je nejednoznačné, dop. nebo odp. Například 3. dubna 3:00.

Každý element `values` pole může obsahovat následující pole: 

|Název vlastnosti|Popis vlastnosti|
|--|--|
|Timex|čas, datum nebo rozsah dat vyjádřené v TIMEX formátu, který odpovídá [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a atributy TIMEX3 pomocí jazyka TimeML poznámky. Tato anotace je podrobněji popsaná [TIMEX pokyny](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Podtyp, což může mít jednu z následujících položek: datum a čas, datum, čas, daterange, timerange, datetimerange, doba trvání, sada.|
|hodnota|**Volitelný parametr.** Objekt datum a čas ve formátu yyyy:MM:dd (date) (čas) hh: mm: yyyy:MM:dd hh: mm: (datetime). Pokud `type` je `duration`, hodnota je počet sekund (doba trvání) <br/> Použít jen v případě `type` je `datetime` nebo `date`, `time`, nebo se doba trvání.|

## <a name="valid-date-values"></a>Hodnoty platné datum.

**DatetimeV2** podporuje kalendářní data mezi následující rozsahy:

| Minimum | Maximum |
|----------|-------------|
| 1. ledna 1900   | 31. prosince 2099 |

## <a name="ambiguous-dates"></a>Nejednoznačný kalendářních dat

Pokud data může být v minulosti nebo budoucí, poskytuje LEOŠ obě hodnoty. Příkladem je utterance obsahující měsíc a datum bez roku.  

Například uděleno utterance "Může 2":
* Pokud je aktuální datum je 3. 2017 může, poskytuje LEOŠ "2017-05-02" a "2018-05-02" jako hodnoty. 
* 1. 2017 může po dnešní datum LEOŠ poskytuje "2016-05-02" a "2017-05-02" jako hodnoty.

Následující příklad ukazuje řešení entity "může 2". Toto řešení předpokládá, že je aktuální datum je datum mezi 2017 2 může a 1 2018 může.
Pole s `X` v `timex` pole jsou částí data, která nejsou explicitně zadané v utterance.

```JSON
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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum rozsahu řešení příklady číselná data

`datetimeV2` Entity extrahuje data a času. `start` a `end` pole zadejte začátek a konec rozsahu. Pro utterance, které jsou "Může 2. do 5. pravděpodobně", poskytuje LEOŠ **daterange** hodnoty do aktuálního roku a do následujícího roku. V `timex` pole, `XXXX` hodnoty stanovují nejednoznačnosti roku. `P3D` Určuje časové období dlouhé tři dny.

```JSON
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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Příkladem řešení rozsah data den v týdnu

Následující příklad ukazuje, jak LEOŠ používá **datetimeV2** vyřešit utterance "Úterý na čtvrtek". V tomto příkladu je aktuální datum 19. června. Zahrnuje LEOŠ **daterange** hodnoty pro obě rozsahů, které předcházet a postupujte podle aktuálního data.

```JSON
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
Pole hodnoty má dva elementy čas, pokud čas nebo časový rozsah je nejednoznačný. Když je nejednoznačný čas, hodnoty mají obě dop. a odp. časy.

## <a name="time-range-resolution-example"></a>Příklad řešení rozsah čas

Následující příklad ukazuje, jak LEOŠ používá **datetimeV2** vyřešit utterance, který má časový rozsah.

```
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

## <a name="deprecated-prebuilt-datetime"></a>Nepoužívané předem data a času

`datetime` Předem entity je zastaralá a nahrazuje [ `datetimeV2` ](#builtindatetimev2). 

Chcete-li nahradit `datetime` s `datetimeV2` ve vaší aplikaci LEOŠ proveďte následující kroky:

1. Otevřete **entity** podokně LEOŠ webové rozhraní. 
2. Odstranit **data a času** předem entity.
3. Klikněte na tlačítko **přidat předem entity**
4. Vyberte **datetimeV2** a klikněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace o [dimenze](luis-reference-prebuilt-dimension.md), [e-mailu](luis-reference-prebuilt-email.md) entity, a [číslo](luis-reference-prebuilt-number.md). 

