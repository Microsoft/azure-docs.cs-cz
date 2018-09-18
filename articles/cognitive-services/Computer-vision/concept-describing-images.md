---
title: Popis Image - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s popisem obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: a65e3ea2fb28ca8a2250fb3e39860eb5e08c18f4
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981976"
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

Najdete příklad, který používá tuto technologii ke generování popisků obrazu robota [tady](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [kategorizace obrázků](concept-categorizing-images.md).