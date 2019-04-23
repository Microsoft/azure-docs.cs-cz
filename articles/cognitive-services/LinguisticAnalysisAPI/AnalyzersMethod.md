---
title: Metoda Analyzers - Lingistic rozhraní API pro analýzu
titlesuffix: Azure Cognitive Services
description: Analyzátory rozhraní REST API poskytuje seznam analyzátory, které jsou aktuálně podporuje rozhraní API pro jazykovou analýzu.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404707"
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
id   | string | Jedinečné ID pro tento analyzátor
Typ | string | široký typ analyzátoru zde
Specifikace | string | Název specifikace použité pro tento analyzátor
Implementace | string | Popis modelu a/nebo algoritmus za tento analyzátor

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
