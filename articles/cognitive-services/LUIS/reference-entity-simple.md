---
title: Jednoduchý typ entity – LUIS
titleSuffix: Azure Cognitive Services
description: Jednoduchá entita je obecná entita, která popisuje jeden koncept a je získána z kontextu zjištěného počítačem. Vzhledem k tomu, že jednoduché entity jsou obecně názvy, například názvy společností, názvy produktů nebo jiné kategorie názvů, přidejte seznam frází při použití jednoduché entity k posílení signálu používaných názvů.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c1514b6cd512924a162a524d11e888055fa06514
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563197"
---
# <a name="simple-entity"></a>Jednoduchá entita 

Jednoduchá entita je obecná entita, která popisuje jeden koncept a je získána z kontextu zjištěného počítačem. Vzhledem k tomu, že jednoduché entity jsou obecně názvy, například názvy společností, názvy produktů nebo jiné kategorie názvů, přidejte [seznam frází](luis-concept-feature.md) při použití jednoduché entity k posílení signálu používaných názvů. 

**Entita je vhodná v případě, že:**

* Data nejsou konzistentně naformátovaná, ale označují stejnou věc. 

![jednoduché entity](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Ukázkový soubor JSON

`Bob Jones wants 3 meatball pho`

V předchozím utterance `Bob Jones` je označena jako jednoduchý `Customer` entity.

Data vrácená z koncového bodu obsahuje název entity, zjištěný text z utterance, umístění zjištěných textu a skóre:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Jednoduché Entity|`Customer`|`bob jones`|

## <a name="next-steps"></a>Další kroky

V tomto [kurzu](luis-quickstart-primary-and-secondary-data.md)extrahujete pomocí **jednoduché entity**data z pracovní úlohy, která se naučila z utterance. Chcete-li zvýšit přesnost extrakce, přidejte [seznam frází](luis-concept-feature.md) , které jsou specifické pro jednoduchou entitu.