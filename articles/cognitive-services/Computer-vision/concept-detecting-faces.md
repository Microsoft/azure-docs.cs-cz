---
title: Detekce tváře – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s funkcí detekce obličeje rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3675acf59c6889dd00c8f26089f509bbcd3c724e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945248"
---
# <a name="face-detection-with-computer-vision"></a>Rozpoznávání tváře pomocí Počítačové zpracování obrazu

Počítačové zpracování obrazu dokáže detekovat lidské obličeje v rámci obrázku a generovat věk, pohlaví a obdélník pro každou zjištěnou plochu. 

> [!NOTE]
> Tuto funkci nabízí i služba Azure [Face](/azure/cognitive-services/face/) . Viz Tato alternativa pro podrobnější analýzu obličeje, včetně identifikace obličeje a detekce pozice. 

## <a name="face-detection-examples"></a>Příklady detekce obličeje

Následující příklad ukazuje odpověď JSON vrácenou Počítačové zpracování obrazu pro obrázek obsahující jednu lidskou plochu.

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

Následující příklad ukazuje odpověď JSON vrácenou pro obrázek obsahující více lidských plošek.

![Fotografická analýza rodiny](./Images/family_photo_face.png)

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

Další informace o tom, jak používat funkci detekce obličeje, najdete v referenční dokumentaci k [analýze obrázku](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
