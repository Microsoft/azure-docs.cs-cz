---
title: Rozpoznání tváře - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkce rozpoznávání tváře z rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1056b8be113d56342aea8f83d5325737f7ecb93b
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308447"
---
# <a name="face-detection-with-computer-vision"></a>Rozpoznávání tváře se pro počítačové zpracování obrazu

Počítačové zpracování obrazu můžete detekovat lidské tváře v obrázku a generovat věk, pohlaví a rámeček pro každé zjištěné rozpoznávání tváře. 

> [!NOTE]
> Tato funkce také nabízí Azure [pro rozpoznávání tváře](/azure/cognitive-services/face/) služby. Zobrazit tuto alternativu pro podrobnější analýzu, včetně identifikace tváře pro rozpoznávání tváře a představovat zjišťování. 

## <a name="face-detection-examples"></a>Příklady rozpoznávání tváře

Následující příklad ukazuje odpověď JSON vrátil pro počítačové zpracování obrazu pro image obsahující jeden lidské tváře.

![Vizuální analýza ženy na střeše](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Následující příklad ukazuje odpověď JSON pro image obsahující několik lidských tváří.

![Pro zpracování obrazu analyzovat rodinných fotografií pro rozpoznávání tváře](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Další postup

Zobrazit [analyzovat Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) referenční dokumentaci se dozvíte další informace o tom, jak používat funkce rozpoznávání tváře.
