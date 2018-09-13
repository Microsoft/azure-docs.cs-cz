---
title: Rozpoznávání tváří
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Koncepty související se zjišťováním tváře pro počítačové zpracování obrazu ve službě Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: abf0dc0ea80685130f6566ae962d2bed124e0924
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725330"
---
# <a name="detecting-faces"></a>Rozpoznávání tváří

Počítačové zpracování obrazu detekuje lidské tváře v obrázku a generuje věk, pohlaví a rámeček pro každé zjištěné rozpoznávání tváře. Počítačové zpracování obrazu poskytuje podmnožinu funkcí, které najdete ve službě [Rozpoznávání tváře](/azure/cognitive-services/face/), kterou můžete využít k podrobnější analýze, například identifikaci tváří a rozpoznávání pózy.  

## <a name="face-detection-examples"></a>Příklady rozpoznávání tváře

První příklad ukazuje odpověď JSON vrátil pro počítačové zpracování obrazu pro image obsahující jeden lidské tváře.

![Pro zpracování obrazu analyzovat žena stříška pro rozpoznávání tváře](./Images/woman_roof_face.png)

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

Druhý příklad ukazuje odpověď JSON pro image obsahující několik lidských tváří.

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

Seznamte se s koncepty [zjišťování obsahu specifického pro doménu](concept-detecting-domain-content.md).