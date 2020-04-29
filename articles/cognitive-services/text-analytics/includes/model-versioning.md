---
title: Správa verzí modelů
titleSuffix: Azure Cognitive Services
description: Zadat verze modelu v koncových bodech V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77089086"
---
Verze 3 rozhraní API pro analýzu textu umožňuje zvolit verzi modelu, která je pro vaše data nejaktuálnější. Pomocí volitelného `model-version` parametru vyberte verzi modelu, která je pro vaše požadavky požadovaná. Pokud tento parametr není zadán `latest`, bude rozhraní API výchozím nastavením nejnovější stabilní verze. I když můžete použít nejnovější verzi modelu v jakékoli žádosti, v každé verzi se aktualizují jenom některé funkce. Následující tabulka popisuje, které funkce byly v každé verzi modelu aktualizované:

| Verze modelu           | Aktualizované funkce         | Nejnovější verze pro:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Rozpoznávání entit                      | Rozpoznávání entit                      |
| `2019-10-01`            | Rozpoznávání entit, analýza mínění  | Detekce jazyka, extrakce klíčových frází, analýza mínění|


Každá odpověď z koncových bodů V3 obsahuje `model-version` pole určující verzi modelu, která byla použita.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Podrobnosti o aktualizacích pro tyto verze modelu najdete v tématu [co je nového](../whats-new.md) .
