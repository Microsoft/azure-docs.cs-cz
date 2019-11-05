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
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488635"
---
Verze 3 rozhraní API pro analýzu textu umožňuje zvolit model Analýza textu použitý pro vaše data. Použijte parametr Optional `model-version` k výběru verze modelu v rámci vašich požadavků. Pokud tento parametr není zadán, rozhraní API bude ve výchozím nastavení `latest`, což je nejnovější stabilní verze modelu.

Dostupné verze modelu:
* `2019-10-01` (`latest`)

Každá odpověď z koncových bodů V3 obsahuje pole `model-version` určující verzi modelu, která byla použita.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
