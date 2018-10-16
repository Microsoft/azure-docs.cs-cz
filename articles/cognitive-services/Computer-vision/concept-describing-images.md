---
title: Popis Image - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s popisem obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 423d1be57bc800108a08a81b72587ca2711bbc3d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342411"
---
# <a name="describing-images"></a>Popisování obrázků

Počítačové zpracování obrazu algoritmy analyzovat obsah v obrázku. Tato analýza tvoří základ pro "popisu" zobrazí jako čitelný jazyk v úplných větách. Popis shrnuje, co se nachází na obrázku. Počítačové zpracování obrazu algoritmy generovat různé popisy podle vizuální funkce identifikované v bitové kopii. Každý popis je vyhodnocen a generované skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší.

## <a name="image-description-example"></a>Příklad popis obrázku

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při popisu příklad image založenou na jeho vizuální funkce.

![B & vá budov](./Images/bw_buildings.png)

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