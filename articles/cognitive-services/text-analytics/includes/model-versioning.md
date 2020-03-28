---
title: Správa verzí modelu
titleSuffix: Azure Cognitive Services
description: Určení verzí modelu v koncových bodech V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77089086"
---
Verze 3 rozhraní API pro analýzu textu umožňuje zvolit verzi modelu, která je pro vaše data nejaktuálnější. Pomocí volitelného `model-version` parametru vyberte verzi modelu, která je požadovaná pro vaše požadavky. Pokud tento parametr není zadán rozhraní `latest`API bude výchozí na , nejnovější stabilní verze. I když můžete použít nejnovější verzi modelu v libovolném požadavku, pouze některé funkce jsou aktualizovány v každé verzi. Následující tabulka popisuje, které funkce byly aktualizovány v každé verzi modelu:

| Verze modelu           | Aktualizované funkce         | Nejnovější verze pro:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Rozpoznávání entit                      | Rozpoznávání entit                      |
| `2019-10-01`            | Rozpoznávání entit, analýza mínění  | Detekce jazyka, extrakce klíčových frází, analýza mínění|


Každá odpověď z koncových bodů `model-version` verze 3 obsahuje pole určující použitou verzi modelu.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Podrobnosti o aktualizacích těchto verzí modelu najdete v [tématu Co je nového.](../whats-new.md)
