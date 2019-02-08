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
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7919a84ffe948c9b6a8f68fc1372f1976c09bc79
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864277"
---
# <a name="describe-images-with-human-readable-language"></a>Popis bitové kopie v lidsky čitelném jazyce

Počítačové zpracování obrazu algoritmy analyzovat obsah v obrázku. Tato analýza tvoří základ popisu zobrazeného v čitelném jazyce a v celých větách. Popis shrnuje zjištěný obsah obrázku. Počítačové zpracování obrazu algoritmy generovat různé popisy podle vizuální funkce identifikované v bitové kopii. Každý popis je vyhodnocen a generované skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší.

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
