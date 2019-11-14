---
title: Správa verzí modelů
titleSuffix: Azure Cognitive Services
description: Zadat verze modelu v koncových bodech V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021061"
---
Verze 3 rozhraní API pro analýzu textu umožňuje zvolit model Analýza textu použitý pro vaše data. Použijte parametr Optional `model-version` k výběru verze modelu v rámci vašich požadavků. Pokud tento parametr není zadán, rozhraní API bude ve výchozím nastavení `latest`, což je nejnovější stabilní verze modelu.

Dostupné verze modelu:
* `2019-10-01` (`latest`)

Každá odpověď z koncových bodů V3 obsahuje pole `model-version` určující verzi modelu, která byla použita.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
