---
title: Analýza metoda v rozhraní API lingvistické Analysis | Microsoft Docs
description: Jak používat metodu analyzovat v lingvistické Analysis rozhraní API k analýze určité vstupy přirozeném jazyce.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342482"
---
# <a name="analyze-method"></a>Analýza – metoda

**Analyzovat** REST API slouží k analýze vstup dané přirozeného jazyka.
Které mohou zahrnovat jenom hledání [věty a tokeny](Sentences-and-Tokens.md) v rámci které vstup hledání [část řeči značky](POS-tagging.md), nebo při hledání [constitutency stromu](Constituency-Parsing.md).
Můžete zadat výsledky, které chcete výběrem příslušné analyzátorů.
Seznam všech dostupných analyzátorů, podívejte se na  **[analyzátorů](AnalyzersMethod.md)**.

Všimněte si, že budete muset zadat jazyk vstupní řetězec.

**Koncový bod REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parametry žádosti

Název | Typ | Požaduje se | Popis
-----|-------|----------|------------
**jazyk**    | řetězec | Ano | Dva písmeno kód jazyka ISO, který se má použít pro analýzu. Například "en" je angličtina.
**analyzerIds** | seznam řetězců | Ano | Seznam identifikátorů GUID analyzátorů použít. Najdete v dokumentaci analyzátorů Další informace.
**text**        | řetězec | Ano | Nezpracovaná vstupu má být analyzován. To může být například slovo nebo frázi, úplná věty, nebo úplné odstavce nebo discourse krátký řetězec.

<br>
## <a name="response-json"></a>Odpověď (JSON)
Pole výstupy analýzy, jednu pro každý atribut v požadavku.

Výsledky vypadat takto:

Název | Typ | Popis
-----|------|--------------
analyzerId | řetězec | Analyzátor zadaný identifikátor GUID
Výsledek | objekt | výsledek analyzátoru

Všimněte si, že typ výsledku závisí na typu vstupní analyzátor.

### <a name="tokens-response-json"></a>Tokeny odpovědi (JSON)

Název | Typ | Popis
-----|------|-------------
Výsledek | seznam objektů větu | hranice větu identifikovat v rámci textu |
výsledek [x]. Posunutí | celá čísla | počáteční odsazení znaku každý věty. |
výsledek [x]. Len | celá čísla | Délka ve znacích každý věty. |
výsledek [x]. Tokeny | seznam objektů tokenu | Token hranice ve větě určen |
výsledek [x]. Tokeny [y]. Posunutí | celá čísla | počáteční odsazení znaku tokenu |
výsledek [x]. Tokeny [y]. Len | celá čísla | Délka ve znacích tokenu |
výsledek [x]. Tokeny [y]. RawToken | řetězec | znaky uvnitř tohoto tokenu před normalizaci |
výsledek [x]. Tokeny [y]. NormalizedToken | řetězec | Normalizovaný formuláře znaku, bezpečné pro použití v [strom analýzy](Constituency-Parsing.md); pro instanci znakem otevřete závorky ' (' stane - LRB - |

Příklad vstup: ' Toto je test. Hello. "
Příklad odpověď JSON:
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


### <a name="pos-tags-response-json"></a>POS odpovědi značky (JSON)

Výsledkem je seznamů řetězců.
Pro každé větě je seznam značek POS jeden POS značky pro každý token.
Token odpovídající jednotlivé značky POS najdete budete chtít požádat o na Tokenizace objekt.

### <a name="constituency-tree-response-json"></a>Odpověď stromu volebním (JSON)

Výsledkem je, a seznam řetězců, strom analýzy jeden pro každé větě nalezených ve vstupu.
Stromy analýzy jsou reprezentované ve formuláři v závorkách.

<br>

## <a name="example"></a>Příklad:

`POST /analyze`

Textu žádosti: Datová část JSON
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

