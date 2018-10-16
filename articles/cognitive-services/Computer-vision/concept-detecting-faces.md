---
title: Rozpoznání tváře - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související se zjišťováním tváří pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: bf358d1e8f60f989ced8db966bbf0a5179fab25b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342309"
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