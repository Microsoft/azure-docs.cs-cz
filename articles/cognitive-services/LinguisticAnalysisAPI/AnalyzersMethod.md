---
title: Metoda Analyzers - Lingistic rozhraní API pro analýzu
titlesuffix: Azure Cognitive Services
description: Analyzátory rozhraní REST API poskytuje seznam analyzátory, které jsou aktuálně podporuje rozhraní API pro jazykovou analýzu.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 8bf13bffe763b88e95da94f885e30d271e36da42
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221678"
---
# <a name="analyzers-method"></a>Metoda analyzers

> [!IMPORTANT]
> Dne 9. srpna 2018 došlo k vyřazení jazykové analýzy ve verzi Preview z provozu. Ke zpracování a analýze textu doporučujeme používat [moduly analýzy textu služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

**Analyzátory** rozhraní REST API poskytuje seznam aktuálně podporované službou analyzátory.
Odpověď obsahuje jejich [názvy](Analyzer-Names.md) a jazyky podporované jednotlivými (například "en" pro angličtinu).

## <a name="request-parameters"></a>Parametry žádosti
Žádný

<br>

## <a name="response-parameters"></a>Parametry odpovědi
Name | Typ | Popis
-----|------|--------------
Jazyky | seznam řetězců | seznam kódů dvě písmeno ISO jazyků, pro které je možné tento analyzátor.
id   | řetězec | Jedinečné ID pro tento analyzátor
Typ | řetězec | široký typ analyzátoru zde
Specifikace | řetězec | Název specifikace použité pro tento analyzátor
Implementace | řetězec | Popis modelu a/nebo algoritmus za tento analyzátor

<br>
## <a name="example"></a>Příklad:
ZÍSKAT /analyzers

Odpověď: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
