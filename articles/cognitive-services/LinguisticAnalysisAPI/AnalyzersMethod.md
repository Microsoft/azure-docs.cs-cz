---
title: Analyzátory metoda v rozhraní API Lingistic Analysis | Microsoft Docs
description: Analyzátory REST API poskytuje seznam analyzátory, které jsou aktuálně podporovány službou v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342391"
---
# <a name="analyzers-method"></a>Analyzátory – metoda

**Analyzátorů** obsahuje seznam analyzátorů aktuálně nepodporuje službu rozhraní REST API.
Odpověď obsahuje jejich [názvy](Analyzer-Names.md) a jazyky podporované každou (např. "en" pro angličtinu).

## <a name="request-parameters"></a>Parametry žádosti
Žádný

<br>

## <a name="response-parameters"></a>Parametry odpovědi
Název | Typ | Popis
-----|------|--------------
jazyky | seznam řetězců | seznam kódů dvě písmeno ISO jazyků, pro které je možné tento analyzátor.
id   | řetězec | Jedinečné ID pro tento analyzátor
Typ | řetězec | široká typ analyzátor sem
Specifikace | řetězec | Název specifikace použité pro tento analyzátor
implementace | řetězec | Popis modelu a/nebo algoritmus za tento analyzátor

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
