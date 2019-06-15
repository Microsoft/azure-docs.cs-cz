---
title: Popis Image - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkcí Popis bitové kopie z rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368373"
---
# <a name="describe-images-with-human-readable-language"></a>Popis bitové kopie v lidsky čitelném jazyce

Počítačové zpracování obrazu můžete analyzovat bitovou kopii a generovat čitelné věta popisující její obsah. Algoritmus ve skutečnosti retruns několika popisů na základě různých vizuální funkce a každý popis dostane skóre spolehlivosti. Poslední výstupem je seznam popisů seřazené od nejvyšší k nejnižší spolehlivosti.

## <a name="image-description-example"></a>Příklad popis obrázku

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při popisu příklad image založenou na jeho vizuální funkce.

![Černá a bílá obrázek budovy v Manhattan](./Images/bw_buildings.png)

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

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [kategorizace obrázků](concept-categorizing-images.md).
