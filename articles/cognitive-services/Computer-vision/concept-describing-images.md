---
title: Popisy obrázků – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkcí Popis obrázku rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945267"
---
# <a name="describe-images-with-human-readable-language"></a>Popište Image pomocí uživatelsky čitelného jazyka.

Počítačové zpracování obrazu může analyzovat obrázek a generovat větu umožňující čtení, která popisuje jeho obsah. Algoritmus ve skutečnosti vrátí několik popisů založených na různých vizuálních funkcích a každý popis je dán skóre spolehlivosti. Konečný výstup je seznam popisů seřazených od nejvyšších po nejnižší spolehlivost.

## <a name="image-description-example"></a>Příklad popisu obrázku

Následující odpověď JSON ukazuje, co Počítačové zpracování obrazu vrátí, když popisují příklad obrázku na základě jeho vizuálních funkcí.

![Černý a bílý obrázek budov v Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Další kroky

Naučte se koncepty [označování imagí](concept-tagging-images.md) a [kategorizace imagí](concept-categorizing-images.md).
