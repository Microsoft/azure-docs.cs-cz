---
title: Analyzovat metodu – rozhraní API pro jazykovou analýzu
titlesuffix: Azure Cognitive Services
description: Jak používat metodu analyzovat v rozhraní API pro jazykovou analýzu k analýze určité vstupy přirozeného jazyka.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 2b02b048719dd7707db7e97df3641a314b512177
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861676"
---
# <a name="analyze-method"></a>Analýza – metoda

> [!IMPORTANT]
> Dne 9. srpna 2018 došlo k vyřazení jazykové analýzy ve verzi Preview z provozu. Ke zpracování a analýze textu doporučujeme používat [moduly analýzy textu služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

**Analyzovat** rozhraní REST API slouží k analýze vstup dané přirozeného jazyka.
Která může zahrnovat jenom hledání [věty a tokeny](Sentences-and-Tokens.md) v rámci tento vstup, hledání [značek částí řeči](POS-tagging.md), nebo hledání [constitutency stromu](Constituency-Parsing.md).
Můžete určit, jaké výsledky chcete výběrem příslušné analyzátory.
Seznam všech dostupných analyzátory, podívejte se na  **[analyzátory](AnalyzersMethod.md)**.

Všimněte si, že je třeba zadat jazyk vstupního řetězce.

**Koncový bod REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parametry žádosti

Název | Type | Povinné | Popis
-----|-------|----------|------------
**Jazyk**    | řetězec | Ano | Dvě písmena kód ISO jazyka použitého pro analýzu. Angličtina je například "en".
**analyzerIds** | seznam řetězců | Ano | Seznam identifikátorů GUID analyzátory použít. Další informace v dokumentaci analyzátory.
**Text**        | řetězec | Ano | Vstup nezpracovaných dat má být analyzován. To může být třeba slovo nebo frázi, úplné věty, nebo úplné odstavce nebo discourse krátký řetězec.

## <a name="response-json"></a>Odpověď (JSON)

Pole analýzy výstupy, jeden pro každý atribut v požadavku.

Výsledky budou vypadat takto:

Název | Typ | Popis
-----|------|--------------
analyzerId | řetězec | Analyzátor zadaný identifikátor GUID
výsledek | objekt | výsledek analyzátoru

Všimněte si, že typ výsledku závisí na typu vstupu analyzátor.

### <a name="tokens-response-json"></a>Tokeny odpovědí (JSON)

Název | Typ | Popis
-----|------|-------------
výsledek | seznam objektů větu | hranice věty identifikované v textu |
výsledek [x]. Posun | int | počáteční odsazení znaku každé věty. |
výsledek [x]. Délka | int | Délka ve znacích jednotlivé věty |
result[x].Tokens | seznam tokenů objektů | Token hranice identifikované ve větě |
výsledek [x]. Tokeny [y]. Posun | int | počáteční odsazení znaku tokenu |
výsledek [x]. Tokeny [y]. Délka | int | Délka ve znacích tokenu |
výsledek [x]. Tokeny [y]. RawToken | řetězec | znaky uvnitř tohoto tokenu, před normalizace |
výsledek [x]. Tokeny [y]. NormalizedToken | řetězec | normalizovaná forma znaku, bezpečný pro použití v [strom analýzy](Constituency-Parsing.md); například znak levou (otevírací) ' (' stane - LRB – |

Příklad vstupu: "Toto je test. Dobrý den. "
Příklad odpovědi JSON:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Odpověď značky POS (JSON)

Výsledkem je seznam seznamů řetězců.
Pro jednotlivé věty je seznam značky POS jedna značka POS pro každý token.
K vyhledání tokenu odpovídají jednotlivé značky POS, budete chtít požádat o objekt Tokenizace.

### <a name="constituency-tree-response-json"></a>Složková stromu odpovědi (JSON)

Výsledkem je seznam řetězců, jeden analýzy strom pro jednotlivé věty nalezených ve vstupu.
Stromů analýzy jsou reprezentovány ve formě v závorkách.

## <a name="example"></a>Příklad:

`POST /analyze`

Text žádosti: Datová část JSON
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Odpověď: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
