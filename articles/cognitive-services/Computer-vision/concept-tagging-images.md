---
title: Označování imagí - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s označení obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 159a1b9404f6ce635dcc8bb6007e52b7346ef98d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982960"
---
# <a name="tagging-images"></a>Označování obrázků značkami

Počítačové zpracování obrazu vrací značky na základě na více než 2 000 rozpoznatelných objektů, živých bytostí, krajin a akcí. Při značky jsou nejednoznačný nebo není, rozhraní API poskytne "pomocné parametry" pro objasnění významu značky v kontextu známé nastavení. Značky nejsou uspořádaná jako taxonomie a neexistuje žádná hierarchie dědičnosti. Kolekce obsahu značek tvoří základ pro bitovou kopii "popisu" zobrazí jako lidského čitelné jazyk, ve formátu v úplných větách. Všimněte si, že v tomto okamžiku angličtina je jediný podporovaný jazyk pro popis obrázku.

Po nahrání obrázku nebo zadáte adresu URL obrázku, výstupní algoritmy pro počítačové zpracování obrazu značky na základě objektů, živých bytostí a akcí identifikované v bitové kopii. Označování není omezena pouze na hlavní předmět, jako je osoba v popředí, ale také zahrnuje nastavení (vnitřních nebo venkovní), o nábytek, nástroje, zařízení, zvířata, příslušenství, miniaplikace atd.

## <a name="image-tagging-example"></a>Příklad označení Image

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při označování vizuální funkce v na obrázku.

![House_Yard](./Images/house_yard.png).

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