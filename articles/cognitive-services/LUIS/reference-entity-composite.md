---
title: Typ složené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Složená entita je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celé entity.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563235"
---
# <a name="composite-entity"></a>Složený entity 

Složená entita je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celé entity. 

**Tato entita je vhodná, když jsou data:**

* Se vztahují k sobě navzájem. 
* Se k sobě v kontextu promluvy navzájem vztahují.
* Využijte celou řadu typů entit.
* Musí být seskupena a zpracována klientskou aplikací jako jednotka informací.
* Máte celou řadu uživatelských projevy, která vyžadují strojové učení.

![Složený entity](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Ukázkový soubor JSON

Vezměte v úvahu složenou entitu `number` předem `Location::ToLocation` sestavenou a s následujícím utterance:

`book 2 tickets to paris`

Všimněte si, že `2`, je číslo, a `paris`, slova mezi nimi, které nejsou součástí žádné entity, které mají ToLocation. Zelená podtržení, používané s popiskem utterance v [LUIS](luis-reference-regions.md) webu, označuje složený entity.

![Složený Entity](./media/luis-concept-data-extraction/composite-entity.png)

Složený entity jsou vráceny v `compositeEntities` pole a všechny entity v rámci složeného jsou vráceny také v `entities` pole:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Předem připravených entit – počet|"builtin.number"|"2"|
|Předem sestavená entita – GeographyV2|"Location::ToLocation"|"Paříž"|

## <a name="next-steps"></a>Další postup

V tomto [kurzu](luis-tutorial-composite-entity.md)přidejte složenou **entitu** , která bude seskupit extrahovaná data různých typů do jedné obsahující entity. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.
