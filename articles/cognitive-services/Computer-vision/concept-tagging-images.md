---
title: Použití značek obsahu do Image - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Další koncepty související s imagí označení funkce rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f5013c83f3575e7e1fbaa6dff614e4679abf09b6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882229"
---
# <a name="applying-content-tags-to-images"></a>Nastavení obsahu klíčových slov do bitové kopie

Počítačové zpracování obrazu vrací značky na základě tisíce rozpoznatelných objektů, živých bytostí, krajin a akcí. Pokud jsou značky nejasné nebo nejsou obecně známé, nabídne API v odpovědi nápovědu, která objasní význam značky v kontextu známého prostředí. Značky nejsou uspořádané podle taxonomie a neexistují hierarchie dědění. Shromážděné značky obsahu tvoří základ „popisu“ obrázku, který se zobrazí v čitelném jazyce formátovaném do celých vět. V tuto chvíli je jediným podporovaným jazykem používaným k popisu obrázku angličtina.

Po nahrání obrázku nebo zadáte adresu URL obrázku, výstupní algoritmy pro počítačové zpracování obrazu značky na základě objektů, živých bytostí a akcí identifikované v bitové kopii. Značky se neomezují na hlavní předmět, například postavu v popředí, ale zahrnují také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.

## <a name="image-tagging-example"></a>Příklad označení Image

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při označování vizuální funkce v na obrázku.

![Modré house a front-yard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [kategorizace obrázků](concept-categorizing-images.md) a [imagí s popisem](concept-describing-images.md).
